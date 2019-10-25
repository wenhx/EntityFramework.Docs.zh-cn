---
title: 自定义迁移历史记录表-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 0db393ff3101564f8d8081d0a57b264c2c459df7
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812071"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="c0520-102">自定义迁移历史记录表</span><span class="sxs-lookup"><span data-stu-id="c0520-102">Custom Migrations History Table</span></span>

<span data-ttu-id="c0520-103">默认情况下，EF Core 通过在名为 `__EFMigrationsHistory`的表中记录哪些迁移已应用到数据库中。</span><span class="sxs-lookup"><span data-stu-id="c0520-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="c0520-104">由于各种原因，你可能需要自定义此表以更好地满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="c0520-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c0520-105">如果在应用迁移*后*自定义迁移历史记录表，则需要负责更新数据库中的现有表。</span><span class="sxs-lookup"><span data-stu-id="c0520-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="c0520-106">架构和表名称</span><span class="sxs-lookup"><span data-stu-id="c0520-106">Schema and table name</span></span>

<span data-ttu-id="c0520-107">你可以使用 `OnConfiguring()` 中的 `MigrationsHistoryTable()` 方法（或 ASP.NET Core 上的 `ConfigureServices()`）更改架构和表名。</span><span class="sxs-lookup"><span data-stu-id="c0520-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="c0520-108">下面是一个示例，说明如何使用 SQL Server EF Core 提供程序。</span><span class="sxs-lookup"><span data-stu-id="c0520-108">Here is an example using the SQL Server EF Core provider.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

## <a name="other-changes"></a><span data-ttu-id="c0520-109">其他更改</span><span class="sxs-lookup"><span data-stu-id="c0520-109">Other changes</span></span>

<span data-ttu-id="c0520-110">若要配置表的其他方面，请重写并替换特定于提供程序的 `IHistoryRepository` 服务。</span><span class="sxs-lookup"><span data-stu-id="c0520-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="c0520-111">下面是将 MigrationId 列名称更改为 SQL Server 上的*Id*的示例。</span><span class="sxs-lookup"><span data-stu-id="c0520-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> <span data-ttu-id="c0520-112">`SqlServerHistoryRepository` 位于内部命名空间内部，并且在将来的版本中可能会更改。</span><span class="sxs-lookup"><span data-stu-id="c0520-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

``` csharp
class MyHistoryRepository : SqlServerHistoryRepository
{
    public MyHistoryRepository(HistoryRepositoryDependencies dependencies)
        : base(dependencies)
    {
    }

    protected override void ConfigureTable(EntityTypeBuilder<HistoryRow> history)
    {
        base.ConfigureTable(history);

        history.Property(h => h.MigrationId).HasColumnName("Id");
    }
}
```
