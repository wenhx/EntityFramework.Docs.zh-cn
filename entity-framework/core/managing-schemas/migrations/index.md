---
title: 迁移概述 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238224"
---
# <a name="migrations-overview"></a><span data-ttu-id="a9905-102">迁移概述</span><span class="sxs-lookup"><span data-stu-id="a9905-102">Migrations Overview</span></span>

<span data-ttu-id="a9905-103">在实际项目中，数据模型随着功能的实现而变化：添加和删除新的实体或属性，并且需要相应地更改数据库架构，使其与应用程序保持同步。</span><span class="sxs-lookup"><span data-stu-id="a9905-103">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="a9905-104">EF Core 中的迁移功能能够以递增方式更新数据库架构，使其与应用程序的数据模型保持同步，同时保留数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="a9905-104">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="a9905-105">简要地说，迁移的方式如下：</span><span class="sxs-lookup"><span data-stu-id="a9905-105">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="a9905-106">当引入数据模型更改时，开发人员使用 EF Core 工具添加相应的迁移，以描述使数据库架构保持同步所需的更新。EF Core 将当前模型与旧模型的快照进行比较，以确定差异，并生成迁移源文件；文件可在项目的源代码管理中进行跟踪，如任何其他源文件。</span><span class="sxs-lookup"><span data-stu-id="a9905-106">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="a9905-107">生成新的迁移后，可通过多种方式将其应用于数据库。</span><span class="sxs-lookup"><span data-stu-id="a9905-107">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="a9905-108">EF Core 在一个特殊的历史记录表中记录所有应用的迁移，使其知道哪些迁移已应用，哪些迁移尚未应用。</span><span class="sxs-lookup"><span data-stu-id="a9905-108">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="a9905-109">本页的其余部分是使用迁移的分步式入门指南。</span><span class="sxs-lookup"><span data-stu-id="a9905-109">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="a9905-110">有关更深入的信息，请参阅本节中的其他页面。</span><span class="sxs-lookup"><span data-stu-id="a9905-110">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="a9905-111">入门</span><span class="sxs-lookup"><span data-stu-id="a9905-111">Getting started</span></span>

<span data-ttu-id="a9905-112">假设你刚刚完成了第一个 EF Core 应用程序，其中包含以下简单模型：</span><span class="sxs-lookup"><span data-stu-id="a9905-112">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="a9905-113">在开发过程中，你可能已使用[创建和删除 API ](xref:core/managing-schemas/ensure-created)以快速迭代，并根据需要更改模型；但现在，你的应用程序将进入生产环境，你需要一种方法来安全地演化架构，而无需删除整个数据库。</span><span class="sxs-lookup"><span data-stu-id="a9905-113">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="a9905-114">安装工具</span><span class="sxs-lookup"><span data-stu-id="a9905-114">Install the tools</span></span>

