---
title: Code First 新的数据库-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
ms.openlocfilehash: d540fc6e84049f345ae22998f94c309e0be73fc3
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182573"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="3a4d6-102">Code First 到新数据库</span><span class="sxs-lookup"><span data-stu-id="3a4d6-102">Code First to a New Database</span></span>
<span data-ttu-id="3a4d6-103">此视频和分步演练提供了面向新数据库的 Code First 开发的简介。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="3a4d6-104">此方案包括针对不存在的数据库和 Code First 将创建的数据库，或者 Code First 将向其中添加新表的空数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-104">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="3a4d6-105">Code First 允许使用 C\# 或 VB.Net 类定义模型。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="3a4d6-106">还可以选择使用类和属性上的属性或使用 Fluent API 来执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-106">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="3a4d6-107">观看视频</span><span class="sxs-lookup"><span data-stu-id="3a4d6-107">Watch the video</span></span>
<span data-ttu-id="3a4d6-108">此视频介绍了面向新数据库的 Code First 开发。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-108">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="3a4d6-109">此方案包括针对不存在的数据库和 Code First 将创建的数据库，或者 Code First 将向其中添加新表的空数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-109">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="3a4d6-110">Code First 允许使用C#或 VB.Net 类定义模型。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-110">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="3a4d6-111">还可以选择使用类和属性上的属性或使用 Fluent API 来执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-111">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="3a4d6-112">**主讲人**：[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="3a4d6-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="3a4d6-113">**视频**：[WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="3a4d6-113">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="3a4d6-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="3a4d6-114">Pre-Requisites</span></span>

