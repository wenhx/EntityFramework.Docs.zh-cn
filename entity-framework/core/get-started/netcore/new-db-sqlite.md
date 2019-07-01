---
title: NET Core 入门 - 新数据库 - EF Core
author: rick-anderson
ms.author: riande
description: 通过 Entity Framework Core 开始使用 .NET Core
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: e6996630e399659807d23304993c8e19c11ca6f5
ms.sourcegitcommit: 83c1e2fc034e5eb1fec1ebabc8d629ffcc7c0632
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2019
ms.locfileid: "67351342"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="84fa9-103">使用新数据库在 NET Core 控制台应用程序上开始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="84fa9-103">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="84fa9-104">在本教程中，将创建一个 .NET Core 控制台应用，该应用使用 Entity Framework Core 对 SQLite 数据库执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="84fa9-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="84fa9-105">你使用[迁移](xref:core/managing-schemas/migrations/index)从模型创建数据库。</span><span class="sxs-lookup"><span data-stu-id="84fa9-105">You use [migrations](xref:core/managing-schemas/migrations/index) to create the database from the model.</span></span> <span data-ttu-id="84fa9-106">有关使用 ASP.NET Core MVC 的 Visual Studio 版本，请参阅 [ASP.NET Core - 新数据库](xref:core/get-started/aspnetcore/new-db)。</span><span class="sxs-lookup"><span data-stu-id="84fa9-106">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

