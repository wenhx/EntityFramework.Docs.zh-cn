---
title: 入门 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 0e7a1ee159cdf5b72448fe6d73c972975b1ab95b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412862"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="26b98-102">EF Core 入门</span><span class="sxs-lookup"><span data-stu-id="26b98-102">Getting Started with EF Core</span></span>

<span data-ttu-id="26b98-103">在本教程中，将创建一个 .NET Core 控制台应用，该应用使用 Entity Framework Core 对 SQLite 数据库执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="26b98-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="26b98-104">你可在 Windows 上使用 Visual Studio，或在 Windows、macOS 或 Linux 上使用 .NET Core CLI 来学习本教程。</span><span class="sxs-lookup"><span data-stu-id="26b98-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="26b98-105">[在 GitHub 上查看此文章的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="26b98-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26b98-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="26b98-106">Prerequisites</span></span>

<span data-ttu-id="26b98-107">安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="26b98-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="26b98-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="26b98-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="26b98-109">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="26b98-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="26b98-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b98-111">对于此工作负载，建议采用 [Visual Studio 2019 版本 16.3 或更高版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="26b98-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="26b98-112">“.NET Core 跨平台开发”  （位于“其他工具集”  下）</span><span class="sxs-lookup"><span data-stu-id="26b98-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="26b98-113">创建新项目</span><span class="sxs-lookup"><span data-stu-id="26b98-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="26b98-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="26b98-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="26b98-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b98-116">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-116">Open Visual Studio</span></span>
* <span data-ttu-id="26b98-117">单击“创建新项目” </span><span class="sxs-lookup"><span data-stu-id="26b98-117">Click **Create a new project**</span></span>
* <span data-ttu-id="26b98-118">选择带有 C# 标记的“控制台应用 (.NET Core)”   ，然后单击“下一步” </span><span class="sxs-lookup"><span data-stu-id="26b98-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="26b98-119">输入“EFGetStarted”  作为名称，然后单击“创建” </span><span class="sxs-lookup"><span data-stu-id="26b98-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="26b98-120">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="26b98-120">Install Entity Framework Core</span></span>

<span data-ttu-id="26b98-121">要安装 EF Core，请为要作为目标对象的 EF Core 数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="26b98-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="26b98-122">本教程使用 SQLite 的原因是，它可在 .NET Core 支持的所有平台上运行。</span><span class="sxs-lookup"><span data-stu-id="26b98-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="26b98-123">有关可用提供程序的列表，请参阅[数据库提供程序](../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="26b98-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="26b98-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="26b98-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="26b98-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b98-126"> “工具”>“NuGet 包管理器”>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="26b98-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="26b98-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="26b98-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="26b98-128">提示:还可以通过右键单击项目并选择“管理 NuGet 程序包”  来安装包</span><span class="sxs-lookup"><span data-stu-id="26b98-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="26b98-129">创建模型</span><span class="sxs-lookup"><span data-stu-id="26b98-129">Create the model</span></span>

<span data-ttu-id="26b98-130">定义构成模型的上下文类和实体类。</span><span class="sxs-lookup"><span data-stu-id="26b98-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="26b98-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="26b98-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="26b98-132">在项目文件夹中，使用以下代码创建 Model.cs </span><span class="sxs-lookup"><span data-stu-id="26b98-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="26b98-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b98-134">右键单击项目，然后选择“添加”>“类” </span><span class="sxs-lookup"><span data-stu-id="26b98-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="26b98-135">输入“Model.cs”  作为名称，然后单击“添加” </span><span class="sxs-lookup"><span data-stu-id="26b98-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="26b98-136">将此文件的内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="26b98-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="26b98-137">EF Core 还可以从现有数据库对模型进行[反向工程](../managing-schemas/scaffolding.md)。</span><span class="sxs-lookup"><span data-stu-id="26b98-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="26b98-138">提示:在实际应用中，将每个类放在单独的文件中，并将[连接字符串](../miscellaneous/connection-strings.md)放在配置文件或环境变量中。</span><span class="sxs-lookup"><span data-stu-id="26b98-138">Tip: In a real app, you put each class in a separate file and put the [connection string](../miscellaneous/connection-strings.md) in a configuration file or environment variable.</span></span> <span data-ttu-id="26b98-139">为简化本教程，所有内容均放在一个文件中。</span><span class="sxs-lookup"><span data-stu-id="26b98-139">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="26b98-140">创建数据库</span><span class="sxs-lookup"><span data-stu-id="26b98-140">Create the database</span></span>

<span data-ttu-id="26b98-141">以下步骤使用[迁移](xref:core/managing-schemas/migrations/index)创建数据库。</span><span class="sxs-lookup"><span data-stu-id="26b98-141">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="26b98-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="26b98-142">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="26b98-143">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="26b98-143">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="26b98-144">这会安装 [dotnet ef](../miscellaneous/cli/dotnet.md) 和设计包，这是对项目运行命令所必需的。</span><span class="sxs-lookup"><span data-stu-id="26b98-144">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="26b98-145">`migrations` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="26b98-145">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="26b98-146">`database update` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="26b98-146">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="26b98-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b98-148">在“包管理器控制台”  中运行以下命令</span><span class="sxs-lookup"><span data-stu-id="26b98-148">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="26b98-149">这会安装 [ EF Core 的 PMC 工具](../miscellaneous/cli/powershell.md)。</span><span class="sxs-lookup"><span data-stu-id="26b98-149">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="26b98-150">`Add-Migration` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="26b98-150">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="26b98-151">`Update-Database` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="26b98-151">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="26b98-152">创建、读取、更新和删除</span><span class="sxs-lookup"><span data-stu-id="26b98-152">Create, read, update & delete</span></span>

* <span data-ttu-id="26b98-153">打开 Program.cs  并将内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="26b98-153">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="26b98-154">运行应用</span><span class="sxs-lookup"><span data-stu-id="26b98-154">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="26b98-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="26b98-155">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="26b98-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b98-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26b98-157">运行 .NET Core 控制台应用时，Visual Studio 使用不一致的工作目录。</span><span class="sxs-lookup"><span data-stu-id="26b98-157">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="26b98-158">（请参阅 [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)）这会导致引发异常：*无此类表格：博客*。</span><span class="sxs-lookup"><span data-stu-id="26b98-158">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="26b98-159">更新工作目录：</span><span class="sxs-lookup"><span data-stu-id="26b98-159">To update the working directory:</span></span>

* <span data-ttu-id="26b98-160">右键单击项目，并选择“编辑项目文件” </span><span class="sxs-lookup"><span data-stu-id="26b98-160">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="26b98-161">在 TargetFramework  属性下方，添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="26b98-161">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="26b98-162">保存该文件</span><span class="sxs-lookup"><span data-stu-id="26b98-162">Save the file</span></span>

<span data-ttu-id="26b98-163">现在可以运行应用：</span><span class="sxs-lookup"><span data-stu-id="26b98-163">Now you can run the app:</span></span>

* <span data-ttu-id="26b98-164">“调试”>“开始执行(不调试)” </span><span class="sxs-lookup"><span data-stu-id="26b98-164">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="26b98-165">后续步骤</span><span class="sxs-lookup"><span data-stu-id="26b98-165">Next steps</span></span>

* <span data-ttu-id="26b98-166">按照 [ASP.NET Core 教程](/aspnet/core/data/ef-rp/intro)在 Web 应用中使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="26b98-166">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="26b98-167">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="26b98-167">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="26b98-168">[配置模型](xref:core/modeling/index)指定[必需项](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大长度](xref:core/modeling/entity-properties#maximum-length)等内容</span><span class="sxs-lookup"><span data-stu-id="26b98-168">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="26b98-169">在更改模型后使用[迁移](xref:core/managing-schemas/migrations/index)来更新数据库架构</span><span class="sxs-lookup"><span data-stu-id="26b98-169">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
