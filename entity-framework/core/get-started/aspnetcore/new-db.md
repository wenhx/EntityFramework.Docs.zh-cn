---
title: ASP.NET Core入门 - 新数据库 - EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 4734586adc89e9c1d866a1b4accd8b5e51fe2bb0
ms.sourcegitcommit: ebf661025d2ad2b62466fa7bf0e0772a7811cbe7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2019
ms.locfileid: "54211161"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="4a2e7-102">使用新数据库在 ASP.NET Core 上开始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="4a2e7-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="4a2e7-103">在本教程中，你将使用 Entity Framework Core 构建执行基本数据访问的 ASP.NET Core MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="4a2e7-104">该教程使用迁移基于此数据模型创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="4a2e7-105">你可在 Windows 上使用 Visual Studio 2017，或在 Windows、macOS 或 Linux 上使用 .NET Core CLI 来学习本教程。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="4a2e7-106">在 GitHub 上查看此文章的示例：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-106">View this article's sample on GitHub:</span></span>
* <span data-ttu-id="4a2e7-107">[Visual Studio 2017 with SQL Server](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)（配有 SQL Server 的 Visual Studio 2017）</span><span class="sxs-lookup"><span data-stu-id="4a2e7-107">[Visual Studio 2017 with SQL Server](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)</span></span>
* <span data-ttu-id="4a2e7-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite)（配有 SQLite 的 .NET Core CLI）。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a2e7-109">系统必备</span><span class="sxs-lookup"><span data-stu-id="4a2e7-109">Prerequisites</span></span>

<span data-ttu-id="4a2e7-110">安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-112">具有以下工作负载的 [Visual Studio 2017 15.7 版或更高版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="4a2e7-113">“ASP.NET 和 Web 开发”（位于“Web 和云”下）</span><span class="sxs-lookup"><span data-stu-id="4a2e7-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="4a2e7-114">“.NET Core 跨平台开发”（位于“其他工具集”下）</span><span class="sxs-lookup"><span data-stu-id="4a2e7-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="4a2e7-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="4a2e7-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4a2e7-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="4a2e7-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="4a2e7-118">创建新项目</span><span class="sxs-lookup"><span data-stu-id="4a2e7-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-120">打开 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="4a2e7-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="4a2e7-121">“文件”>“新建”>“项目”</span><span class="sxs-lookup"><span data-stu-id="4a2e7-121">**File > New > Project**</span></span>
* <span data-ttu-id="4a2e7-122">从左菜单中选择“已安装”>“Visual C#”>“.NET Core”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="4a2e7-123">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="4a2e7-124">输入“EFGetStarted.AspNetCore.NewDb”作为名称，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="4a2e7-125">在“新建 ASP.NET Core Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="4a2e7-126">确保在下拉列表中选择“.NET Core”和“ASP.NET Core 2.1”</span><span class="sxs-lookup"><span data-stu-id="4a2e7-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="4a2e7-127">选择“Web 应用程序(模型视图控制器)”项目模板</span><span class="sxs-lookup"><span data-stu-id="4a2e7-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="4a2e7-128">确保将“身份验证”设置为“无身份验证”</span><span class="sxs-lookup"><span data-stu-id="4a2e7-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="4a2e7-129">单击“确定” </span><span class="sxs-lookup"><span data-stu-id="4a2e7-129">Click **OK**</span></span>

<span data-ttu-id="4a2e7-130">警告:如果你使用“单独用户帐户”（而不是“无”）进行身份验证，Entity Framework Core 模型会添加到 `Models\IdentityModel.cs` 中的项目。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="4a2e7-131">使用在本教程中学习的技巧，可以选择添加第二个模型，或者扩展此现有模型以包含实体类。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4a2e7-133">运行以下命令以创建 MVC 项目：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="4a2e7-134">更改为项目目录。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-134">Change to the project directory.</span></span> <span data-ttu-id="4a2e7-135">你输入的下一个命令需要针对新项目运行。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="4a2e7-136">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4a2e7-136">Install Entity Framework Core</span></span>

