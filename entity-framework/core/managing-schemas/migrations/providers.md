---
title: 多个提供程序的迁移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: c9b1a2563ef548e592374f90a6242b0bd851bc98
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811957"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="8848f-102">迁移多个提供程序</span><span class="sxs-lookup"><span data-stu-id="8848f-102">Migrations with Multiple Providers</span></span>

<span data-ttu-id="8848f-103">[EF Core 工具][1]仅基架活动提供程序的迁移。</span><span class="sxs-lookup"><span data-stu-id="8848f-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="8848f-104">但有时，您可能想要将多个提供程序（例如 Microsoft SQL Server 和 SQLite）用于 DbContext。</span><span class="sxs-lookup"><span data-stu-id="8848f-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="8848f-105">可以通过两种方式来处理迁移。</span><span class="sxs-lookup"><span data-stu-id="8848f-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="8848f-106">您可以维护两组迁移-每个提供程序一个，或将它们合并到可以同时使用的单个集。</span><span class="sxs-lookup"><span data-stu-id="8848f-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="8848f-107">两个迁移集</span><span class="sxs-lookup"><span data-stu-id="8848f-107">Two migration sets</span></span>

<span data-ttu-id="8848f-108">第一种方法是为每个模型更改生成两个迁移。</span><span class="sxs-lookup"><span data-stu-id="8848f-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="8848f-109">实现此目的的一种方法是将每个迁移集放[在单独的程序集中][2]，并在添加两个迁移之间手动切换活动提供程序（和迁移程序集）。</span><span class="sxs-lookup"><span data-stu-id="8848f-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="8848f-110">更轻松地使用工具的另一种方法是创建一个从 DbContext 派生的新类型并重写活动提供程序。</span><span class="sxs-lookup"><span data-stu-id="8848f-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="8848f-111">此类型在设计时用于添加或应用迁移。</span><span class="sxs-lookup"><span data-stu-id="8848f-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="8848f-112">由于每个迁移集使用自己的 DbContext 类型，因此此方法不需要使用单独的迁移程序集。</span><span class="sxs-lookup"><span data-stu-id="8848f-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="8848f-113">添加新迁移时，指定上下文类型。</span><span class="sxs-lookup"><span data-stu-id="8848f-113">When adding new migration, specify the context types.</span></span>

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> <span data-ttu-id="8848f-114">无需指定输出目录即可进行后续迁移，因为它们是以同级方式创建的。</span><span class="sxs-lookup"><span data-stu-id="8848f-114">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="8848f-115">一个迁移集</span><span class="sxs-lookup"><span data-stu-id="8848f-115">One migration set</span></span>

<span data-ttu-id="8848f-116">如果不喜欢两组迁移，可以手动将它们合并为可应用于这两个提供程序的单个集。</span><span class="sxs-lookup"><span data-stu-id="8848f-116">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="8848f-117">批注可以共存，因为提供程序会忽略它不理解的任何批注。</span><span class="sxs-lookup"><span data-stu-id="8848f-117">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="8848f-118">例如，使用 Microsoft SQL Server 和 SQLite 的主键列可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="8848f-118">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="8848f-119">如果只能在一个提供程序上应用操作（或在提供程序之间以不同方式进行操作），请使用 `ActiveProvider` 属性告诉哪个提供程序处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8848f-119">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
