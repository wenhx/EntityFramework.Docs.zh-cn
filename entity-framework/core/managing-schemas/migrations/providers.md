---
title: 多个提供程序的 EF Core 的迁移
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: efe95893f7dbfc8e5c4775e86d58abb32eee3c83
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414253"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="60162-102">迁移多个提供程序</span><span class="sxs-lookup"><span data-stu-id="60162-102">Migrations with Multiple Providers</span></span>

<span data-ttu-id="60162-103">[EF Core 工具][1]仅基架活动提供程序的迁移。</span><span class="sxs-lookup"><span data-stu-id="60162-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="60162-104">但有时，您可能想要将多个提供程序（例如 Microsoft SQL Server 和 SQLite）用于 DbContext。</span><span class="sxs-lookup"><span data-stu-id="60162-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="60162-105">可以通过两种方式来处理迁移。</span><span class="sxs-lookup"><span data-stu-id="60162-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="60162-106">您可以维护两组迁移-每个提供程序一个，或将它们合并到可以同时使用的单个集。</span><span class="sxs-lookup"><span data-stu-id="60162-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="60162-107">两个迁移集</span><span class="sxs-lookup"><span data-stu-id="60162-107">Two migration sets</span></span>

<span data-ttu-id="60162-108">第一种方法是为每个模型更改生成两个迁移。</span><span class="sxs-lookup"><span data-stu-id="60162-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="60162-109">实现此目的的一种方法是将每个迁移集放[在单独的程序集中][2]，并在添加两个迁移之间手动切换活动提供程序（和迁移程序集）。</span><span class="sxs-lookup"><span data-stu-id="60162-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="60162-110">更轻松地使用工具的另一种方法是创建一个从 DbContext 派生的新类型并重写活动提供程序。</span><span class="sxs-lookup"><span data-stu-id="60162-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="60162-111">此类型在设计时用于添加或应用迁移。</span><span class="sxs-lookup"><span data-stu-id="60162-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="60162-112">由于每个迁移集使用自己的 DbContext 类型，因此此方法不需要使用单独的迁移程序集。</span><span class="sxs-lookup"><span data-stu-id="60162-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="60162-113">添加新迁移时，指定上下文类型。</span><span class="sxs-lookup"><span data-stu-id="60162-113">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="60162-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="60162-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="60162-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60162-115">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="60162-116">无需指定输出目录即可进行后续迁移，因为它们是以同级方式创建的。</span><span class="sxs-lookup"><span data-stu-id="60162-116">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="60162-117">一个迁移集</span><span class="sxs-lookup"><span data-stu-id="60162-117">One migration set</span></span>

<span data-ttu-id="60162-118">如果不喜欢两组迁移，可以手动将它们合并为可应用于这两个提供程序的单个集。</span><span class="sxs-lookup"><span data-stu-id="60162-118">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="60162-119">批注可以共存，因为提供程序会忽略它不理解的任何批注。</span><span class="sxs-lookup"><span data-stu-id="60162-119">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="60162-120">例如，使用 Microsoft SQL Server 和 SQLite 的主键列可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="60162-120">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="60162-121">如果只能在一个提供程序上应用操作（或在提供程序之间以不同方式进行操作），请使用 `ActiveProvider` 属性告诉哪个提供程序处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="60162-121">If operations can only be applied on one provider (or they're differently between providers), use the `ActiveProvider` property to tell which provider is active.</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