<span data-ttu-id="4a2e7-137">要安装 EF Core，请为要作为目标对象的 EF Core 数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="4a2e7-138">有关可用提供程序的列表，请参阅[数据库提供程序](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a2e7-140">对于本教程，无需安装提供程序包，因为本教程使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="4a2e7-141">SQL Server 提供程序包包含在 [Microsoft.AspnetCore.App 元包](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)中。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4a2e7-143">本教程使用 SQLite 的原因是，它可在 .NET Core 支持的所有平台上运行。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="4a2e7-144">运行以下命令安装 SQLite 提供程序：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="4a2e7-145">创建模型</span><span class="sxs-lookup"><span data-stu-id="4a2e7-145">Create the model</span></span>

<span data-ttu-id="4a2e7-146">定义构成模型的上下文类和实体类。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-148">右键单击“Models”文件夹，然后选择“添加”>“类”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="4a2e7-149">输入“Model.cs”作为名称，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="4a2e7-150">将此文件的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4a2e7-152">在 Models 文件夹中，使用以下代码创建 Model.cs：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="4a2e7-153">生产应用通常会将每个类放在单独的文件中。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="4a2e7-154">为简单起见，本教程将这些类放在一个文件中。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="4a2e7-155">使用依赖注入注册上下文</span><span class="sxs-lookup"><span data-stu-id="4a2e7-155">Register the context with dependency injection</span></span>

<span data-ttu-id="4a2e7-156">服务（例如 `BloggingContext`）在应用程序启动期间通过[依赖关系注入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)进行注册。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-156">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="4a2e7-157">需要这些服务的组件（如 MVC 控制器）可以通过向构造函数或属性添加相关参数来获得对应服务。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-157">Components that require these services (such as MVC controllers) are provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="4a2e7-158">若要使 `BloggingContext` 对 MVC 控制器可用，请将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-158">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-159">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-160">在 Startup.cs 中，添加以下 `using` 语句：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-160">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="4a2e7-161">将以下突出显示的代码添加到 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-161">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-162">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4a2e7-163">在 Startup.cs 中，添加以下 `using` 语句：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-163">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="4a2e7-164">将以下突出显示的代码添加到 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-164">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="4a2e7-165">生产应用通常会将连接字符串放在配置文件或环境变量中。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-165">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="4a2e7-166">为简单起见，本教程在代码中定义它。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-166">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="4a2e7-167">有关详细信息，请参阅[连接字符串 ](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-167">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="4a2e7-168">创建数据库</span><span class="sxs-lookup"><span data-stu-id="4a2e7-168">Create the database</span></span>

<span data-ttu-id="4a2e7-169">以下步骤使用[迁移](xref:core/managing-schemas/migrations/index)创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-169">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-170">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-171">“工具”>“NuGet 包管理器”>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="4a2e7-171">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="4a2e7-172">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-172">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="4a2e7-173">如果收到错误，指出 `The term 'add-migration' is not recognized as the name of a cmdlet`，请关闭并重新打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-173">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="4a2e7-174">`Add-Migration` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-174">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="4a2e7-175">`Update-Database` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-175">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-176">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4a2e7-177">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-177">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="4a2e7-178">`migrations` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-178">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="4a2e7-179">`database update` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-179">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="4a2e7-180">创建控制器</span><span class="sxs-lookup"><span data-stu-id="4a2e7-180">Create a controller</span></span>

<span data-ttu-id="4a2e7-181">生成 `Blog` 实体控制器和视图的基架。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-181">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-182">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-183">在“解决方案资源管理器”中，右键单击“控制器”文件夹，然后选择“添加”>“控制器”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-183">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="4a2e7-184">选择“视图使用 Entity Framework 的 MVC 控制器”，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-184">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="4a2e7-185">将“模型类”设置为“Blog”，将“数据上下文类”设置为“BloggingContext”。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-185">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="4a2e7-186">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-186">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-187">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-187">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4a2e7-188">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-188">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="4a2e7-189">`tool install` 和 `add package` 命令安装可以为控制器和视图搭建基架的工具。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-189">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="4a2e7-190">`restore` 命令可确保下载所有的项目包，`aspnet-codegenerator` 命令可执行基架搭建。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-190">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="4a2e7-191">基架引擎创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="4a2e7-191">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="4a2e7-192">控制器 (Controllers/BlogsController.cs)</span><span class="sxs-lookup"><span data-stu-id="4a2e7-192">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="4a2e7-193">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图 (Views/Blogs/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="4a2e7-193">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Blogs/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="4a2e7-194">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="4a2e7-194">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2e7-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2e7-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a2e7-196">调试 > 开始执行(不调试)</span><span class="sxs-lookup"><span data-stu-id="4a2e7-196">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2e7-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4a2e7-197">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="4a2e7-198">导航到 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="4a2e7-198">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="4a2e7-199">使用“新建”链接创建一些博客条目。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-199">Use the **Create New** link to create some blog entries.</span></span>

  ![创建页面](_static/create.png)

* <span data-ttu-id="4a2e7-201">测试“详细信息”、“编辑”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="4a2e7-201">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![索引页](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="4a2e7-203">其他资源</span><span class="sxs-lookup"><span data-stu-id="4a2e7-203">Additional Resources</span></span>

* [<span data-ttu-id="4a2e7-204">教程：使用 SQLite 通过新数据库在 .NET Core 上开始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="4a2e7-204">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* [<span data-ttu-id="4a2e7-205">教程：在 ASP.NET Core 中开始使用 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4a2e7-205">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4a2e7-206">教程：在 ASP.NET Core 中结合使用 Razor Pages 和 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4a2e7-206">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
