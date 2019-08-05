---
title: ASP.NET Core 入门 - 现有数据库 - EF Core
author: rowanmiller
description: 通过现有数据库在 ASP.NET Core 上开始使用 EF Core
ms.date: 08/02/2018
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: 6b0ed0a9222644bee31d23234aa27b2084137f4a
ms.sourcegitcommit: 755a15a789631cc4ea581e2262a2dcc49c219eef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497515"
---
# <a name="get-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a><span data-ttu-id="eb8a6-103">通过现有数据库在 ASP.NET Core 上开始使用 EF Core</span><span class="sxs-lookup"><span data-stu-id="eb8a6-103">Get started with EF Core on ASP.NET Core with an existing database</span></span>

<span data-ttu-id="eb8a6-104">在本教程中，你将使用 Entity Framework Core 构建执行基本数据访问的 ASP.NET Core MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-104">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="eb8a6-105">要对现有数据库进行反向工程，以创建 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-105">You reverse engineer an existing database to create an Entity Framework model.</span></span>

<span data-ttu-id="eb8a6-106">[在 GitHub 上查看此文章的示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-106">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb8a6-107">系统必备</span><span class="sxs-lookup"><span data-stu-id="eb8a6-107">Prerequisites</span></span>

<span data-ttu-id="eb8a6-108">安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="eb8a6-108">Install the following software:</span></span>

* <span data-ttu-id="eb8a6-109">具有以下工作负载的 [Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="eb8a6-109">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="eb8a6-110">“ASP.NET 和 Web 开发”  （位于“Web 和云”  下）</span><span class="sxs-lookup"><span data-stu-id="eb8a6-110">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="eb8a6-111">“.NET Core 跨平台开发”  （位于“其他工具集”  下）</span><span class="sxs-lookup"><span data-stu-id="eb8a6-111">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="eb8a6-112">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="eb8a6-112">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="create-blogging-database"></a><span data-ttu-id="eb8a6-113">创建博客数据库</span><span class="sxs-lookup"><span data-stu-id="eb8a6-113">Create Blogging database</span></span>

<span data-ttu-id="eb8a6-114">本教程使用 LocalDb 实例上的博客  数据库作为现有数据库。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-114">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span> <span data-ttu-id="eb8a6-115">如果已在其他教程中创建了博客  数据库，请跳过这些步骤。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-115">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="eb8a6-116">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb8a6-116">Open Visual Studio</span></span>
* <span data-ttu-id="eb8a6-117">“工具”->“连接到数据库...” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-117">**Tools -> Connect to Database...**</span></span>
* <span data-ttu-id="eb8a6-118">选择“Microsoft SQL Server”  ，然后单击“继续” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-118">Select **Microsoft SQL Server** and click **Continue**</span></span>
* <span data-ttu-id="eb8a6-119">输入“(localdb)\mssqllocaldb”  作为服务器名称 </span><span class="sxs-lookup"><span data-stu-id="eb8a6-119">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>
* <span data-ttu-id="eb8a6-120">输入“master”  作为数据库名称  ，然后单击“确定” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-120">Enter **master** as the **Database Name** and click **OK**</span></span>
* <span data-ttu-id="eb8a6-121">Master 数据库现在显示在“服务器资源管理器”的“数据连接”  中 </span><span class="sxs-lookup"><span data-stu-id="eb8a6-121">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>
* <span data-ttu-id="eb8a6-122">右键单击“服务器资源管理器”中的  数据库，然后选择“新建查询” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-122">Right-click on the database in **Server Explorer** and select **New Query**</span></span>
* <span data-ttu-id="eb8a6-123">将下列脚本复制到查询编辑器中</span><span class="sxs-lookup"><span data-stu-id="eb8a6-123">Copy the script listed below into the query editor</span></span>
* <span data-ttu-id="eb8a6-124">右键单击查询编辑器，然后选择“执行” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-124">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="eb8a6-125">创建新项目</span><span class="sxs-lookup"><span data-stu-id="eb8a6-125">Create a new project</span></span>

* <span data-ttu-id="eb8a6-126">打开 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="eb8a6-126">Open Visual Studio 2017</span></span>
* <span data-ttu-id="eb8a6-127">“文件”>“新建”>“项目...” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-127">**File > New > Project...**</span></span>
* <span data-ttu-id="eb8a6-128">从左菜单中选择“已安装”>“Visual C#”>“Web”  。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-128">From the left menu select **Installed > Visual C# > Web**</span></span>
* <span data-ttu-id="eb8a6-129">选择“ASP.NET Core Web 应用程序”项目模板 </span><span class="sxs-lookup"><span data-stu-id="eb8a6-129">Select the **ASP.NET Core Web Application** project template</span></span>
* <span data-ttu-id="eb8a6-130">输入 EFGetStarted.AspNetCore.ExistingDb  作为名称（它必须完全匹配稍后在代码中使用的命名空间），再单击“确定” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-130">Enter **EFGetStarted.AspNetCore.ExistingDb** as the name (it has to match exactly the namespace later used in the code) and click **OK**</span></span> 
* <span data-ttu-id="eb8a6-131">等待“新建 ASP.NET Core Web 应用程序”对话框显示出来 </span><span class="sxs-lookup"><span data-stu-id="eb8a6-131">Wait for the **New ASP.NET Core Web Application** dialog to appear</span></span>
* <span data-ttu-id="eb8a6-132">确保目标框架下拉列表设置为 .NET Core，版本下拉列表设置为 ASP.NET Core 2.1  </span><span class="sxs-lookup"><span data-stu-id="eb8a6-132">Make sure that the target framework dropdown is set to **.NET Core**, and the version dropdown is set to **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="eb8a6-133">选择“Web 应用程序(模型视图控制器)”  模板</span><span class="sxs-lookup"><span data-stu-id="eb8a6-133">Select the **Web Application (Model-View-Controller)** template</span></span>
* <span data-ttu-id="eb8a6-134">确保将“身份验证”  设置为“无身份验证” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-134">Ensure that **Authentication** is set to **No Authentication**</span></span>
* <span data-ttu-id="eb8a6-135">单击“确定” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-135">Click **OK**</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="eb8a6-136">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="eb8a6-136">Install Entity Framework Core</span></span>

<span data-ttu-id="eb8a6-137">要安装 EF Core，请为要作为目标对象的 EF Core 数据库提供程序安装程序包。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="eb8a6-138">有关可用提供程序的列表，请参阅[数据库提供程序](../../providers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-138">For a list of available providers see [Database Providers](../../providers/index.md).</span></span> 

<span data-ttu-id="eb8a6-139">对于本教程，无需安装提供程序包，因为本教程使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-139">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="eb8a6-140">SQL Server 提供程序包包含在 [Microsoft.AspnetCore.App 元包](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)中。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-140">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="eb8a6-141">对模型实施反向工程</span><span class="sxs-lookup"><span data-stu-id="eb8a6-141">Reverse engineer your model</span></span>

<span data-ttu-id="eb8a6-142">现在是时候基于现有数据库创建 EF 模型了。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-142">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="eb8a6-143"> “工具”–>“NuGet 包管理器”–>“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="eb8a6-143">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="eb8a6-144">运行以下命令以从现有数据库创建模型：</span><span class="sxs-lookup"><span data-stu-id="eb8a6-144">Run the following command to create a model from the existing database:</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="eb8a6-145">如果收到错误，指出 `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`，请关闭并重新打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-145">If you receive an error stating `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`, then close and reopen Visual Studio.</span></span>

> [!TIP]  
> <span data-ttu-id="eb8a6-146">可以通过将 `-Tables` 参数添加到上述命令来指定要为哪些表生成实体。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-146">You can specify which tables you want to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="eb8a6-147">例如 `-Tables Blog,Post`。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-147">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="eb8a6-148">反向工程过程基于现有数据库的架构创建实体类 (`Blog.cs` & `Post.cs`) 和派生上下文 (`BloggingContext.cs`)。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-148">The reverse engineer process created entity classes (`Blog.cs` & `Post.cs`) and a derived context (`BloggingContext.cs`) based on the schema of the existing database.</span></span>

 <span data-ttu-id="eb8a6-149">实体类是简单的 C# 对象，代表要查询和保存的数据。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-149">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="eb8a6-150">以下是 `Blog` 和 `Post` 实体类：</span><span class="sxs-lookup"><span data-stu-id="eb8a6-150">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Post.cs)]

> [!TIP]  
> <span data-ttu-id="eb8a6-151">若要启用延迟加载，可以创建导航属性 `virtual`（Blog.Post 和 Post.Blog）。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-151">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

 <span data-ttu-id="eb8a6-152">上下文表示与数据库的会话，并允许查询和保存实体类的实例。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-152">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- Static code listing, rather than a linked file, because the tutorial modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public BloggingContext()
    {
    }

    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    {
    }

    public virtual DbSet<Blog> Blog { get; set; }
    public virtual DbSet<Post> Post { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>(entity =>
        {
            entity.Property(e => e.Url).IsRequired();
        });

        modelBuilder.Entity<Post>(entity =>
        {
            entity.HasOne(d => d.Blog)
                .WithMany(p => p.Post)
                .HasForeignKey(d => d.BlogId);
        });
    }
}
```

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="eb8a6-153">通过依赖关系注入注册上下文</span><span class="sxs-lookup"><span data-stu-id="eb8a6-153">Register your context with dependency injection</span></span>

<span data-ttu-id="eb8a6-154">依赖关系注入的概念是 ASP.NET Core 的核心。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-154">The concept of dependency injection is central to ASP.NET Core.</span></span> <span data-ttu-id="eb8a6-155">服务（例如 `BloggingContext`）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-155">Services (such as `BloggingContext`) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="eb8a6-156">然后，通过构造函数参数或属性为需要这些服务的组件（如 MVC 控制器）提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-156">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span> <span data-ttu-id="eb8a6-157">有关依赖关系注入的详细信息，请参阅 ASP.NET 网站上的文章[依赖关系注入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-157">For more information on dependency injection see the [Dependency Injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) article on the ASP.NET site.</span></span>

### <a name="register-and-configure-your-context-in-startupcs"></a><span data-ttu-id="eb8a6-158">在 Startup.cs 中注册并配置上下文</span><span class="sxs-lookup"><span data-stu-id="eb8a6-158">Register and configure your context in Startup.cs</span></span>

<span data-ttu-id="eb8a6-159">若要使 `BloggingContext` 对 MVC 控制器可用，请将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-159">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

* <span data-ttu-id="eb8a6-160">打开 Startup.cs </span><span class="sxs-lookup"><span data-stu-id="eb8a6-160">Open **Startup.cs**</span></span>
* <span data-ttu-id="eb8a6-161">在文件开头添加以下 `using` 语句</span><span class="sxs-lookup"><span data-stu-id="eb8a6-161">Add the following `using` statements at the start of the file</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

<span data-ttu-id="eb8a6-162">现在，可以使用 `AddDbContext(...)` 方法将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-162">Now you can use the `AddDbContext(...)` method to register it as a service.</span></span>
* <span data-ttu-id="eb8a6-163">找到 `ConfigureServices(...)` 方法</span><span class="sxs-lookup"><span data-stu-id="eb8a6-163">Locate the `ConfigureServices(...)` method</span></span>
* <span data-ttu-id="eb8a6-164">添加以下突出显示的代码以将上下文注册为服务</span><span class="sxs-lookup"><span data-stu-id="eb8a6-164">Add the following highlighted code to register the context as a service</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=14-15)]

> [!TIP]  
> <span data-ttu-id="eb8a6-165">在实际的应用程序中，通常会将连接字符串置于配置文件或环境变量中。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-165">In a real application you would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="eb8a6-166">为简单起见，本教程要你在代码中定义它。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-166">For the sake of simplicity, this tutorial has you define it in code.</span></span> <span data-ttu-id="eb8a6-167">有关详细信息，请参阅[连接字符串](../../miscellaneous/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-167">For more information, see [Connection Strings](../../miscellaneous/connection-strings.md).</span></span>

## <a name="create-a-controller-and-views"></a><span data-ttu-id="eb8a6-168">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="eb8a6-168">Create a controller and views</span></span>

* <span data-ttu-id="eb8a6-169">在“解决方案资源管理器”中，右键单击“控制器”文件夹，然后选择“添加”->“控制器...”   </span><span class="sxs-lookup"><span data-stu-id="eb8a6-169">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="eb8a6-170">选择“视图使用 Entity Framework 的 MVC 控制器”，然后单击“确定”  </span><span class="sxs-lookup"><span data-stu-id="eb8a6-170">Select **MVC Controller with views, using Entity Framework** and click **Ok**</span></span>
* <span data-ttu-id="eb8a6-171">将“模型类”  设置为“Blog”  ，将“数据上下文类”  设置为“BloggingContext” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-171">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**</span></span>
* <span data-ttu-id="eb8a6-172">单击“添加” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-172">Click **Add**</span></span>

## <a name="run-the-application"></a><span data-ttu-id="eb8a6-173">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="eb8a6-173">Run the application</span></span>

<span data-ttu-id="eb8a6-174">现在可以运行应用程序来查看其实际运行情况。</span><span class="sxs-lookup"><span data-stu-id="eb8a6-174">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="eb8a6-175">“调试”->“开始执行(不调试)” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-175">**Debug -> Start Without Debugging**</span></span>
* <span data-ttu-id="eb8a6-176">应用程序将生成并在 Web 浏览器中打开</span><span class="sxs-lookup"><span data-stu-id="eb8a6-176">The application builds and opens in a web browser</span></span>
* <span data-ttu-id="eb8a6-177">导航到 `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="eb8a6-177">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="eb8a6-178">单击“新建” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-178">Click **Create New**</span></span>
* <span data-ttu-id="eb8a6-179">输入新博客的 Url  ，然后单击“创建” </span><span class="sxs-lookup"><span data-stu-id="eb8a6-179">Enter a **Url** for the new blog and click **Create**</span></span>

  ![创建页面](_static/create.png)

  ![索引页](_static/index-existing-db.png)

## <a name="next-steps"></a><span data-ttu-id="eb8a6-182">后续步骤</span><span class="sxs-lookup"><span data-stu-id="eb8a6-182">Next steps</span></span>

<span data-ttu-id="eb8a6-183">有关如何构建上下文和实体类的详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="eb8a6-183">For more information about how to scaffold a context and entity classes, see the following articles:</span></span>
* [<span data-ttu-id="eb8a6-184">反向工程</span><span class="sxs-lookup"><span data-stu-id="eb8a6-184">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
* [<span data-ttu-id="eb8a6-185">Entity Framework Core 工具参考 - .NET CLI</span><span class="sxs-lookup"><span data-stu-id="eb8a6-185">Entity Framework Core tools reference - .NET CLI</span></span>](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [<span data-ttu-id="eb8a6-186">Entity Framework Core 工具参考 - 程序包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="eb8a6-186">Entity Framework Core tools reference - Package Manager Console</span></span>](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)