<span data-ttu-id="3a4d6-115">需要至少安装 Visual Studio 2010 或 Visual Studio 2012 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="3a4d6-116">如果你使用的是 Visual Studio 2010，你还需要安装[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="3a4d6-117">1. 创建应用程序</span><span class="sxs-lookup"><span data-stu-id="3a4d6-117">1. Create the Application</span></span>

<span data-ttu-id="3a4d6-118">为了简单起见，我们将构建一个使用 Code First 执行数据访问的基本控制台应用程序。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-118">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="3a4d6-119">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a4d6-119">Open Visual Studio</span></span>
-   <span data-ttu-id="3a4d6-120">**文件-&gt;&gt; 项目 。**</span><span class="sxs-lookup"><span data-stu-id="3a4d6-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="3a4d6-121">从左侧菜单和**控制台应用程序**选择**Windows**</span><span class="sxs-lookup"><span data-stu-id="3a4d6-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="3a4d6-122">输入**CodeFirstNewDatabaseSample**作为名称</span><span class="sxs-lookup"><span data-stu-id="3a4d6-122">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="3a4d6-123">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="3a4d6-123">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="3a4d6-124">2. 创建模型</span><span class="sxs-lookup"><span data-stu-id="3a4d6-124">2. Create the Model</span></span>

<span data-ttu-id="3a4d6-125">让我们使用类定义一个非常简单的模型。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-125">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="3a4d6-126">我们只是在 Program.cs 文件中对其进行定义，但在实际应用程序中，您可以将类拆分为单独的文件，并可能是单独的项目。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-126">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="3a4d6-127">在 Program.cs 中的 Program 类定义下，添加以下两个类。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-127">Below the Program class definition in Program.cs add the following two classes.</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

<span data-ttu-id="3a4d6-128">你会注意到，我们正在建立两个导航属性（博客和博客）虚拟。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-128">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="3a4d6-129">这将启用实体框架的延迟加载功能。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-129">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="3a4d6-130">延迟加载是指在您尝试访问这些属性时，这些属性的内容将自动从数据库加载。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-130">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="3a4d6-131">3. 创建上下文</span><span class="sxs-lookup"><span data-stu-id="3a4d6-131">3. Create a Context</span></span>

<span data-ttu-id="3a4d6-132">现在可以定义一个派生上下文，它表示与数据库的会话，从而使我们能够查询和保存数据。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-132">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="3a4d6-133">我们定义了从 DbContext 派生的上下文，并为模型中的每个类公开了类型化的 DbSet&lt;TEntity&gt;。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-133">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="3a4d6-134">现在，我们开始使用实体框架中的类型，因此我们需要添加 EntityFramework NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-134">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="3a4d6-135">**项目–&gt; 管理 NuGet 程序包 。**</span><span class="sxs-lookup"><span data-stu-id="3a4d6-135">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="3a4d6-136">注意：如果你没有 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="3a4d6-136">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="3a4d6-137">选项你应安装[最新版本的 NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="3a4d6-137">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="3a4d6-138">选择 "**联机**" 选项卡</span><span class="sxs-lookup"><span data-stu-id="3a4d6-138">Select the **Online** tab</span></span>
-   <span data-ttu-id="3a4d6-139">选择**EntityFramework**包</span><span class="sxs-lookup"><span data-stu-id="3a4d6-139">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="3a4d6-140">单击 "**安装**"</span><span class="sxs-lookup"><span data-stu-id="3a4d6-140">Click **Install**</span></span>

<span data-ttu-id="3a4d6-141">在 Program.cs 的顶部为 system.string 添加 using 语句。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-141">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="3a4d6-142">在 Program.cs 中的 Post 类下面添加以下派生上下文。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-142">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="3a4d6-143">下面是 Program.cs 应该包含的完整列表。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-143">Here is a complete listing of what Program.cs should now contain.</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

<span data-ttu-id="3a4d6-144">这就是我们开始存储和检索数据所需的全部代码。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-144">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="3a4d6-145">很显然，幕后会出现很多情况，接下来我们将对此进行介绍，但首先我们来看看这一点。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-145">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="3a4d6-146">4. 读取 & 写入数据</span><span class="sxs-lookup"><span data-stu-id="3a4d6-146">4. Reading & Writing Data</span></span>

<span data-ttu-id="3a4d6-147">在 Program.cs 中实现 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-147">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="3a4d6-148">此代码创建一个新的上下文实例，然后使用它来插入新的博客。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-148">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="3a4d6-149">然后，它使用 LINQ 查询从按标题字母顺序排序的数据库中检索所有博客。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-149">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="3a4d6-150">你现在可以运行该应用程序并对其进行测试。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-150">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="3a4d6-151">我的数据在哪里？</span><span class="sxs-lookup"><span data-stu-id="3a4d6-151">Where’s My Data?</span></span>

<span data-ttu-id="3a4d6-152">按惯例，DbContext 已为您创建了一个数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-152">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="3a4d6-153">如果本地 SQL Express 实例可用（默认情况下随 Visual Studio 2010 一起安装），则 Code First 已在该实例上创建了数据库</span><span class="sxs-lookup"><span data-stu-id="3a4d6-153">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="3a4d6-154">如果 SQL Express 不可用，Code First 将尝试使用[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) （默认情况下安装 Visual Studio 2012）</span><span class="sxs-lookup"><span data-stu-id="3a4d6-154">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="3a4d6-155">数据库以派生上下文的完全限定名称命名，在本例中为**CodeFirstNewDatabaseSample. "bloggingcontext"**</span><span class="sxs-lookup"><span data-stu-id="3a4d6-155">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="3a4d6-156">这些只是默认的约定，有多种方法可用于更改 Code First 使用的数据库，有关详细信息，请访问**DbContext 如何发现模型和数据库连接**主题。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-156">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="3a4d6-157">你可以使用 Visual Studio 中的服务器资源管理器连接到此数据库</span><span class="sxs-lookup"><span data-stu-id="3a4d6-157">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="3a4d6-158">**视图-&gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="3a4d6-158">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="3a4d6-159">右键单击 "**数据连接**"，然后选择 "**添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="3a4d6-159">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="3a4d6-160">如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源</span><span class="sxs-lookup"><span data-stu-id="3a4d6-160">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![选择数据源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="3a4d6-162">连接到 LocalDB 或 SQL Express，具体取决于你安装的是哪个</span><span class="sxs-lookup"><span data-stu-id="3a4d6-162">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="3a4d6-163">现在，我们可以检查 Code First 创建的架构。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-163">We can now inspect the schema that Code First created.</span></span>

![架构初始](~/ef6/media/schemainitial.png)

<span data-ttu-id="3a4d6-165">DbContext 通过查看我们定义的 DbSet 属性来确定要包括在模型中的类。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-165">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="3a4d6-166">然后，它使用默认的 Code First 约定集来确定表和列的名称、确定数据类型、查找主键等。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-166">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="3a4d6-167">稍后在本演练中，我们将介绍如何覆盖这些约定。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-167">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="3a4d6-168">5. 处理模型更改</span><span class="sxs-lookup"><span data-stu-id="3a4d6-168">5. Dealing with Model Changes</span></span>

<span data-ttu-id="3a4d6-169">现在，可以对模型进行一些更改，当我们进行这些更改时，我们还需要更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-169">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="3a4d6-170">为此，我们将使用一项称为 Code First 迁移的功能，或使用较短的迁移。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-170">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="3a4d6-171">迁移允许我们提供一组有序的步骤，这些步骤描述如何升级（和降级）我们的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-171">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="3a4d6-172">其中的每个步骤（称为迁移）都包含一些描述要应用的更改的代码。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-172">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="3a4d6-173">第一步是为 "Bloggingcontext" 启用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-173">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="3a4d6-174">**工具-&gt; 库包管理器-&gt; 程序包管理器控制台**</span><span class="sxs-lookup"><span data-stu-id="3a4d6-174">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="3a4d6-175">在包管理器控制台中运行 Enable-Migrations 命令</span><span class="sxs-lookup"><span data-stu-id="3a4d6-175">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="3a4d6-176">已将新的迁移文件夹添加到包含两个项目的项目中：</span><span class="sxs-lookup"><span data-stu-id="3a4d6-176">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="3a4d6-177">**Configuration.cs** –此文件包含迁移将用于迁移 "bloggingcontext" 的设置。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-177">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="3a4d6-178">对于本演练，我们不需要更改任何内容，但可以在此处指定种子数据，注册其他数据库的提供程序，更改将在其中生成迁移的命名空间等。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-178">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="3a4d6-179">**&lt;时间戳&gt;\_InitialCreate.cs** –这是第一次迁移，它表示已应用到数据库的更改，以使其成为包含博客和文章表的数据库的空数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-179">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="3a4d6-180">尽管我们允许 Code First 自动为我们创建这些表，但现在我们已选择迁移，它们已转换为迁移。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-180">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="3a4d6-181">还在本地数据库中记录了已应用此迁移的 Code First。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-181">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="3a4d6-182">文件名上的时间戳用于排序目的。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-182">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="3a4d6-183">现在，我们来对模型进行更改，将 Url 属性添加到博客类：</span><span class="sxs-lookup"><span data-stu-id="3a4d6-183">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="3a4d6-184">在 Package Manager Console 中运行**Add-迁移 AddUrl**命令。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-184">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="3a4d6-185">添加迁移命令将检查自上次迁移后发生的更改，并基架找到的任何更改进行新迁移。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-185">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="3a4d6-186">我们可以为迁移指定一个名称;在此示例中，我们将调用迁移 "AddUrl"。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-186">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="3a4d6-187">基架代码是说，我们需要向 dbo 添加 Url 列，该 Url 列可以保存字符串数据。博客表。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-187">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="3a4d6-188">如果需要，我们可以编辑基架代码，但在这种情况下不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-188">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   <span data-ttu-id="3a4d6-189">在 Package Manager Console 中运行 "**更新数据库**" 命令。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-189">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="3a4d6-190">此命令会将任何挂起的迁移应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-190">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="3a4d6-191">已应用 InitialCreate 迁移，因此迁移只会应用新的 AddUrl 迁移。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-191">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="3a4d6-192">提示：若要查看正在对数据库执行的 SQL，可以在调用 Update-Database 时使用 **-Verbose**开关。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-192">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="3a4d6-193">此时，新的 Url 列将添加到数据库的 "博客" 表中：</span><span class="sxs-lookup"><span data-stu-id="3a4d6-193">The new Url column is now added to the Blogs table in the database:</span></span>

![带有 Url 的架构](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="3a4d6-195">6. 数据批注</span><span class="sxs-lookup"><span data-stu-id="3a4d6-195">6. Data Annotations</span></span>

<span data-ttu-id="3a4d6-196">到目前为止，我们只是让 EF 使用其默认约定来发现模型，但有时我们的类不遵循约定，我们需要能够进一步进行配置。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-196">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="3a4d6-197">有两个选项可供选择：我们将在本节中查看数据批注，然后在下一部分中查看 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-197">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="3a4d6-198">让我们向模型中添加一个用户类</span><span class="sxs-lookup"><span data-stu-id="3a4d6-198">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="3a4d6-199">我们还需要向派生的上下文添加一个集</span><span class="sxs-lookup"><span data-stu-id="3a4d6-199">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="3a4d6-200">如果我们尝试添加迁移，会收到一个错误，指出 "*EntityType" User "未定义键。为此 EntityType 定义密钥。 "*</span><span class="sxs-lookup"><span data-stu-id="3a4d6-200">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="3a4d6-201">因为 EF 无法知道用户名应为用户的主密钥。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-201">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="3a4d6-202">现在我们将使用数据批注，因此我们需要在 Program.cs 的顶部添加 using 语句</span><span class="sxs-lookup"><span data-stu-id="3a4d6-202">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="3a4d6-203">现在为用户名属性添加批注，以确定它是主键</span><span class="sxs-lookup"><span data-stu-id="3a4d6-203">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="3a4d6-204">使用**Add-迁移 AddUser**命令基架迁移，将这些更改应用到数据库</span><span class="sxs-lookup"><span data-stu-id="3a4d6-204">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="3a4d6-205">运行 "**更新-数据库**" 命令以将新迁移应用到数据库</span><span class="sxs-lookup"><span data-stu-id="3a4d6-205">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="3a4d6-206">新表现在已添加到数据库中：</span><span class="sxs-lookup"><span data-stu-id="3a4d6-206">The new table is now added to the database:</span></span>

![具有用户的架构](~/ef6/media/schemawithusers.png)

<span data-ttu-id="3a4d6-208">EF 支持的全部批注列表为：</span><span class="sxs-lookup"><span data-stu-id="3a4d6-208">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="3a4d6-209">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-209">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="3a4d6-210">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-210">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="3a4d6-211">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-211">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="3a4d6-212">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-212">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="3a4d6-213">有 requiredattribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-213">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="3a4d6-214">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-214">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="3a4d6-215">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-215">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="3a4d6-216">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-216">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="3a4d6-217">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-217">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="3a4d6-218">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-218">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="3a4d6-219">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-219">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="3a4d6-220">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-220">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="3a4d6-221">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="3a4d6-221">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="3a4d6-222">7. 熟知 API</span><span class="sxs-lookup"><span data-stu-id="3a4d6-222">7. Fluent API</span></span>

<span data-ttu-id="3a4d6-223">在上一部分中，我们介绍了如何使用数据批注来补充或替代约定检测到的内容。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-223">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="3a4d6-224">配置模型的另一种方法是通过 Code First Fluent API。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-224">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="3a4d6-225">大多数模型配置可以使用简单的数据批注完成。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-225">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="3a4d6-226">Fluent API 是一种更高级的方法，它指定模型配置，该配置涵盖了数据批注可以执行的所有操作以及数据批注无法实现的更高级配置。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-226">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="3a4d6-227">数据批注和 Fluent API 可一起使用。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-227">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="3a4d6-228">若要访问 Fluent API 可以重写 DbContext 中的 OnModelCreating 方法。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-228">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="3a4d6-229">假设我们要将用户名称栏存储在中的列重命名为显示\_名称。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-229">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="3a4d6-230">用以下代码替代 "Bloggingcontext" 上的 OnModelCreating 方法</span><span class="sxs-lookup"><span data-stu-id="3a4d6-230">Override the OnModelCreating method on BloggingContext with the following code</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   <span data-ttu-id="3a4d6-231">使用**Add-迁移 ChangeDisplayName**命令基架迁移，将这些更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-231">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="3a4d6-232">运行 "**更新-数据库**" 命令，以将新迁移应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-232">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="3a4d6-233">DisplayName 列现在已重命名为显示\_名称：</span><span class="sxs-lookup"><span data-stu-id="3a4d6-233">The DisplayName column is now renamed to display\_name:</span></span>

![重命名了显示名称的架构](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="3a4d6-235">摘要</span><span class="sxs-lookup"><span data-stu-id="3a4d6-235">Summary</span></span>

<span data-ttu-id="3a4d6-236">在本演练中，我们将使用新数据库查看 Code First 开发。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-236">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="3a4d6-237">我们使用类定义了一个模型，然后使用该模型来创建数据库并存储和检索数据。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-237">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="3a4d6-238">创建数据库后，我们使用 Code First 迁移来更改模型，因为模型已演化。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-238">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="3a4d6-239">我们还了解了如何使用数据批注和熟知的 API 配置模型。</span><span class="sxs-lookup"><span data-stu-id="3a4d6-239">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
