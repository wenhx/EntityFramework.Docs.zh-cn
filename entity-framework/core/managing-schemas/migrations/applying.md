---
title: 应用迁移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238721"
---
# <a name="applying-migrations"></a><span data-ttu-id="7e35e-102">应用迁移</span><span class="sxs-lookup"><span data-stu-id="7e35e-102">Applying Migrations</span></span>

<span data-ttu-id="7e35e-103">添加迁移后，需要对其进行部署并将其应用于数据库。</span><span class="sxs-lookup"><span data-stu-id="7e35e-103">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="7e35e-104">有多种策略可用于执行此操作，某些策略更适用于生产环境，而另一些则用于开发生命周期。</span><span class="sxs-lookup"><span data-stu-id="7e35e-104">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="7e35e-105">无论部署策略如何，都应始终检查生成的迁移并在应用到生产数据库之前对其进行测试。</span><span class="sxs-lookup"><span data-stu-id="7e35e-105">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="7e35e-106">如果尝试对列进行重命名，则迁移可能会删除列，如果应用到数据库，则可能会因各种原因而失败。</span><span class="sxs-lookup"><span data-stu-id="7e35e-106">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="7e35e-107">SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="7e35e-107">SQL scripts</span></span>

<span data-ttu-id="7e35e-108">部署到生产数据库的建议方法是生成 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-108">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="7e35e-109">此策略的优点包括：</span><span class="sxs-lookup"><span data-stu-id="7e35e-109">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="7e35e-110">可以查看 SQL 脚本的准确性;这一点很重要，因为对生产数据库应用架构更改可能会导致数据丢失。</span><span class="sxs-lookup"><span data-stu-id="7e35e-110">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="7e35e-111">在某些情况下，可以根据生产数据库的特定需求调整这些脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-111">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="7e35e-112">SQL 脚本可以与部署技术结合使用，甚至可以作为 CI 过程的一部分生成。</span><span class="sxs-lookup"><span data-stu-id="7e35e-112">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="7e35e-113">SQL 脚本可提供给 DBA，可以单独管理和存档。</span><span class="sxs-lookup"><span data-stu-id="7e35e-113">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7e35e-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e35e-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="7e35e-115">基本用法</span><span class="sxs-lookup"><span data-stu-id="7e35e-115">Basic Usage</span></span>

<span data-ttu-id="7e35e-116">下面的内容生成一个从空白数据库到最新迁移的 SQL 脚本：</span><span class="sxs-lookup"><span data-stu-id="7e35e-116">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="7e35e-117">使用 From（to 隐含）</span><span class="sxs-lookup"><span data-stu-id="7e35e-117">With From (to implied)</span></span>

<span data-ttu-id="7e35e-118">以下生成从给定的迁移到最新迁移的 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-118">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="7e35e-119">使用 From 和 To</span><span class="sxs-lookup"><span data-stu-id="7e35e-119">With From and To</span></span>

<span data-ttu-id="7e35e-120">以下生成从指定的 `from` 迁移到指定迁移的 SQL 脚本 `to` 。</span><span class="sxs-lookup"><span data-stu-id="7e35e-120">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="7e35e-121">可以使用比 `to` 新的 `from` 来生成回退脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-121">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="7e35e-122">请记下潜在的数据丢失方案。</span><span class="sxs-lookup"><span data-stu-id="7e35e-122">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="7e35e-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e35e-123">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="7e35e-124">基本用法</span><span class="sxs-lookup"><span data-stu-id="7e35e-124">Basic Usage</span></span>

<span data-ttu-id="7e35e-125">下面的内容生成一个从空白数据库到最新迁移的 SQL 脚本：</span><span class="sxs-lookup"><span data-stu-id="7e35e-125">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="7e35e-126">使用 From（to 隐含）</span><span class="sxs-lookup"><span data-stu-id="7e35e-126">With From (to implied)</span></span>

<span data-ttu-id="7e35e-127">以下生成从给定的迁移到最新迁移的 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-127">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="7e35e-128">使用 From 和 To</span><span class="sxs-lookup"><span data-stu-id="7e35e-128">With From and To</span></span>

<span data-ttu-id="7e35e-129">以下生成从指定的 `from` 迁移到指定迁移的 SQL 脚本 `to` 。</span><span class="sxs-lookup"><span data-stu-id="7e35e-129">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="7e35e-130">可以使用比 `to` 新的 `from` 来生成回退脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-130">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="7e35e-131">请记下潜在的数据丢失方案。</span><span class="sxs-lookup"><span data-stu-id="7e35e-131">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="7e35e-132">脚本生成接受以下两个自变量，以指示应生成的迁移范围：</span><span class="sxs-lookup"><span data-stu-id="7e35e-132">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="7e35e-133">from 迁移应是运行该脚本前应用到数据库的最后一个迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-133">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="7e35e-134">如果未应用任何迁移，请指定 `0`（默认值）。</span><span class="sxs-lookup"><span data-stu-id="7e35e-134">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="7e35e-135">to 迁移是运行该脚本后应用到数据库的最后一个迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-135">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="7e35e-136">它默认为项目中的最后一个迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-136">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="7e35e-137">幂等 SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="7e35e-137">Idempotent SQL scripts</span></span>

