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
# <a name="custom-migrations-history-table"></a>自定义迁移历史记录表

默认情况下，EF Core 通过在名为 `__EFMigrationsHistory`的表中记录哪些迁移已应用到数据库中。 由于各种原因，你可能需要自定义此表以更好地满足你的需求。

> [!IMPORTANT]
> 如果在应用迁移*后*自定义迁移历史记录表，则需要负责更新数据库中的现有表。

## <a name="schema-and-table-name"></a>架构和表名称

你可以使用 `OnConfiguring()` 中的 `MigrationsHistoryTable()` 方法（或 ASP.NET Core 上的 `ConfigureServices()`）更改架构和表名。 下面是一个示例，说明如何使用 SQL Server EF Core 提供程序。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options.UseSqlServer(
        connectionString,
        x => x.MigrationsHistoryTable("__MyMigrationsHistory", "mySchema"));
```

## <a name="other-changes"></a>其他更改

若要配置表的其他方面，请重写并替换特定于提供程序的 `IHistoryRepository` 服务。 下面是将 MigrationId 列名称更改为 SQL Server 上的*Id*的示例。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IHistoryRepository, MyHistoryRepository>();
```

> [!WARNING]
> `SqlServerHistoryRepository` 位于内部命名空间内部，并且在将来的版本中可能会更改。

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
