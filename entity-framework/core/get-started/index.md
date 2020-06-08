---
title: 入门 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: b45a7e5010dd3ef37ea952aaf483847ae95ea4ef
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418933"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="843a7-102">EF Core 入门</span><span class="sxs-lookup"><span data-stu-id="843a7-102">Getting Started with EF Core</span></span>

<span data-ttu-id="843a7-103">在本教程中，将创建一个 .NET Core 控制台应用，该应用使用 Entity Framework Core 对 SQLite 数据库执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="843a7-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="843a7-104">你可在 Windows 上使用 Visual Studio，或在 Windows、macOS 或 Linux 上使用 .NET Core CLI 来学习本教程。</span><span class="sxs-lookup"><span data-stu-id="843a7-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="843a7-105">[在 GitHub 上查看此文章的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="843a7-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="843a7-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="843a7-106">Prerequisites</span></span>

<span data-ttu-id="843a7-107">安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="843a7-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="843a7-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="843a7-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="843a7-109">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="843a7-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="843a7-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="843a7-111">对于此工作负载，建议采用 [Visual Studio 2019 版本 16.3 或更高版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="843a7-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="843a7-112">“.NET Core 跨平台开发”（位于“其他工具集”下）</span><span class="sxs-lookup"><span data-stu-id="843a7-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="843a7-113">创建新项目</span><span class="sxs-lookup"><span data-stu-id="843a7-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="843a7-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="843a7-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="843a7-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="843a7-116">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-116">Open Visual Studio</span></span>
* <span data-ttu-id="843a7-117">单击“创建新项目”</span><span class="sxs-lookup"><span data-stu-id="843a7-117">Click **Create a new project**</span></span>
* <span data-ttu-id="843a7-118">选择带有 C# 标记的“控制台应用 (.NET Core)” ，然后单击“下一步”</span><span class="sxs-lookup"><span data-stu-id="843a7-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="843a7-119">输入“EFGetStarted”作为名称，然后单击“创建”</span><span class="sxs-lookup"><span data-stu-id="843a7-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="843a7-120">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="843a7-120">Install Entity Framework Core</span></span>

<span data-ttu-id="843a7-121">要安装 EF Core，请为要作为目标对象的 EF Core 数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="843a7-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="843a7-122">本教程使用 SQLite 的原因是，它可在 .NET Core 支持的所有平台上运行。</span><span class="sxs-lookup"><span data-stu-id="843a7-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="843a7-123">有关可用提供程序的列表，请参阅[数据库提供程序](../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="843a7-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="843a7-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="843a7-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="843a7-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="843a7-126">“工具”>“NuGet 包管理器”>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="843a7-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="843a7-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="843a7-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="843a7-128">提示:还可以通过右键单击项目并选择“管理 NuGet 程序包”来安装包</span><span class="sxs-lookup"><span data-stu-id="843a7-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="843a7-129">创建模型</span><span class="sxs-lookup"><span data-stu-id="843a7-129">Create the model</span></span>

<span data-ttu-id="843a7-130">定义构成模型的上下文类和实体类。</span><span class="sxs-lookup"><span data-stu-id="843a7-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="843a7-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="843a7-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="843a7-132">在项目文件夹中，使用以下代码创建 Model.cs</span><span class="sxs-lookup"><span data-stu-id="843a7-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="843a7-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="843a7-134">右键单击项目，然后选择“添加”>“类”</span><span class="sxs-lookup"><span data-stu-id="843a7-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="843a7-135">输入“Model.cs”作为名称，然后单击“添加”</span><span class="sxs-lookup"><span data-stu-id="843a7-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="843a7-136">将此文件的内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="843a7-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="843a7-137">EF Core 还可以从现有数据库对模型进行[反向工程](../managing-schemas/scaffolding.md)。</span><span class="sxs-lookup"><span data-stu-id="843a7-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="843a7-138">提示:为清楚起见，有意简化了此应用程序。</span><span class="sxs-lookup"><span data-stu-id="843a7-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="843a7-139">[连接字符串](../miscellaneous/connection-strings.md)不应存储在生产应用程序的代码中。</span><span class="sxs-lookup"><span data-stu-id="843a7-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="843a7-140">可能还需要将每个 C# 类拆分为其自己的文件。</span><span class="sxs-lookup"><span data-stu-id="843a7-140">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="843a7-141">创建数据库</span><span class="sxs-lookup"><span data-stu-id="843a7-141">Create the database</span></span>

<span data-ttu-id="843a7-142">以下步骤使用[迁移](xref:core/managing-schemas/migrations/index)创建数据库。</span><span class="sxs-lookup"><span data-stu-id="843a7-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="843a7-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="843a7-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="843a7-144">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="843a7-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="843a7-145">这会安装 [dotnet ef](../miscellaneous/cli/dotnet.md) 和设计包，这是对项目运行命令所必需的。</span><span class="sxs-lookup"><span data-stu-id="843a7-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="843a7-146">`migrations` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="843a7-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="843a7-147">`database update` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="843a7-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="843a7-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="843a7-149">在“包管理器控制台”中运行以下命令</span><span class="sxs-lookup"><span data-stu-id="843a7-149">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="843a7-150">这会安装 [ EF Core 的 PMC 工具](../miscellaneous/cli/powershell.md)。</span><span class="sxs-lookup"><span data-stu-id="843a7-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="843a7-151">`Add-Migration` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="843a7-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="843a7-152">`Update-Database` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="843a7-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="843a7-153">创建、读取、更新和删除</span><span class="sxs-lookup"><span data-stu-id="843a7-153">Create, read, update & delete</span></span>

* <span data-ttu-id="843a7-154">打开 Program.cs 并将内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="843a7-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="843a7-155">运行应用</span><span class="sxs-lookup"><span data-stu-id="843a7-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="843a7-156">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="843a7-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="843a7-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="843a7-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="843a7-158">运行 .NET Core 控制台应用时，Visual Studio 使用不一致的工作目录。</span><span class="sxs-lookup"><span data-stu-id="843a7-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="843a7-159">（请参阅 [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)）这会导致引发异常：*无此类表格：博客*。</span><span class="sxs-lookup"><span data-stu-id="843a7-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="843a7-160">更新工作目录：</span><span class="sxs-lookup"><span data-stu-id="843a7-160">To update the working directory:</span></span>

* <span data-ttu-id="843a7-161">右键单击项目，并选择“编辑项目文件”</span><span class="sxs-lookup"><span data-stu-id="843a7-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="843a7-162">在 TargetFramework 属性下方，添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="843a7-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="843a7-163">保存该文件</span><span class="sxs-lookup"><span data-stu-id="843a7-163">Save the file</span></span>

<span data-ttu-id="843a7-164">现在可以运行应用：</span><span class="sxs-lookup"><span data-stu-id="843a7-164">Now you can run the app:</span></span>

* <span data-ttu-id="843a7-165">“调试”>“开始执行(不调试)”</span><span class="sxs-lookup"><span data-stu-id="843a7-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="843a7-166">后续步骤</span><span class="sxs-lookup"><span data-stu-id="843a7-166">Next steps</span></span>

* <span data-ttu-id="843a7-167">按照 [ASP.NET Core 教程](/aspnet/core/data/ef-rp/intro)在 Web 应用中使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="843a7-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="843a7-168">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="843a7-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="843a7-169">[配置模型](xref:core/modeling/index)指定[必需项](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大长度](xref:core/modeling/entity-properties#maximum-length)等内容</span><span class="sxs-lookup"><span data-stu-id="843a7-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="843a7-170">在更改模型后使用[迁移](xref:core/managing-schemas/migrations/index)来更新数据库架构</span><span class="sxs-lookup"><span data-stu-id="843a7-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
