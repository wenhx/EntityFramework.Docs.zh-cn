---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415171"
---
# <a name="database-first"></a><span data-ttu-id="be58f-102">Database First</span><span class="sxs-lookup"><span data-stu-id="be58f-102">Database First</span></span>
<span data-ttu-id="be58f-103">此视频和分步演练提供使用实体框架 Database First 开发的简介。</span><span class="sxs-lookup"><span data-stu-id="be58f-103">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="be58f-104">Database First 允许从现有数据库对模型进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="be58f-104">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="be58f-105">模型存储在 EDMX 文件（.edmx 扩展名）中，可在 Entity Framework Designer 中查看和编辑。</span><span class="sxs-lookup"><span data-stu-id="be58f-105">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="be58f-106">在应用程序中与之交互的类将自动从 EDMX 文件生成。</span><span class="sxs-lookup"><span data-stu-id="be58f-106">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="be58f-107">观看视频</span><span class="sxs-lookup"><span data-stu-id="be58f-107">Watch the video</span></span>
<span data-ttu-id="be58f-108">此视频介绍了使用实体框架进行 Database First 开发的简介。</span><span class="sxs-lookup"><span data-stu-id="be58f-108">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="be58f-109">Database First 允许从现有数据库对模型进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="be58f-109">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="be58f-110">模型存储在 EDMX 文件（.edmx 扩展名）中，可在 Entity Framework Designer 中查看和编辑。</span><span class="sxs-lookup"><span data-stu-id="be58f-110">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="be58f-111">在应用程序中与之交互的类将自动从 EDMX 文件生成。</span><span class="sxs-lookup"><span data-stu-id="be58f-111">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="be58f-112">**主讲人**：[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="be58f-112">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="be58f-113">**视频**：WMV | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="be58f-113">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="be58f-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="be58f-114">Pre-Requisites</span></span>

<span data-ttu-id="be58f-115">需要至少安装 Visual Studio 2010 或 Visual Studio 2012 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="be58f-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="be58f-116">如果你使用的是 Visual Studio 2010，你还需要安装[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。</span><span class="sxs-lookup"><span data-stu-id="be58f-116">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="be58f-117">1.创建现有数据库</span><span class="sxs-lookup"><span data-stu-id="be58f-117">1. Create an Existing Database</span></span>

<span data-ttu-id="be58f-118">通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="be58f-118">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="be58f-119">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="be58f-119">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="be58f-120">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="be58f-120">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="be58f-121">如果使用的是 Visual Studio 2012，则将创建一个[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)数据库。</span><span class="sxs-lookup"><span data-stu-id="be58f-121">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="be58f-122">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="be58f-122">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="be58f-123">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be58f-123">Open Visual Studio</span></span>
-   <span data-ttu-id="be58f-124">**视图-&gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="be58f-124">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="be58f-125">右键单击 "**数据连接-&gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="be58f-125">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="be58f-126">如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源</span><span class="sxs-lookup"><span data-stu-id="be58f-126">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![选择数据源](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="be58f-128">连接到 LocalDB 或 SQL Express，具体取决于已安装的数据，并输入**DatabaseFirst**作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="be58f-128">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Sql Express 连接 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 连接 DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="be58f-131">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="be58f-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    !["创建数据库" 对话框](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="be58f-133">新数据库现在将出现在服务器资源管理器中，右键单击该数据库并选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="be58f-133">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="be58f-134">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="be58f-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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
```

## <a name="2-create-the-application"></a><span data-ttu-id="be58f-135">2.创建应用程序</span><span class="sxs-lookup"><span data-stu-id="be58f-135">2. Create the Application</span></span>

<span data-ttu-id="be58f-136">为了简单起见，我们将构建一个使用 Database First 执行数据访问的基本控制台应用程序：</span><span class="sxs-lookup"><span data-stu-id="be58f-136">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="be58f-137">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be58f-137">Open Visual Studio</span></span>
-   <span data-ttu-id="be58f-138">**文件-&gt;&gt; 项目 。**</span><span class="sxs-lookup"><span data-stu-id="be58f-138">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="be58f-139">从左侧菜单和**控制台应用程序**选择**Windows**</span><span class="sxs-lookup"><span data-stu-id="be58f-139">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="be58f-140">输入**DatabaseFirstSample**作为名称</span><span class="sxs-lookup"><span data-stu-id="be58f-140">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="be58f-141">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="be58f-141">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="be58f-142">3.反向工程模型</span><span class="sxs-lookup"><span data-stu-id="be58f-142">3. Reverse Engineer Model</span></span>

<span data-ttu-id="be58f-143">我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。</span><span class="sxs-lookup"><span data-stu-id="be58f-143">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="be58f-144">**项目-&gt; "添加新项 ..."**</span><span class="sxs-lookup"><span data-stu-id="be58f-144">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="be58f-145">从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="be58f-145">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="be58f-146">输入**BloggingModel**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="be58f-146">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="be58f-147">这将启动**实体数据模型向导**</span><span class="sxs-lookup"><span data-stu-id="be58f-147">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="be58f-148">选择 "**从数据库生成**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="be58f-148">Select **Generate from Database** and click **Next**</span></span>

    ![向导步骤1](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="be58f-150">选择在第一部分中创建的数据库的连接，输入 " **" bloggingcontext "** " 作为连接字符串的名称，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="be58f-150">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![向导步骤2](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="be58f-152">单击 "表" 旁边的复选框以导入所有表，然后单击 "完成"</span><span class="sxs-lookup"><span data-stu-id="be58f-152">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![向导步骤3](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="be58f-154">反向工程过程完成后，会将新模型添加到项目中，并打开，以便在 Entity Framework Designer 中查看。</span><span class="sxs-lookup"><span data-stu-id="be58f-154">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="be58f-155">App.config 文件也已添加到您的项目中，其中包含数据库的连接详细信息。</span><span class="sxs-lookup"><span data-stu-id="be58f-155">An App.config file has also been added to your project with the connection details for the database.</span></span>

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="be58f-157">Visual Studio 2010 中的其他步骤</span><span class="sxs-lookup"><span data-stu-id="be58f-157">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="be58f-158">如果使用的是 Visual Studio 2010，升级到最新版本的实体框架需要遵循一些额外步骤。</span><span class="sxs-lookup"><span data-stu-id="be58f-158">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="be58f-159">升级很重要，因为它使你能够访问改进的 API 图面，更易于使用，并提供最新的 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="be58f-159">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="be58f-160">首先，我们需要从 NuGet 获取最新版本的实体框架。</span><span class="sxs-lookup"><span data-stu-id="be58f-160">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="be58f-161">**项目–&gt; 管理 NuGet 程序包 ...** 
    *如果你没有 "\*\*管理 nuget 包 ...*\* " 选项，则应安装[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="be58f-161">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="be58f-162">选择 "**联机**" 选项卡</span><span class="sxs-lookup"><span data-stu-id="be58f-162">Select the **Online** tab</span></span>
-   <span data-ttu-id="be58f-163">选择**EntityFramework**包</span><span class="sxs-lookup"><span data-stu-id="be58f-163">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="be58f-164">单击“安装”</span><span class="sxs-lookup"><span data-stu-id="be58f-164">Click **Install**</span></span>

<span data-ttu-id="be58f-165">接下来，我们需要交换模型，以生成使用 DbContext API 的代码，这些代码是在实体框架的更高版本中引入的。</span><span class="sxs-lookup"><span data-stu-id="be58f-165">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="be58f-166">在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="be58f-166">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="be58f-167">从左侧菜单中选择 "**联机模板**"，然后搜索**DbContext**</span><span class="sxs-lookup"><span data-stu-id="be58f-167">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="be58f-168">选择**用于 C\#的 EF DbContext 生成器**，输入**BloggingModel**作为名称，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="be58f-168">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext 模板](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="be58f-170">4.读取 & 写入数据</span><span class="sxs-lookup"><span data-stu-id="be58f-170">4. Reading & Writing Data</span></span>

<span data-ttu-id="be58f-171">现在，我们有了一个模型，可以使用它来访问某些数据了。</span><span class="sxs-lookup"><span data-stu-id="be58f-171">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="be58f-172">要用于访问数据的类将根据 EDMX 文件自动生成。</span><span class="sxs-lookup"><span data-stu-id="be58f-172">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="be58f-173">*此屏幕快照来自 Visual Studio 2012，如果你使用的是 Visual Studio 2010，则 BloggingModel.tt 和 BloggingModel.Context.tt 文件将直接位于你的项目下，而不是在 EDMX 文件下嵌套。*</span><span class="sxs-lookup"><span data-stu-id="be58f-173">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![生成的类 DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="be58f-175">在 Program.cs 中实现 Main 方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="be58f-175">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="be58f-176">此代码创建一个新的上下文实例，然后使用它来插入新的博客。</span><span class="sxs-lookup"><span data-stu-id="be58f-176">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="be58f-177">然后，它使用 LINQ 查询从按标题字母顺序排序的数据库中检索所有博客。</span><span class="sxs-lookup"><span data-stu-id="be58f-177">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="be58f-178">你现在可以运行该应用程序并对其进行测试。</span><span class="sxs-lookup"><span data-stu-id="be58f-178">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="be58f-179">5.处理数据库更改</span><span class="sxs-lookup"><span data-stu-id="be58f-179">5. Dealing with Database Changes</span></span>

<span data-ttu-id="be58f-180">现在，需要对数据库架构进行一些更改，我们进行这些更改时，我们还需要更新模型以反映这些更改。</span><span class="sxs-lookup"><span data-stu-id="be58f-180">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="be58f-181">第一步是对数据库架构进行一些更改。</span><span class="sxs-lookup"><span data-stu-id="be58f-181">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="be58f-182">我们要将用户表添加到该架构中。</span><span class="sxs-lookup"><span data-stu-id="be58f-182">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="be58f-183">在服务器资源管理器中右键单击**DatabaseFirst**数据库，然后选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="be58f-183">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="be58f-184">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="be58f-184">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="be58f-185">现在，架构已更新，现在可以用这些更改更新模型了。</span><span class="sxs-lookup"><span data-stu-id="be58f-185">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="be58f-186">在 EF 设计器中右键单击模型的空白点，然后选择 "从数据库更新模型 ..."，这将启动更新向导</span><span class="sxs-lookup"><span data-stu-id="be58f-186">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="be58f-187">在更新向导的 "添加" 选项卡上，选中 "表" 旁边的框，这表示要从架构中添加任何新表。</span><span class="sxs-lookup"><span data-stu-id="be58f-187">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="be58f-188">*"刷新" 选项卡显示模型中的所有现有表，这些表将在更新过程中检查更改。删除选项卡显示已从架构中删除的所有表，并将作为更新的一部分从模型中删除。这两个选项卡上的信息是自动检测的，仅提供提供信息，无法更改任何设置。*</span><span class="sxs-lookup"><span data-stu-id="be58f-188">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![刷新向导](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="be58f-190">在更新向导中单击 "完成"</span><span class="sxs-lookup"><span data-stu-id="be58f-190">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="be58f-191">模型现在已更新为包含新的用户实体，该实体映射到添加到数据库中的用户表。</span><span class="sxs-lookup"><span data-stu-id="be58f-191">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![已更新模型](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="be58f-193">Summary</span><span class="sxs-lookup"><span data-stu-id="be58f-193">Summary</span></span>

<span data-ttu-id="be58f-194">在本演练中，我们将讨论 Database First 开发，这允许我们基于现有数据库在 EF 设计器中创建模型。</span><span class="sxs-lookup"><span data-stu-id="be58f-194">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="be58f-195">然后，使用该模型从数据库中读取和写入一些数据。</span><span class="sxs-lookup"><span data-stu-id="be58f-195">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="be58f-196">最后，我们更新了模型以反映对数据库架构所做的更改。</span><span class="sxs-lookup"><span data-stu-id="be58f-196">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
