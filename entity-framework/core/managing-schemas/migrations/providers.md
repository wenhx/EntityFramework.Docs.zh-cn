---
title: 多个提供程序的迁移-EF Core
description: 在将多个数据库提供程序定位到 Entity Framework Core 时使用迁移来管理数据库架构
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: df38af6ac700a530894b98e1f29bbe804831bad5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619187"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="b2e31-103">迁移多个提供程序</span><span class="sxs-lookup"><span data-stu-id="b2e31-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="b2e31-104">[EF Core 工具][1]仅基架活动提供程序的迁移。</span><span class="sxs-lookup"><span data-stu-id="b2e31-104">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="b2e31-105">但有时，您可能希望使用多个提供程序 (例如 Microsoft SQL Server 和 SQLite) DbContext。</span><span class="sxs-lookup"><span data-stu-id="b2e31-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="b2e31-106">可以通过两种方式来处理迁移。</span><span class="sxs-lookup"><span data-stu-id="b2e31-106">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="b2e31-107">您可以维护两组迁移-每个提供程序一个，或将它们合并到可以同时使用的单个集。</span><span class="sxs-lookup"><span data-stu-id="b2e31-107">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="b2e31-108">两个迁移集</span><span class="sxs-lookup"><span data-stu-id="b2e31-108">Two migration sets</span></span>

<span data-ttu-id="b2e31-109">第一种方法是为每个模型更改生成两个迁移。</span><span class="sxs-lookup"><span data-stu-id="b2e31-109">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="b2e31-110">实现此目的的一种方法是将每个迁移集放 [在单独的程序集中][2] ，并在添加两个迁移之间手动切换活动提供程序 (和迁移程序集) 。</span><span class="sxs-lookup"><span data-stu-id="b2e31-110">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="b2e31-111">更轻松地使用工具的另一种方法是创建一个从 DbContext 派生的新类型并重写活动提供程序。</span><span class="sxs-lookup"><span data-stu-id="b2e31-111">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="b2e31-112">此类型在设计时用于添加或应用迁移。</span><span class="sxs-lookup"><span data-stu-id="b2e31-112">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="b2e31-113">由于每个迁移集使用自己的 DbContext 类型，因此此方法不需要使用单独的迁移程序集。</span><span class="sxs-lookup"><span data-stu-id="b2e31-113">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="b2e31-114">添加新迁移时，指定上下文类型。</span><span class="sxs-lookup"><span data-stu-id="b2e31-114">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b2e31-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b2e31-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="b2e31-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2e31-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="b2e31-117">无需指定输出目录即可进行后续迁移，因为它们是以同级方式创建的。</span><span class="sxs-lookup"><span data-stu-id="b2e31-117">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="b2e31-118">一个迁移集</span><span class="sxs-lookup"><span data-stu-id="b2e31-118">One migration set</span></span>

<span data-ttu-id="b2e31-119">如果不喜欢两组迁移，可以手动将它们合并为可应用于这两个提供程序的单个集。</span><span class="sxs-lookup"><span data-stu-id="b2e31-119">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="b2e31-120">批注可以共存，因为提供程序会忽略它不理解的任何批注。</span><span class="sxs-lookup"><span data-stu-id="b2e31-120">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="b2e31-121">例如，使用 Microsoft SQL Server 和 SQLite 的主键列可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="b2e31-121">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="b2e31-122">如果只能对一个提供程序应用操作，或它们在提供程序之间不同，请使用 `ActiveProvider` 属性来确定哪个提供程序处于活动状态：</span><span class="sxs-lookup"><span data-stu-id="b2e31-122">If operations can be applied only for one provider, or they're different between providers, use the `ActiveProvider` property to determine which provider is active:</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