<span data-ttu-id="7e35e-138">上面生成的 SQL 脚本只能应用于将架构从一个迁移更改到另一个迁移;你需要负责适当地应用脚本，并且仅适用于处于正确迁移状态的数据库。</span><span class="sxs-lookup"><span data-stu-id="7e35e-138">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="7e35e-139">EF Core 还支持生成**幂等**脚本，这些脚本在内部检查是否已通过迁移历史记录表) 应用了哪些迁移 (并仅应用缺少的迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-139">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="7e35e-140">如果您不知道最后一次应用到数据库的迁移，或者要部署到多个数据库（每个数据库都在不同的迁移上），这会很有用。</span><span class="sxs-lookup"><span data-stu-id="7e35e-140">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="7e35e-141">以下生成幂等迁移：</span><span class="sxs-lookup"><span data-stu-id="7e35e-141">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="7e35e-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e35e-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="7e35e-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e35e-143">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="7e35e-144">命令行工具</span><span class="sxs-lookup"><span data-stu-id="7e35e-144">Command-line tools</span></span>

<span data-ttu-id="7e35e-145">EF 命令行工具可用于将迁移应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="7e35e-145">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="7e35e-146">尽管迁移的本地开发和测试工作效率，但这种方法不适合用于管理生产数据库：</span><span class="sxs-lookup"><span data-stu-id="7e35e-146">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="7e35e-147">SQL 命令直接应用于该工具，而不是让开发人员进行更改来检查或修改它们。</span><span class="sxs-lookup"><span data-stu-id="7e35e-147">The SQL commands are applied directly by the tool, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="7e35e-148">这在生产环境中可能会很危险。</span><span class="sxs-lookup"><span data-stu-id="7e35e-148">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="7e35e-149">.NET SDK 和 EF 工具必须安装在生产服务器上。</span><span class="sxs-lookup"><span data-stu-id="7e35e-149">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7e35e-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e35e-150">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="7e35e-151">以下内容将数据库更新到最新迁移：</span><span class="sxs-lookup"><span data-stu-id="7e35e-151">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="7e35e-152">以下操作会将数据库更新到给定的迁移：</span><span class="sxs-lookup"><span data-stu-id="7e35e-152">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="7e35e-153">请注意，这也可用于回滚到之前的迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-153">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="7e35e-154">请记下潜在的数据丢失方案。</span><span class="sxs-lookup"><span data-stu-id="7e35e-154">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="7e35e-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e35e-155">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="7e35e-156">以下内容将数据库更新到最新迁移：</span><span class="sxs-lookup"><span data-stu-id="7e35e-156">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="7e35e-157">以下操作会将数据库更新到给定的迁移：</span><span class="sxs-lookup"><span data-stu-id="7e35e-157">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="7e35e-158">请注意，这也可用于回滚到之前的迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-158">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="7e35e-159">请记下潜在的数据丢失方案。</span><span class="sxs-lookup"><span data-stu-id="7e35e-159">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="7e35e-160">有关通过命令行工具应用迁移的详细信息，请参阅[EF Core 工具参考](xref:core/miscellaneous/cli/index)。</span><span class="sxs-lookup"><span data-stu-id="7e35e-160">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="7e35e-161">在运行时应用迁移</span><span class="sxs-lookup"><span data-stu-id="7e35e-161">Apply migrations at runtime</span></span>

<span data-ttu-id="7e35e-162">应用程序本身可能会以编程方式（通常是在启动期间）应用迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-162">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="7e35e-163">尽管迁移的本地开发和测试工作效率，但由于以下原因，此方法不适用于管理生产数据库：</span><span class="sxs-lookup"><span data-stu-id="7e35e-163">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="7e35e-164">如果你的应用程序的多个实例正在运行，这两个应用程序都可以尝试同时应用迁移和失败 (或更糟，导致数据损坏) 。</span><span class="sxs-lookup"><span data-stu-id="7e35e-164">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="7e35e-165">同样，如果应用程序在另一个应用程序迁移它时访问数据库，这可能会导致严重问题。</span><span class="sxs-lookup"><span data-stu-id="7e35e-165">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="7e35e-166">应用程序必须具有提升的访问权限才能修改数据库架构。</span><span class="sxs-lookup"><span data-stu-id="7e35e-166">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="7e35e-167">在生产环境中限制应用程序的数据库权限通常是一种很好的做法。</span><span class="sxs-lookup"><span data-stu-id="7e35e-167">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="7e35e-168">在出现问题时，必须能够回滚应用的迁移。</span><span class="sxs-lookup"><span data-stu-id="7e35e-168">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="7e35e-169">其他策略可轻松地提供这种情况。</span><span class="sxs-lookup"><span data-stu-id="7e35e-169">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="7e35e-170">SQL 命令直接应用于程序，而不会为开发人员提供检查或修改它们的更改。</span><span class="sxs-lookup"><span data-stu-id="7e35e-170">The SQL commands are applied directly by the program, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="7e35e-171">这在生产环境中可能会很危险。</span><span class="sxs-lookup"><span data-stu-id="7e35e-171">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="7e35e-172">若要以编程方式应用迁移，请调用 `context.Database.Migrate()` 。</span><span class="sxs-lookup"><span data-stu-id="7e35e-172">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="7e35e-173">例如，典型的 ASP.NET 应用程序可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7e35e-173">For example, a typical ASP.NET application can do the following:</span></span>

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="7e35e-174">请注意， `Migrate()` 在服务的基础上构建 `IMigrator` ，可用于更高级的方案。</span><span class="sxs-lookup"><span data-stu-id="7e35e-174">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="7e35e-175">请使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="7e35e-175">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="7e35e-176">在生产环境中使用此方法之前，请仔细考虑。</span><span class="sxs-lookup"><span data-stu-id="7e35e-176">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="7e35e-177">经验表明，此部署策略的简易性有点了它创建的问题。</span><span class="sxs-lookup"><span data-stu-id="7e35e-177">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="7e35e-178">请考虑改用 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="7e35e-178">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="7e35e-179">请勿在 `Migrate()` 前调用 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="7e35e-179">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="7e35e-180">`EnsureCreated()` 会绕过迁移创建架构，这会导致 `Migrate()` 失败。</span><span class="sxs-lookup"><span data-stu-id="7e35e-180">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
