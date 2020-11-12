---
title: 入门 - EF Core
description: Entity Framework Core 入门教程
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: ee2b8f87e48f466f8385548b0b9e20a3791ba2a3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431163"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="8b516-103">EF Core 入门</span><span class="sxs-lookup"><span data-stu-id="8b516-103">Getting Started with EF Core</span></span>

<span data-ttu-id="8b516-104">在本教程中，将创建一个 .NET Core 控制台应用，该应用使用 Entity Framework Core 对 SQLite 数据库执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="8b516-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="8b516-105">你可在 Windows 上使用 Visual Studio，或在 Windows、macOS 或 Linux 上使用 .NET Core CLI 来学习本教程。</span><span class="sxs-lookup"><span data-stu-id="8b516-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="8b516-106">[在 GitHub 上查看此文章的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="8b516-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b516-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="8b516-107">Prerequisites</span></span>

<span data-ttu-id="8b516-108">安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="8b516-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8b516-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b516-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="8b516-110">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="8b516-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="8b516-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b516-112">对于此工作负载，建议采用 [Visual Studio 2019 版本 16.3 或更高版本](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="8b516-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="8b516-113">“.NET Core 跨平台开发”（位于“其他工具集”下）</span><span class="sxs-lookup"><span data-stu-id="8b516-113">**.NET Core cross-platform development** (under **Other Toolsets** )</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="8b516-114">创建新项目</span><span class="sxs-lookup"><span data-stu-id="8b516-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8b516-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b516-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="8b516-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b516-117">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-117">Open Visual Studio</span></span>
* <span data-ttu-id="8b516-118">单击“创建新项目”</span><span class="sxs-lookup"><span data-stu-id="8b516-118">Click **Create a new project**</span></span>
* <span data-ttu-id="8b516-119">选择带有 C# 标记的“控制台应用 (.NET Core)” ，然后单击“下一步”</span><span class="sxs-lookup"><span data-stu-id="8b516-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="8b516-120">输入“EFGetStarted”作为名称，然后单击“创建”</span><span class="sxs-lookup"><span data-stu-id="8b516-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="8b516-121">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8b516-121">Install Entity Framework Core</span></span>

<span data-ttu-id="8b516-122">要安装 EF Core，请为要作为目标对象的 EF Core 数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="8b516-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="8b516-123">本教程使用 SQLite 的原因是，它可在 .NET Core 支持的所有平台上运行。</span><span class="sxs-lookup"><span data-stu-id="8b516-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="8b516-124">有关可用提供程序的列表，请参阅[数据库提供程序](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="8b516-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8b516-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b516-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="8b516-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b516-127">“工具”>“NuGet 包管理器”>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="8b516-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="8b516-128">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8b516-128">Run the following commands:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="8b516-129">提示:还可以通过右键单击项目并选择“管理 NuGet 程序包”来安装包</span><span class="sxs-lookup"><span data-stu-id="8b516-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="8b516-130">创建模型</span><span class="sxs-lookup"><span data-stu-id="8b516-130">Create the model</span></span>

<span data-ttu-id="8b516-131">定义构成模型的上下文类和实体类。</span><span class="sxs-lookup"><span data-stu-id="8b516-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8b516-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b516-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="8b516-133">在项目文件夹中，使用以下代码创建 Model.cs</span><span class="sxs-lookup"><span data-stu-id="8b516-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="8b516-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b516-135">右键单击项目，然后选择“添加”>“类”</span><span class="sxs-lookup"><span data-stu-id="8b516-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="8b516-136">输入“Model.cs”作为名称，然后单击“添加”</span><span class="sxs-lookup"><span data-stu-id="8b516-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="8b516-137">将此文件的内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="8b516-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="8b516-138">EF Core 还可以从现有数据库对模型进行[反向工程](xref:core/managing-schemas/scaffolding)。</span><span class="sxs-lookup"><span data-stu-id="8b516-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="8b516-139">提示:为清楚起见，有意简化了此应用程序。</span><span class="sxs-lookup"><span data-stu-id="8b516-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="8b516-140">[连接字符串](xref:core/miscellaneous/connection-strings)不应存储在生产应用程序的代码中。</span><span class="sxs-lookup"><span data-stu-id="8b516-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="8b516-141">可能还需要将每个 C# 类拆分为其自己的文件。</span><span class="sxs-lookup"><span data-stu-id="8b516-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="8b516-142">创建数据库</span><span class="sxs-lookup"><span data-stu-id="8b516-142">Create the database</span></span>

<span data-ttu-id="8b516-143">以下步骤使用[迁移](xref:core/managing-schemas/migrations/index)创建数据库。</span><span class="sxs-lookup"><span data-stu-id="8b516-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8b516-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b516-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="8b516-145">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8b516-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="8b516-146">这会安装 [dotnet ef](xref:core/cli/dotnet) 和设计包，这是对项目运行命令所必需的。</span><span class="sxs-lookup"><span data-stu-id="8b516-146">This installs [dotnet ef](xref:core/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="8b516-147">`migrations` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="8b516-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="8b516-148">`database update` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="8b516-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="8b516-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b516-150">在“包管理器控制台(PMC)”中，运行以下命令</span><span class="sxs-lookup"><span data-stu-id="8b516-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="8b516-151">这会安装 [ EF Core 的 PMC 工具](xref:core/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="8b516-151">This installs the [PMC tools for EF Core](xref:core/cli/powershell).</span></span> <span data-ttu-id="8b516-152">`Add-Migration` 命令为迁移搭建基架，以便为模型创建一组初始表。</span><span class="sxs-lookup"><span data-stu-id="8b516-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="8b516-153">`Update-Database` 命令创建数据库并向其应用新的迁移。</span><span class="sxs-lookup"><span data-stu-id="8b516-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="8b516-154">创建、读取、更新和删除</span><span class="sxs-lookup"><span data-stu-id="8b516-154">Create, read, update & delete</span></span>

* <span data-ttu-id="8b516-155">打开 Program.cs 并将内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="8b516-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="8b516-156">运行应用</span><span class="sxs-lookup"><span data-stu-id="8b516-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8b516-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b516-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="8b516-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b516-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b516-159">运行 .NET Core 控制台应用时，Visual Studio 使用不一致的工作目录。</span><span class="sxs-lookup"><span data-stu-id="8b516-159">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="8b516-160">（请参阅 [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)）这会导致引发异常： *无此类表格：博客* 。</span><span class="sxs-lookup"><span data-stu-id="8b516-160">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="8b516-161">更新工作目录：</span><span class="sxs-lookup"><span data-stu-id="8b516-161">To update the working directory:</span></span>

* <span data-ttu-id="8b516-162">右键单击项目，并选择“编辑项目文件”</span><span class="sxs-lookup"><span data-stu-id="8b516-162">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="8b516-163">在 TargetFramework 属性下方，添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="8b516-163">Just below the *TargetFramework* property, add the following:</span></span>

  ```xml
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="8b516-164">保存该文件</span><span class="sxs-lookup"><span data-stu-id="8b516-164">Save the file</span></span>

<span data-ttu-id="8b516-165">现在可以运行应用：</span><span class="sxs-lookup"><span data-stu-id="8b516-165">Now you can run the app:</span></span>

* <span data-ttu-id="8b516-166">“调试”>“开始执行(不调试)”</span><span class="sxs-lookup"><span data-stu-id="8b516-166">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="8b516-167">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8b516-167">Next steps</span></span>

* <span data-ttu-id="8b516-168">按照 [ASP.NET Core 教程](/aspnet/core/data/ef-rp/intro)在 Web 应用中使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="8b516-168">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="8b516-169">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="8b516-169">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="8b516-170">[配置模型](xref:core/modeling/index)指定[必需项](xref:core/modeling/entity-properties#required-and-optional-properties)和[最大长度](xref:core/modeling/entity-properties#maximum-length)等内容</span><span class="sxs-lookup"><span data-stu-id="8b516-170">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="8b516-171">在更改模型后使用[迁移](xref:core/managing-schemas/migrations/index)来更新数据库架构</span><span class="sxs-lookup"><span data-stu-id="8b516-171">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
