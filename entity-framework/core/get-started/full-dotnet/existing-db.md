---
title: .NET Framework 入门 - 现有数据库 - EF Core
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 1b90c491a3b2025da750a3266ff45d9d92bb1d0d
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688610"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a><span data-ttu-id="18228-102">通过现有数据库在 .NET Framework 上开始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="18228-102">Getting started with EF Core on .NET Framework with an Existing Database</span></span>

<span data-ttu-id="18228-103">在本教程中，你将构建一个控制台应用程序，它使用 Entity Framework 对 Microsoft SQL Server 数据库执行基本数据访问。</span><span class="sxs-lookup"><span data-stu-id="18228-103">In this tutorial, you build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="18228-104">通过对现有数据库进行反向工程，创建 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="18228-104">You create an Entity Framework model by reverse engineering an existing database.</span></span>

<span data-ttu-id="18228-105">[在 GitHub 上查看此文章的示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="18228-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18228-106">系统必备</span><span class="sxs-lookup"><span data-stu-id="18228-106">Prerequisites</span></span>

* [<span data-ttu-id="18228-107">Visual Studio 2017 版本 15.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="18228-107">Visual Studio 2017 version 15.7 or later</span></span>](https://www.visualstudio.com/downloads/)

## <a name="create-blogging-database"></a><span data-ttu-id="18228-108">创建博客数据库</span><span class="sxs-lookup"><span data-stu-id="18228-108">Create Blogging database</span></span>

<span data-ttu-id="18228-109">本教程使用 LocalDb 实例上的博客数据库作为现有数据库。</span><span class="sxs-lookup"><span data-stu-id="18228-109">This tutorial uses a **Blogging** database on the LocalDb instance as the existing database.</span></span> <span data-ttu-id="18228-110">如果已在其他教程中创建了博客数据库，请跳过这些步骤。</span><span class="sxs-lookup"><span data-stu-id="18228-110">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="18228-111">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18228-111">Open Visual Studio</span></span>

* <span data-ttu-id="18228-112">“工具”->“连接到数据库...”</span><span class="sxs-lookup"><span data-stu-id="18228-112">**Tools > Connect to Database...**</span></span>

* <span data-ttu-id="18228-113">选择“Microsoft SQL Server”，然后单击“继续”</span><span class="sxs-lookup"><span data-stu-id="18228-113">Select **Microsoft SQL Server** and click **Continue**</span></span>

* <span data-ttu-id="18228-114">输入“(localdb)\mssqllocaldb”作为服务器名称</span><span class="sxs-lookup"><span data-stu-id="18228-114">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>

* <span data-ttu-id="18228-115">输入“master”作为数据库名称，然后单击“确定”</span><span class="sxs-lookup"><span data-stu-id="18228-115">Enter **master** as the **Database Name** and click **OK**</span></span>

* <span data-ttu-id="18228-116">Master 数据库现在显示在“服务器资源管理器”的“数据连接”中</span><span class="sxs-lookup"><span data-stu-id="18228-116">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>

* <span data-ttu-id="18228-117">右键单击“服务器资源管理器”中的数据库，然后选择“新建查询”</span><span class="sxs-lookup"><span data-stu-id="18228-117">Right-click on the database in **Server Explorer** and select **New Query**</span></span>

* <span data-ttu-id="18228-118">将下列脚本复制到查询编辑器中</span><span class="sxs-lookup"><span data-stu-id="18228-118">Copy the script listed below into the query editor</span></span>

* <span data-ttu-id="18228-119">右键单击查询编辑器，然后选择“执行”</span><span class="sxs-lookup"><span data-stu-id="18228-119">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="18228-120">创建新项目</span><span class="sxs-lookup"><span data-stu-id="18228-120">Create a new project</span></span>

* <span data-ttu-id="18228-121">打开 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="18228-121">Open Visual Studio 2017</span></span>

* <span data-ttu-id="18228-122">“文件”>“新建”>“项目...”</span><span class="sxs-lookup"><span data-stu-id="18228-122">**File > New > Project...**</span></span>

* <span data-ttu-id="18228-123">从左侧菜单中选择“已安装”>“Visual C#”->“Windows Desktop”</span><span class="sxs-lookup"><span data-stu-id="18228-123">From the left menu select **Installed > Visual C# > Windows Desktop**</span></span>

* <span data-ttu-id="18228-124">选择“控制台应用(.NET Framework)”项目模板</span><span class="sxs-lookup"><span data-stu-id="18228-124">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="18228-125">确保项目面向 **.NET Framework 4.6.1** 或更高版本</span><span class="sxs-lookup"><span data-stu-id="18228-125">Make sure that the project targets **.NET Framework 4.6.1** or later</span></span>

* <span data-ttu-id="18228-126">将项目命名为 ConsoleApp.ExistingDb，并单击“确定”</span><span class="sxs-lookup"><span data-stu-id="18228-126">Name the project *ConsoleApp.ExistingDb* and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="18228-127">安装 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="18228-127">Install Entity Framework</span></span>

<span data-ttu-id="18228-128">要使用 EF Core，请为要作为目标对象的数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="18228-128">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="18228-129">本教程使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="18228-129">This tutorial uses SQL Server.</span></span> <span data-ttu-id="18228-130">有关可用提供程序的列表，请参阅[数据库提供程序](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="18228-130">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="18228-131">“工具”>“NuGet 包管理器”>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="18228-131">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="18228-132">运行 `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span><span class="sxs-lookup"><span data-stu-id="18228-132">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="18228-133">下一步，要使用一些 Entity Framework Tools 对该数据库进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="18228-133">In the next step, you use some Entity Framework Tools to reverse engineer the database.</span></span> <span data-ttu-id="18228-134">因此，请同时安装该工具包。</span><span class="sxs-lookup"><span data-stu-id="18228-134">So install the tools package as well.</span></span>

* <span data-ttu-id="18228-135">运行 `Install-Package Microsoft.EntityFrameworkCore.Tools`</span><span class="sxs-lookup"><span data-stu-id="18228-135">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="reverse-engineer-the-model"></a><span data-ttu-id="18228-136">对模型实施反向工程</span><span class="sxs-lookup"><span data-stu-id="18228-136">Reverse engineer the model</span></span>

<span data-ttu-id="18228-137">现在是时候基于现有数据库创建 EF 模型了。</span><span class="sxs-lookup"><span data-stu-id="18228-137">Now it's time to create the EF model based on an existing database.</span></span>

* <span data-ttu-id="18228-138">“工具”–>“NuGet 包管理器”–>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="18228-138">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>

* <span data-ttu-id="18228-139">运行以下命令以从现有数据库创建模型</span><span class="sxs-lookup"><span data-stu-id="18228-139">Run the following command to create a model from the existing database</span></span>

  ``` powershell
  Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
  ```

> [!TIP]  
> <span data-ttu-id="18228-140">可以通过将 `-Tables` 参数添加到上述命令来指定要为其生成实体的表。</span><span class="sxs-lookup"><span data-stu-id="18228-140">You can specify the tables to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="18228-141">例如 `-Tables Blog,Post`。</span><span class="sxs-lookup"><span data-stu-id="18228-141">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="18228-142">反向工程过程基于现有数据库的架构创建实体类（`Blog` 和 `Post`）和派生上下文（`BloggingContext`）。</span><span class="sxs-lookup"><span data-stu-id="18228-142">The reverse engineer process created entity classes (`Blog` and `Post`) and a derived context (`BloggingContext`) based on the schema of the existing database.</span></span>

<span data-ttu-id="18228-143">实体类是简单的 C# 对象，代表要查询和保存的数据。</span><span class="sxs-lookup"><span data-stu-id="18228-143">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="18228-144">以下是 `Blog` 和 `Post` 实体类：</span><span class="sxs-lookup"><span data-stu-id="18228-144">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Post.cs)]

> [!TIP]  
> <span data-ttu-id="18228-145">若要启用延迟加载，可以创建导航属性 `virtual`（Blog.Post 和 Post.Blog）。</span><span class="sxs-lookup"><span data-stu-id="18228-145">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

<span data-ttu-id="18228-146">上下文表示与数据库的会话。</span><span class="sxs-lookup"><span data-stu-id="18228-146">The context represents a session with the database.</span></span> <span data-ttu-id="18228-147">其中有可用于查询和保存实体类实例的方法。</span><span class="sxs-lookup"><span data-stu-id="18228-147">It has methods that you can use to query and save instances of the entity classes.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)]

## <a name="use-the-model"></a><span data-ttu-id="18228-148">使用模型</span><span class="sxs-lookup"><span data-stu-id="18228-148">Use the model</span></span>

<span data-ttu-id="18228-149">现在可以使用模型执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="18228-149">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="18228-150">打开 Program.cs</span><span class="sxs-lookup"><span data-stu-id="18228-150">Open *Program.cs*</span></span>

* <span data-ttu-id="18228-151">将此文件的内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="18228-151">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] 

* <span data-ttu-id="18228-152">“调试”>“开始执行(不调试)”</span><span class="sxs-lookup"><span data-stu-id="18228-152">Debug > Start Without Debugging</span></span>

  <span data-ttu-id="18228-153">可以看到，把一个博客保存到数据库后，所有博客的详细信息都将显示在控制台中。</span><span class="sxs-lookup"><span data-stu-id="18228-153">You see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

  ![图像](_static/output-existing-db.png)

## <a name="next-steps"></a><span data-ttu-id="18228-155">后续步骤</span><span class="sxs-lookup"><span data-stu-id="18228-155">Next steps</span></span>

<span data-ttu-id="18228-156">有关如何构建上下文和实体类的详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="18228-156">For more information about how to scaffold a context and entity classes, see the following articles:</span></span>
* [<span data-ttu-id="18228-157">反向工程</span><span class="sxs-lookup"><span data-stu-id="18228-157">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
* [<span data-ttu-id="18228-158">Entity Framework Core 工具参考 - .NET CLI</span><span class="sxs-lookup"><span data-stu-id="18228-158">Entity Framework Core tools reference - .NET CLI</span></span>](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [<span data-ttu-id="18228-159">Entity Framework Core 工具参考 - 程序包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="18228-159">Entity Framework Core tools reference - Package Manager Console</span></span>](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)