<span data-ttu-id="84fa9-107">[在 GitHub 上查看此文章的示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。</span><span class="sxs-lookup"><span data-stu-id="84fa9-107">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84fa9-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="84fa9-108">Prerequisites</span></span>

* [<span data-ttu-id="84fa9-109">.NET Core 2.1 SDK</span><span class="sxs-lookup"><span data-stu-id="84fa9-109">The .NET Core 2.1 SDK</span></span>](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a><span data-ttu-id="84fa9-110">创建新项目</span><span class="sxs-lookup"><span data-stu-id="84fa9-110">Create a new project</span></span>

* <span data-ttu-id="84fa9-111">新建控制台项目：</span><span class="sxs-lookup"><span data-stu-id="84fa9-111">Create a new console project:</span></span>

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  ```
## <a name="change-the-current-directory"></a><span data-ttu-id="84fa9-112">更改当前目录</span><span class="sxs-lookup"><span data-stu-id="84fa9-112">Change the current directory</span></span>

<span data-ttu-id="84fa9-113">在后续步骤中，我们需要针对应用程序发出 `dotnet` 命令。</span><span class="sxs-lookup"><span data-stu-id="84fa9-113">In subsequent steps, we need to issue `dotnet` commands against the application.</span></span>

* <span data-ttu-id="84fa9-114">我们将当前目录更改为应用程序的目录，如下所示：</span><span class="sxs-lookup"><span data-stu-id="84fa9-114">We change the current directory to the application's directory like this:</span></span>

  ``` Console
  cd ConsoleApp.SQLite/
  ```
## <a name="install-entity-framework-core"></a><span data-ttu-id="84fa9-115">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="84fa9-115">Install Entity Framework Core</span></span>

<span data-ttu-id="84fa9-116">要使用 EF Core，请为要作为目标对象的数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="84fa9-116">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="84fa9-117">本演练使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="84fa9-117">This walkthrough uses SQLite.</span></span> <span data-ttu-id="84fa9-118">有关可用提供程序的列表，请参阅[数据库提供程序](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="84fa9-118">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="84fa9-119">安装 Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.Design</span><span class="sxs-lookup"><span data-stu-id="84fa9-119">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* <span data-ttu-id="84fa9-120">运行 `dotnet restore` 来安装新的程序包。</span><span class="sxs-lookup"><span data-stu-id="84fa9-120">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="84fa9-121">创建模型</span><span class="sxs-lookup"><span data-stu-id="84fa9-121">Create the model</span></span>

<span data-ttu-id="84fa9-122">定义构成模型的上下文和实体类：</span><span class="sxs-lookup"><span data-stu-id="84fa9-122">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="84fa9-123">使用以下内容创建一个新的 Model.cs  文件。</span><span class="sxs-lookup"><span data-stu-id="84fa9-123">Create a new *Model.cs* file with the following contents.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="84fa9-124">提示:在实际应用程序中，将每个类放在单独的文件中，并将连接字符串放在配置文件或环境变量中。</span><span class="sxs-lookup"><span data-stu-id="84fa9-124">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="84fa9-125">为简化本教程，所有内容均放在一个文件中。</span><span class="sxs-lookup"><span data-stu-id="84fa9-125">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="84fa9-126">创建数据库</span><span class="sxs-lookup"><span data-stu-id="84fa9-126">Create the database</span></span>

<span data-ttu-id="84fa9-127">有了模型后，即可通过[迁移](xref:core/managing-schemas/migrations/index)创建数据库。</span><span class="sxs-lookup"><span data-stu-id="84fa9-127">Once you have a model, you use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

* <span data-ttu-id="84fa9-128">运行 `dotnet ef migrations add InitialCreate` 以为迁移搭建基架，并为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="84fa9-128">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="84fa9-129">运行 `dotnet ef database update` 以将新迁移应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="84fa9-129">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="84fa9-130">在应用迁移之前，此命令可创建数据库。</span><span class="sxs-lookup"><span data-stu-id="84fa9-130">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="84fa9-131">blogging.db SQLite DB 位于项目目录中  。</span><span class="sxs-lookup"><span data-stu-id="84fa9-131">The *blogging.db* SQLite DB is in the project directory.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="84fa9-132">使用模型</span><span class="sxs-lookup"><span data-stu-id="84fa9-132">Use the model</span></span>

* <span data-ttu-id="84fa9-133">打开 Program.cs  并将内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="84fa9-133">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="84fa9-134">从控制台测试应用。</span><span class="sxs-lookup"><span data-stu-id="84fa9-134">Test the app from the console.</span></span> <span data-ttu-id="84fa9-135">请参阅 [Visual Studio 注释](#vs)，以从 Visual Studio 运行应用。</span><span class="sxs-lookup"><span data-stu-id="84fa9-135">See the [Visual Studio note](#vs) to run the app from Visual Studio.</span></span>

  `dotnet run`

  <span data-ttu-id="84fa9-136">将一个博客保存到数据库，那么所有博客的详细信息都会显示在控制台中。</span><span class="sxs-lookup"><span data-stu-id="84fa9-136">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="84fa9-137">更改模型：</span><span class="sxs-lookup"><span data-stu-id="84fa9-137">Changing the model:</span></span>

- <span data-ttu-id="84fa9-138">如果对模型进行了更改，可使用 `dotnet ef migrations add` 命令为新[迁移](xref:core/managing-schemas/migrations/index)搭建基架。</span><span class="sxs-lookup"><span data-stu-id="84fa9-138">If you make changes to the model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](xref:core/managing-schemas/migrations/index).</span></span> <span data-ttu-id="84fa9-139">检查已搭建基架的代码（并进行了必要更改）后，可使用 `dotnet ef database update` 命令将架构更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="84fa9-139">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the schema changes to the database.</span></span>
- <span data-ttu-id="84fa9-140">EF Core 使用数据库中的 `__EFMigrationsHistory` 表来跟踪哪些迁移已应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="84fa9-140">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="84fa9-141">对于大多数其他关系数据库支持的某些架构更改，SQLite 数据库引擎并不支持。</span><span class="sxs-lookup"><span data-stu-id="84fa9-141">The SQLite database engine doesn't support certain schema changes that are supported by most other relational databases.</span></span> <span data-ttu-id="84fa9-142">例如，不支持 `DropColumn` 操作。</span><span class="sxs-lookup"><span data-stu-id="84fa9-142">For example, the `DropColumn` operation is not supported.</span></span> <span data-ttu-id="84fa9-143">EF Core 迁移将为这些操作生成代码。</span><span class="sxs-lookup"><span data-stu-id="84fa9-143">EF Core Migrations will generate code for these operations.</span></span> <span data-ttu-id="84fa9-144">但是，如果试图将它们应用于数据库或生成脚本，则 EF Core 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="84fa9-144">But if you try to apply them to a database or generate a script, EF Core throws exceptions.</span></span> <span data-ttu-id="84fa9-145">请参阅 [SQLite 限制](../../providers/sqlite/limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="84fa9-145">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="84fa9-146">进行新的开发时，考虑删除数据库并创建新的数据库，而不是在模型更改时使用迁移。</span><span class="sxs-lookup"><span data-stu-id="84fa9-146">For new development, consider dropping the database and creating a new one rather than using migrations when the model changes.</span></span>

<a name="vs"></a>
### <a name="run-from-visual-studio"></a><span data-ttu-id="84fa9-147">从 Visual Studio 中运行</span><span class="sxs-lookup"><span data-stu-id="84fa9-147">Run from Visual Studio</span></span>

<span data-ttu-id="84fa9-148">要从 Visual Studio 运行此示例，必须手动将工作目录设置为项目的根目录。</span><span class="sxs-lookup"><span data-stu-id="84fa9-148">To run this sample from Visual Studio, you must set the working directory manually to be the root of the project.</span></span> <span data-ttu-id="84fa9-149">如果未设置工作目录，则会引发以下 `Microsoft.Data.Sqlite.SqliteException`：`SQLite Error 1: 'no such table: Blogs'`。</span><span class="sxs-lookup"><span data-stu-id="84fa9-149">If  you don't set the working directory, the following `Microsoft.Data.Sqlite.SqliteException` is thrown: `SQLite Error 1: 'no such table: Blogs'`.</span></span>

<span data-ttu-id="84fa9-150">设置工作目录：</span><span class="sxs-lookup"><span data-stu-id="84fa9-150">To set the working directory:</span></span>

* <span data-ttu-id="84fa9-151">在“解决方案资源管理器”中，右键单击该项目，然后选择“属性”。  </span><span class="sxs-lookup"><span data-stu-id="84fa9-151">In **Solution Explorer**, right click the project and then select **Properties**.</span></span>
* <span data-ttu-id="84fa9-152">在左窗格中选择  “调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="84fa9-152">Select the **Debug** tab in the left pane.</span></span>
* <span data-ttu-id="84fa9-153">将工作目录设置为项目目录。 </span><span class="sxs-lookup"><span data-stu-id="84fa9-153">Set **Working directory** to the project directory.</span></span>
* <span data-ttu-id="84fa9-154">保存更改。</span><span class="sxs-lookup"><span data-stu-id="84fa9-154">Save the changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84fa9-155">其他资源</span><span class="sxs-lookup"><span data-stu-id="84fa9-155">Additional Resources</span></span>

* [<span data-ttu-id="84fa9-156">教程：使用 SQLite 通过新数据库在 ASP.NET Core 上开始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="84fa9-156">Tutorial: Get started with EF Core on ASP.NET Core with a new database using SQLite</span></span>](xref:core/get-started/aspnetcore/new-db)
* [<span data-ttu-id="84fa9-157">教程：在 ASP.NET Core 中开始使用 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="84fa9-157">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="84fa9-158">教程：在 ASP.NET Core 中结合使用 Razor Pages 和 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="84fa9-158">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