<span data-ttu-id="a9905-115">首先，必须安装 [EF Core 命令行工具](xref:core/miscellaneous/cli/index)：</span><span class="sxs-lookup"><span data-stu-id="a9905-115">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="a9905-116">我们通常建议使用 [.NET Core CLI 工具](xref:core/miscellaneous/cli/dotnet)，该工具适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="a9905-116">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="a9905-117">如果你更喜欢在 Visual Studio 内工作或你具有 EF6 迁移经验，还可以使用[程序包管理器控制台工具](xref:core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="a9905-117">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="a9905-118">创建第一个迁移</span><span class="sxs-lookup"><span data-stu-id="a9905-118">Create your first migration</span></span>

<span data-ttu-id="a9905-119">你现在已准备好添加第一个迁移！</span><span class="sxs-lookup"><span data-stu-id="a9905-119">You're now ready to add your first migration!</span></span> <span data-ttu-id="a9905-120">指示 EF Core 创建名为 InitialCreate 的迁移：</span><span class="sxs-lookup"><span data-stu-id="a9905-120">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="a9905-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9905-121">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="a9905-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9905-122">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="a9905-123">EF Core 将在项目中创建一个名为“Migrations”的目录，并生成一些文件。</span><span class="sxs-lookup"><span data-stu-id="a9905-123">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="a9905-124">最好检查 EF Core 生成的内容，并在可能的情况下修改它，但现在我们跳过此操作。</span><span class="sxs-lookup"><span data-stu-id="a9905-124">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="a9905-125">创建数据库和架构</span><span class="sxs-lookup"><span data-stu-id="a9905-125">Create your database and schema</span></span>

<span data-ttu-id="a9905-126">此时，可以让 EF 创建数据库并从迁移中创建架构。</span><span class="sxs-lookup"><span data-stu-id="a9905-126">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="a9905-127">可以通过以下操作实现这一点：</span><span class="sxs-lookup"><span data-stu-id="a9905-127">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="a9905-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9905-128">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="a9905-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9905-129">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="a9905-130">就是这么回事 - 你的应用程序已准备好在新数据库上运行，你无需编写任何 SQL 代码。</span><span class="sxs-lookup"><span data-stu-id="a9905-130">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="a9905-131">请注意，这种应用迁移的方法非常适合本地开发，但不太适用于生产环境 - 有关详细信息，请参阅[应用迁移页面](xref:core/managing-schemas/migrations/applying)。</span><span class="sxs-lookup"><span data-stu-id="a9905-131">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="a9905-132">发展模型</span><span class="sxs-lookup"><span data-stu-id="a9905-132">Evolving your model</span></span>

<span data-ttu-id="a9905-133">几天后，系统会要求你将创建时间戳添加到博客。</span><span class="sxs-lookup"><span data-stu-id="a9905-133">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="a9905-134">你已完成针对应用程序的必要更改，模型现在如下所示：</span><span class="sxs-lookup"><span data-stu-id="a9905-134">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="a9905-135">模型和生产数据库现在不同步，我们必须向数据库架构中添加一个新列。</span><span class="sxs-lookup"><span data-stu-id="a9905-135">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="a9905-136">让我们为此创建新迁移：</span><span class="sxs-lookup"><span data-stu-id="a9905-136">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="a9905-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9905-137">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="a9905-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9905-138">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="a9905-139">请注意，我们为迁移提供了一个描述性名称，以便以后更容易了解项目历史记录。</span><span class="sxs-lookup"><span data-stu-id="a9905-139">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="a9905-140">由于这不是项目的第一次迁移，EF Core 现在会在添加列之前将更新的模型与旧模型的快照进行比较；模型快照是 EF Core 在你添加迁移时生成的文件之一，并签入到源代码管理中。</span><span class="sxs-lookup"><span data-stu-id="a9905-140">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="a9905-141">基于该比较，EF Core 检测到已添加一列，并添加适当的迁移。</span><span class="sxs-lookup"><span data-stu-id="a9905-141">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="a9905-142">现在，你可以像以前一样应用迁移：</span><span class="sxs-lookup"><span data-stu-id="a9905-142">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="a9905-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9905-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="a9905-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9905-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="a9905-145">请注意，这次 EF 检测到数据库已存在。</span><span class="sxs-lookup"><span data-stu-id="a9905-145">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="a9905-146">此外，在之前第一次应用迁移时，此事实记录在数据库中的特殊迁移历史记录表中；这允许 EF 自动仅应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="a9905-146">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="a9905-147">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a9905-147">Next steps</span></span>

<span data-ttu-id="a9905-148">以上只是迁移的简短简介。</span><span class="sxs-lookup"><span data-stu-id="a9905-148">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="a9905-149">请参阅其他文档页面，详细了解如何[管理迁移](xref:core/managing-schemas/migrations/managing)、[应用迁移](xref:core/managing-schemas/migrations/applying)等。</span><span class="sxs-lookup"><span data-stu-id="a9905-149">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="a9905-150">[.NET Core CLI 工具参考](xref:core/miscellaneous/cli/index)还包含有关不同命令的有用信息</span><span class="sxs-lookup"><span data-stu-id="a9905-150">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
