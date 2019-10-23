---
title: 迁移 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: e9c4013d17a2d41772822f77b3ceba15702ffc48
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812060"
---
# <a name="migrations"></a><span data-ttu-id="d9804-102">迁移</span><span class="sxs-lookup"><span data-stu-id="d9804-102">Migrations</span></span>

<span data-ttu-id="d9804-103">开发期间，数据模型将发生更改并与数据库不同步。</span><span class="sxs-lookup"><span data-stu-id="d9804-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="d9804-104">可以删除该数据库，让 EF 创建一个新的数据库来匹配该模型，但此过程会导致数据丢失。</span><span class="sxs-lookup"><span data-stu-id="d9804-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="d9804-105">EF Core 中的迁移功能能够以递增方式更新数据库架构，使其与应用程序的数据模型保持同步，同时保留数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="d9804-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="d9804-106">迁移包括命令行工具和 API，可帮助执行以下任务：</span><span class="sxs-lookup"><span data-stu-id="d9804-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="d9804-107">[创建迁移](#create-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="d9804-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="d9804-108">生成可以更新数据库以使其与一系列模型更改同步的代码。</span><span class="sxs-lookup"><span data-stu-id="d9804-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="d9804-109">[更新数据库](#update-the-database)。</span><span class="sxs-lookup"><span data-stu-id="d9804-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="d9804-110">应用挂起的迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d9804-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="d9804-111">[自定义迁移代码](#customize-migration-code)。</span><span class="sxs-lookup"><span data-stu-id="d9804-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="d9804-112">有时，需要修改或补充生成的代码。</span><span class="sxs-lookup"><span data-stu-id="d9804-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="d9804-113">[删除迁移](#remove-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="d9804-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="d9804-114">删除生成的代码。</span><span class="sxs-lookup"><span data-stu-id="d9804-114">Delete the generated code.</span></span>
* <span data-ttu-id="d9804-115">[还原迁移](#revert-a-migration)。</span><span class="sxs-lookup"><span data-stu-id="d9804-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="d9804-116">撤消数据库更改。</span><span class="sxs-lookup"><span data-stu-id="d9804-116">Undo the database changes.</span></span>
* <span data-ttu-id="d9804-117">[生成 SQL 脚本](#generate-sql-scripts)。</span><span class="sxs-lookup"><span data-stu-id="d9804-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="d9804-118">可能需要一个脚本来更新生产数据库，或者对迁移代码进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="d9804-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="d9804-119">[在运行时应用迁移](#apply-migrations-at-runtime)。</span><span class="sxs-lookup"><span data-stu-id="d9804-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="d9804-120">当设计时更新和正在运行脚本不是最佳选项时，调用 `Migrate()` 方法。</span><span class="sxs-lookup"><span data-stu-id="d9804-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

> [!TIP]
> <span data-ttu-id="d9804-121">如果 `DbContext` 与启动项目位于不同程序集中，可以在[包管理器控制台工具](xref:core/miscellaneous/cli/powershell#target-and-startup-project)或 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project)中显式指定目标和启动项目。</span><span class="sxs-lookup"><span data-stu-id="d9804-121">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="install-the-tools"></a><span data-ttu-id="d9804-122">安装工具</span><span class="sxs-lookup"><span data-stu-id="d9804-122">Install the tools</span></span>

<span data-ttu-id="d9804-123">安装[命令提示符工具](xref:core/miscellaneous/cli/index)：</span><span class="sxs-lookup"><span data-stu-id="d9804-123">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="d9804-124">对于 Visual Studio，建议使用 [包管理器控制台工具](xref:core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="d9804-124">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="d9804-125">对于其他开发环境，请选择 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="d9804-125">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

## <a name="create-a-migration"></a><span data-ttu-id="d9804-126">创建迁移</span><span class="sxs-lookup"><span data-stu-id="d9804-126">Create a migration</span></span>

<span data-ttu-id="d9804-127">[定义初始模型](xref:core/modeling/index)后，即应创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d9804-127">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="d9804-128">若要添加初始迁移，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="d9804-128">To add an initial migration, run the following command.</span></span>

``` powershell
Add-Migration InitialCreate
```

``` Console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="d9804-129">向**Migrations**目录下的项目添加以下三个文件：</span><span class="sxs-lookup"><span data-stu-id="d9804-129">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="d9804-130">XXXXXXXXXXXXXX_InitialCreate.cs - 主迁移文件  。</span><span class="sxs-lookup"><span data-stu-id="d9804-130">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="d9804-131">包含应用迁移所需的操作（在 `Up()` 中）和还原迁移所需的操作（在 `Down()` 中）。</span><span class="sxs-lookup"><span data-stu-id="d9804-131">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="d9804-132">XXXXXXXXXXXXXX_InitialCreate.Designer.cs - 迁移元数据文件  。</span><span class="sxs-lookup"><span data-stu-id="d9804-132">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="d9804-133">包含 EF 所用的信息。</span><span class="sxs-lookup"><span data-stu-id="d9804-133">Contains information used by EF.</span></span>
* <span data-ttu-id="d9804-134">**MyContextModelSnapshot.cs**--当前模型的快照。</span><span class="sxs-lookup"><span data-stu-id="d9804-134">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="d9804-135">用于确定添加下一迁移时的更改内容。</span><span class="sxs-lookup"><span data-stu-id="d9804-135">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="d9804-136">文件名中的时间戳有助于保持文件按时间顺序排列，以便你可以查看更改进展。</span><span class="sxs-lookup"><span data-stu-id="d9804-136">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="d9804-137">可以自由移动“Migrations”目录下的迁移文件并更改其命名空间。</span><span class="sxs-lookup"><span data-stu-id="d9804-137">You are free to move Migrations files and change their namespace.</span></span> <span data-ttu-id="d9804-138">新建的迁移和上个迁移同级。</span><span class="sxs-lookup"><span data-stu-id="d9804-138">New migrations are created as siblings of the last migration.</span></span>

## <a name="update-the-database"></a><span data-ttu-id="d9804-139">更新数据库</span><span class="sxs-lookup"><span data-stu-id="d9804-139">Update the database</span></span>

<span data-ttu-id="d9804-140">接下来，将迁移应用到数据库以创建架构。</span><span class="sxs-lookup"><span data-stu-id="d9804-140">Next, apply the migration to the database to create the schema.</span></span>

``` powershell
Update-Database
```

``` Console
dotnet ef database update
```

## <a name="customize-migration-code"></a><span data-ttu-id="d9804-141">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="d9804-141">Customize migration code</span></span>

<span data-ttu-id="d9804-142">更改 EF Core 模型后，数据库架构可能不同步。为使其保持最新，请再添加一个迁移。</span><span class="sxs-lookup"><span data-stu-id="d9804-142">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="d9804-143">迁移名称的用途与版本控制系统中的提交消息类似。</span><span class="sxs-lookup"><span data-stu-id="d9804-143">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="d9804-144">例如，如果更改对于评审是一个新的实体类，可以选择一个名称，如 AddProductReviews  。</span><span class="sxs-lookup"><span data-stu-id="d9804-144">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

``` powershell
Add-Migration AddProductReviews
```

``` Console
dotnet ef migrations add AddProductReviews
```

<span data-ttu-id="d9804-145">搭建迁移基架（为其生成代码）后，检查代码的准确性，并添加、删除或修改正确应用代码所需的任何操作。</span><span class="sxs-lookup"><span data-stu-id="d9804-145">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="d9804-146">例如，迁移可能包含以下操作：</span><span class="sxs-lookup"><span data-stu-id="d9804-146">For example, a migration might contain the following operations:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="d9804-147">虽然这些操作可使数据库架构兼容，但是它们不会保留现有客户姓名。</span><span class="sxs-lookup"><span data-stu-id="d9804-147">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="d9804-148">如下所示，我们可以重写以改善这一情况。</span><span class="sxs-lookup"><span data-stu-id="d9804-148">To make it better, rewrite it as follows.</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> <span data-ttu-id="d9804-149">当某个操作可能会导致数据丢失（例如删除某列），搭建迁移基架过程将对此发出警告。</span><span class="sxs-lookup"><span data-stu-id="d9804-149">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="d9804-150">如果看到此警告，务必检查迁移代码的准确性。</span><span class="sxs-lookup"><span data-stu-id="d9804-150">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="d9804-151">使用相应命令将迁移应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9804-151">Apply the migration to the database using the appropriate command.</span></span>

``` powershell
Update-Database
```

``` Console
dotnet ef database update
```

### <a name="empty-migrations"></a><span data-ttu-id="d9804-152">空迁移</span><span class="sxs-lookup"><span data-stu-id="d9804-152">Empty migrations</span></span>

<span data-ttu-id="d9804-153">有时模型未变更，直接添加迁移也很有用处。</span><span class="sxs-lookup"><span data-stu-id="d9804-153">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="d9804-154">在这种情况下，添加新迁移会创建一个带空类的代码文件。</span><span class="sxs-lookup"><span data-stu-id="d9804-154">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="d9804-155">可以自定义此迁移，执行与 EF Core 模型不直接相关的操作。</span><span class="sxs-lookup"><span data-stu-id="d9804-155">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="d9804-156">可能需要通过此方式管理的一些事项包括：</span><span class="sxs-lookup"><span data-stu-id="d9804-156">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="d9804-157">全文搜索</span><span class="sxs-lookup"><span data-stu-id="d9804-157">Full-Text Search</span></span>
* <span data-ttu-id="d9804-158">函数</span><span class="sxs-lookup"><span data-stu-id="d9804-158">Functions</span></span>
* <span data-ttu-id="d9804-159">存储过程</span><span class="sxs-lookup"><span data-stu-id="d9804-159">Stored procedures</span></span>
* <span data-ttu-id="d9804-160">触发器</span><span class="sxs-lookup"><span data-stu-id="d9804-160">Triggers</span></span>
* <span data-ttu-id="d9804-161">视图</span><span class="sxs-lookup"><span data-stu-id="d9804-161">Views</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="d9804-162">删除迁移</span><span class="sxs-lookup"><span data-stu-id="d9804-162">Remove a migration</span></span>

<span data-ttu-id="d9804-163">有时，你可能在添加迁移后意识到需要在应用迁移前对 EF Core 模型作出其他更改。</span><span class="sxs-lookup"><span data-stu-id="d9804-163">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="d9804-164">要删除上个迁移，请使用如下命令。</span><span class="sxs-lookup"><span data-stu-id="d9804-164">To remove the last migration, use this command.</span></span>

``` powershell
Remove-Migration
```

``` Console
dotnet ef migrations remove
```

<span data-ttu-id="d9804-165">删除迁移后，可对模型作出其他更改，然后再次添加迁移。</span><span class="sxs-lookup"><span data-stu-id="d9804-165">After removing the migration, you can make the additional model changes and add it again.</span></span>

## <a name="revert-a-migration"></a><span data-ttu-id="d9804-166">还原迁移</span><span class="sxs-lookup"><span data-stu-id="d9804-166">Revert a migration</span></span>

<span data-ttu-id="d9804-167">如果已对数据库应用一个迁移（或多个迁移），但需将其复原，则可使用同一命令来应用迁移，并指定回退时的目标迁移名称。</span><span class="sxs-lookup"><span data-stu-id="d9804-167">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

``` powershell
Update-Database LastGoodMigration
```

``` Console
dotnet ef database update LastGoodMigration
```

## <a name="generate-sql-scripts"></a><span data-ttu-id="d9804-168">生成 SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="d9804-168">Generate SQL scripts</span></span>

<span data-ttu-id="d9804-169">调试迁移或将其部署到生产数据库时，生成一个 SQL 脚本很有帮助。</span><span class="sxs-lookup"><span data-stu-id="d9804-169">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="d9804-170">之后可进一步检查该脚本的准确性，并对其作出调整以满足生产数据库的需求。</span><span class="sxs-lookup"><span data-stu-id="d9804-170">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="d9804-171">该脚本还可与部署技术结合使用。</span><span class="sxs-lookup"><span data-stu-id="d9804-171">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="d9804-172">基本命令如下。</span><span class="sxs-lookup"><span data-stu-id="d9804-172">The basic command is as follows.</span></span>

``` powershell
Script-Migration
```

``` Console
dotnet ef migrations script
```

<span data-ttu-id="d9804-173">此命令有几个选项。</span><span class="sxs-lookup"><span data-stu-id="d9804-173">There are several options to this command.</span></span>

<span data-ttu-id="d9804-174">from 迁移应是运行该脚本前应用到数据库的最后一个迁移  。</span><span class="sxs-lookup"><span data-stu-id="d9804-174">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="d9804-175">如果未应用任何迁移，请指定 `0`（默认值）。</span><span class="sxs-lookup"><span data-stu-id="d9804-175">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="d9804-176">to 迁移是运行该脚本后应用到数据库的最后一个迁移  。</span><span class="sxs-lookup"><span data-stu-id="d9804-176">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="d9804-177">它默认为项目中的最后一个迁移。</span><span class="sxs-lookup"><span data-stu-id="d9804-177">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="d9804-178">可以选择生成 idempotent 脚本  。</span><span class="sxs-lookup"><span data-stu-id="d9804-178">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="d9804-179">此脚本仅会应用尚未应用到数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="d9804-179">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="d9804-180">如果不确知应用到数据库的最后一个迁移或需要部署到多个可能分别处于不同迁移的数据库，此脚本非常有用。</span><span class="sxs-lookup"><span data-stu-id="d9804-180">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="d9804-181">在运行时应用迁移</span><span class="sxs-lookup"><span data-stu-id="d9804-181">Apply migrations at runtime</span></span>

<span data-ttu-id="d9804-182">启动或首次运行期间，一些应用可能需要在运行时应用迁移。</span><span class="sxs-lookup"><span data-stu-id="d9804-182">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="d9804-183">为此，请使用 `Migrate()` 方法。</span><span class="sxs-lookup"><span data-stu-id="d9804-183">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="d9804-184">此方法构建于 `IMigrator` 服务之上，该服务可用于更多高级方案。</span><span class="sxs-lookup"><span data-stu-id="d9804-184">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="d9804-185">请使用 `myDbContext.GetInfrastructure().GetService<IMigrator>()` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="d9804-185">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * <span data-ttu-id="d9804-186">此方法并不适合所有人。</span><span class="sxs-lookup"><span data-stu-id="d9804-186">This approach isn't for everyone.</span></span> <span data-ttu-id="d9804-187">尽管此方法非常适合具有本地数据库的应用，但是大多数应用程序需要更可靠的部署策略，例如生成 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="d9804-187">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="d9804-188">请勿在 `Migrate()` 前调用 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="d9804-188">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="d9804-189">`EnsureCreated()` 会绕过迁移创建架构，这会导致 `Migrate()` 失败。</span><span class="sxs-lookup"><span data-stu-id="d9804-189">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d9804-190">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d9804-190">Next steps</span></span>

<span data-ttu-id="d9804-191">有关详细信息，请参阅 <xref:core/miscellaneous/cli/index>。</span><span class="sxs-lookup"><span data-stu-id="d9804-191">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
