---
title: 多个提供程序的迁移-EF Core
description: 在将多个数据库提供程序定位到 Entity Framework Core 时使用迁移来管理数据库架构
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429775"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="22220-103">迁移多个提供程序</span><span class="sxs-lookup"><span data-stu-id="22220-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="22220-104">[EF Core 工具](xref:core/cli/index)仅基架活动提供程序的迁移。</span><span class="sxs-lookup"><span data-stu-id="22220-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="22220-105">但有时，您可能希望使用多个提供程序 (例如 Microsoft SQL Server 和 SQLite) DbContext。</span><span class="sxs-lookup"><span data-stu-id="22220-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="22220-106">通过维护多个迁移集来处理此情况-每个提供程序一个用于每个模型更改，并向每个集合添加一个迁移。</span><span class="sxs-lookup"><span data-stu-id="22220-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="22220-107">使用多种上下文类型</span><span class="sxs-lookup"><span data-stu-id="22220-107">Using multiple context types</span></span>

<span data-ttu-id="22220-108">创建多个迁移集的一种方法是为每个提供程序使用一个 DbContext 类型。</span><span class="sxs-lookup"><span data-stu-id="22220-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="22220-109">添加新迁移时指定上下文类型。</span><span class="sxs-lookup"><span data-stu-id="22220-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="22220-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="22220-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="22220-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22220-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="22220-112">无需指定输出目录即可进行后续迁移，因为它们是以同级方式创建的。</span><span class="sxs-lookup"><span data-stu-id="22220-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="22220-113">使用一个上下文类型</span><span class="sxs-lookup"><span data-stu-id="22220-113">Using one context type</span></span>

<span data-ttu-id="22220-114">还可以使用一种 DbContext 类型。</span><span class="sxs-lookup"><span data-stu-id="22220-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="22220-115">目前需要将迁移迁移到单独的程序集中。</span><span class="sxs-lookup"><span data-stu-id="22220-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="22220-116">有关设置项目的说明，请参阅 [使用单独的迁移项目](xref:core/managing-schemas/migrations/projects) 。</span><span class="sxs-lookup"><span data-stu-id="22220-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="22220-117">可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations)中查看此文章的示例。</span><span class="sxs-lookup"><span data-stu-id="22220-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="22220-118">从 EF Core 5.0 开始，可以从工具将参数传递到应用程序。</span><span class="sxs-lookup"><span data-stu-id="22220-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="22220-119">这可以实现更简单的工作流，使其在运行工具时无需对项目进行手动更改。</span><span class="sxs-lookup"><span data-stu-id="22220-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="22220-120">下面是一种在使用 [泛型宿主](/dotnet/core/extensions/generic-host)时良好运行的模式。</span><span class="sxs-lookup"><span data-stu-id="22220-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="22220-121">由于默认主机生成器从命令行参数读取配置，因此您可以在运行工具时指定提供程序。</span><span class="sxs-lookup"><span data-stu-id="22220-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="22220-122">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="22220-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="22220-123">该 `--` 令牌指示 `dotnet ef` 将后面的所有内容视为一个参数，而不会尝试将它们作为选项进行分析。</span><span class="sxs-lookup"><span data-stu-id="22220-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="22220-124">不使用的任何额外参数 `dotnet ef` 都将转发到应用程序。</span><span class="sxs-lookup"><span data-stu-id="22220-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="22220-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22220-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="22220-126">EF Core 5.0 中添加了指定应用的其他参数的功能。</span><span class="sxs-lookup"><span data-stu-id="22220-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="22220-127">如果使用的是较旧版本，请改用环境变量指定配置值。</span><span class="sxs-lookup"><span data-stu-id="22220-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
