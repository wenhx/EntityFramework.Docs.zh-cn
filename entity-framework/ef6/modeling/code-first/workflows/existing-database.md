---
title: Code First 现有数据库-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
ms.openlocfilehash: 0a51f826422d7e2bff33b968605eace1e754c425
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415597"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="3b60f-102">Code First 到现有数据库</span><span class="sxs-lookup"><span data-stu-id="3b60f-102">Code First to an Existing Database</span></span>
<span data-ttu-id="3b60f-103">此视频和分步演练提供了面向现有数据库 Code First 开发的简介。</span><span class="sxs-lookup"><span data-stu-id="3b60f-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="3b60f-104">Code First 允许使用 C\# 或 VB.Net 类定义模型。</span><span class="sxs-lookup"><span data-stu-id="3b60f-104">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="3b60f-105">另外，还可以使用类和属性上的属性或使用 Fluent API 来执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="3b60f-105">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="3b60f-106">观看视频</span><span class="sxs-lookup"><span data-stu-id="3b60f-106">Watch the video</span></span>
<span data-ttu-id="3b60f-107">此视频[现在适用于第9频道](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)。</span><span class="sxs-lookup"><span data-stu-id="3b60f-107">This video is [now available on Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="3b60f-108">先决条件</span><span class="sxs-lookup"><span data-stu-id="3b60f-108">Pre-Requisites</span></span>

<span data-ttu-id="3b60f-109">你将需要安装**Visual Studio 2012**或**Visual Studio 2013**才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="3b60f-109">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="3b60f-110">还需要安装**适用于 Visual Studio 的 Entity Framework Tools** **6.1** （或更高版本）。</span><span class="sxs-lookup"><span data-stu-id="3b60f-110">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="3b60f-111">有关安装最新版本的 Entity Framework Tools 的信息，请参阅[获取实体框架](~/ef6/fundamentals/install.md)。</span><span class="sxs-lookup"><span data-stu-id="3b60f-111">See [Get Entity Framework](~/ef6/fundamentals/install.md) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="3b60f-112">1. 创建现有数据库</span><span class="sxs-lookup"><span data-stu-id="3b60f-112">1. Create an Existing Database</span></span>

<span data-ttu-id="3b60f-113">通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="3b60f-113">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="3b60f-114">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="3b60f-114">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="3b60f-115">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b60f-115">Open Visual Studio</span></span>
-   <span data-ttu-id="3b60f-116">**视图-&gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="3b60f-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="3b60f-117">右键单击 "**数据连接-&gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="3b60f-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="3b60f-118">如果尚未从**服务器资源管理器**连接到数据库，则需要选择**Microsoft SQL Server**作为数据源</span><span class="sxs-lookup"><span data-stu-id="3b60f-118">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![选择数据源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="3b60f-120">连接到 LocalDB 实例，并输入**博客**作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="3b60f-120">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![LocalDB 连接](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="3b60f-122">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="3b60f-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    !["创建数据库" 对话框](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="3b60f-124">新数据库现在将出现在服务器资源管理器中，右键单击该数据库并选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="3b60f-124">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="3b60f-125">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="3b60f-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a><span data-ttu-id="3b60f-126">2. 创建应用程序</span><span class="sxs-lookup"><span data-stu-id="3b60f-126">2. Create the Application</span></span>

<span data-ttu-id="3b60f-127">为简单起见，我们将构建一个使用 Code First 进行数据访问的基本控制台应用程序：</span><span class="sxs-lookup"><span data-stu-id="3b60f-127">To keep things simple we will build a basic console application that uses Code First to do the data access:</span></span>

-   <span data-ttu-id="3b60f-128">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b60f-128">Open Visual Studio</span></span>
-   <span data-ttu-id="3b60f-129">**文件-&gt;&gt; 项目 。**</span><span class="sxs-lookup"><span data-stu-id="3b60f-129">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="3b60f-130">从左侧菜单和**控制台应用程序**选择**Windows**</span><span class="sxs-lookup"><span data-stu-id="3b60f-130">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="3b60f-131">输入**CodeFirstExistingDatabaseSample**作为名称</span><span class="sxs-lookup"><span data-stu-id="3b60f-131">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="3b60f-132">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="3b60f-132">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="3b60f-133">3. 反向工程模型</span><span class="sxs-lookup"><span data-stu-id="3b60f-133">3. Reverse Engineer Model</span></span>

<span data-ttu-id="3b60f-134">我们将使用适用于 Visual Studio 的 Entity Framework Tools 帮助我们生成一些用于映射到数据库的初始代码。</span><span class="sxs-lookup"><span data-stu-id="3b60f-134">We will use the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="3b60f-135">这些工具只是生成代码，你也可以根据需要手动键入代码。</span><span class="sxs-lookup"><span data-stu-id="3b60f-135">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="3b60f-136">**项目-&gt; "添加新项 ..."**</span><span class="sxs-lookup"><span data-stu-id="3b60f-136">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="3b60f-137">从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="3b60f-137">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="3b60f-138">输入 **"bloggingcontext"** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="3b60f-138">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="3b60f-139">这将启动**实体数据模型向导**</span><span class="sxs-lookup"><span data-stu-id="3b60f-139">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="3b60f-140">选择 "**从数据库 Code First** "，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="3b60f-140">Select **Code First from Database** and click **Next**</span></span>

    ![向导 One CFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="3b60f-142">选择与在第一部分中创建的数据库的连接，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="3b60f-142">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![向导两个 CFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="3b60f-144">单击 "**表**" 旁边的复选框以导入所有表，然后单击 "**完成**"</span><span class="sxs-lookup"><span data-stu-id="3b60f-144">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![向导三个 CFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="3b60f-146">反向工程过程完成后，会向项目中添加大量项目，让我们看看添加的内容。</span><span class="sxs-lookup"><span data-stu-id="3b60f-146">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="3b60f-147">配置文件</span><span class="sxs-lookup"><span data-stu-id="3b60f-147">Configuration file</span></span>

<span data-ttu-id="3b60f-148">已将 App.config 文件添加到项目中，此文件包含现有数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="3b60f-148">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="3b60f-149">*你还会注意到配置文件中的其他一些设置，这些是默认的 EF 设置，告诉 Code First 在何处创建数据库。由于我们要映射到现有数据库，因此在应用程序中将忽略这些设置。*</span><span class="sxs-lookup"><span data-stu-id="3b60f-149">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="3b60f-150">派生上下文</span><span class="sxs-lookup"><span data-stu-id="3b60f-150">Derived Context</span></span>

<span data-ttu-id="3b60f-151">已将 **"bloggingcontext"** 类添加到项目。</span><span class="sxs-lookup"><span data-stu-id="3b60f-151">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="3b60f-152">上下文表示与数据库的会话，从而使我们能够查询并保存数据。</span><span class="sxs-lookup"><span data-stu-id="3b60f-152">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="3b60f-153">上下文公开模型中每个类型的**DbSet&lt;TEntity&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="3b60f-153">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="3b60f-154">你还会注意到，默认构造函数使用**名称 =** 语法调用基构造函数。</span><span class="sxs-lookup"><span data-stu-id="3b60f-154">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="3b60f-155">这会告知 Code First 应从配置文件加载要用于此上下文的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="3b60f-155">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

<span data-ttu-id="3b60f-156">*使用配置文件中的连接字符串时，应始终使用\*\*name =*\* 语法。这可以确保在连接字符串不存在时，实体框架将引发，而不是按约定创建新数据库。\*</span><span class="sxs-lookup"><span data-stu-id="3b60f-156">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="3b60f-157">模型类</span><span class="sxs-lookup"><span data-stu-id="3b60f-157">Model classes</span></span>

<span data-ttu-id="3b60f-158">最后，还将**博客**和**Post**类添加到了项目中。</span><span class="sxs-lookup"><span data-stu-id="3b60f-158">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="3b60f-159">这些是构成模型的域类。</span><span class="sxs-lookup"><span data-stu-id="3b60f-159">These are the domain classes that make up our model.</span></span> <span data-ttu-id="3b60f-160">你将看到应用于类的数据批注，用于指定 Code First 约定不与现有数据库的结构一致的配置。</span><span class="sxs-lookup"><span data-stu-id="3b60f-160">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="3b60f-161">例如，你将在**Blog.Name**和**Blog**上看到**StringLength**批注，因为它们在数据库中的最大长度为**200** （Code First 默认值是使用 SQL Server）中的数据库提供程序（ **nvarchar （max））** 支持的多长度。</span><span class="sxs-lookup"><span data-stu-id="3b60f-161">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a><span data-ttu-id="3b60f-162">4. 读取 & 写入数据</span><span class="sxs-lookup"><span data-stu-id="3b60f-162">4. Reading & Writing Data</span></span>

<span data-ttu-id="3b60f-163">现在，我们有了一个模型，可以使用它来访问某些数据了。</span><span class="sxs-lookup"><span data-stu-id="3b60f-163">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="3b60f-164">在**Program.cs**中实现**Main**方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="3b60f-164">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="3b60f-165">此代码创建一个新的上下文实例，然后使用它来插入新的**博客**。</span><span class="sxs-lookup"><span data-stu-id="3b60f-165">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="3b60f-166">然后，它使用 LINQ 查询从按**标题**字母顺序排序的数据库中检索所有**博客**。</span><span class="sxs-lookup"><span data-stu-id="3b60f-166">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="3b60f-167">现在可以运行应用程序并对其进行测试。</span><span class="sxs-lookup"><span data-stu-id="3b60f-167">You can now run the application and test it.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="3b60f-168">如果数据库发生了更改，该怎么办？</span><span class="sxs-lookup"><span data-stu-id="3b60f-168">What if My Database Changes?</span></span>

<span data-ttu-id="3b60f-169">"Code First 到数据库" 向导旨在生成一组可以进行调整和修改的起始点。</span><span class="sxs-lookup"><span data-stu-id="3b60f-169">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="3b60f-170">如果数据库架构发生更改，则可以手动编辑类，也可以执行其他反向工程来覆盖类。</span><span class="sxs-lookup"><span data-stu-id="3b60f-170">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="3b60f-171">使用现有数据库的 Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="3b60f-171">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="3b60f-172">如果要对现有数据库使用 Code First 迁移，请参阅[Code First 迁移到现有数据库](~/ef6/modeling/code-first/migrations/existing-database.md)。</span><span class="sxs-lookup"><span data-stu-id="3b60f-172">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](~/ef6/modeling/code-first/migrations/existing-database.md).</span></span>

## <a name="summary"></a><span data-ttu-id="3b60f-173">摘要</span><span class="sxs-lookup"><span data-stu-id="3b60f-173">Summary</span></span>

<span data-ttu-id="3b60f-174">在本演练中，我们使用现有数据库查看 Code First 开发。</span><span class="sxs-lookup"><span data-stu-id="3b60f-174">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="3b60f-175">我们使用了 Visual Studio Entity Framework Tools 来反向工程映射到数据库并可用于存储和检索数据的一组类。</span><span class="sxs-lookup"><span data-stu-id="3b60f-175">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
