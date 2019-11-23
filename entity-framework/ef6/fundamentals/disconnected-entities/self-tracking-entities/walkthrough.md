---
title: 自跟踪实体演练-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
ms.openlocfilehash: 9bd644461f50a7eff1006cb8866ca9a3b08b6b8d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181716"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="706c7-102">自跟踪实体演练</span><span class="sxs-lookup"><span data-stu-id="706c7-102">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="706c7-103">我们不再建议使用自跟踪实体模板。</span><span class="sxs-lookup"><span data-stu-id="706c7-103">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="706c7-104">它将仅继续用于支持现有应用程序。</span><span class="sxs-lookup"><span data-stu-id="706c7-104">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="706c7-105">如果应用程序需要使用断开连接的实体图，请考虑其他替代方案，例如[可跟踪实体](https://trackableentities.github.io/)，它与自跟踪实体类似，社区在更积极地开发这种技术，或使用低级别更改跟踪 API 编写自定义代码。</span><span class="sxs-lookup"><span data-stu-id="706c7-105">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="706c7-106">本演练演示了 Windows Communication Foundation （WCF）服务公开返回实体关系图的操作的情况。</span><span class="sxs-lookup"><span data-stu-id="706c7-106">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="706c7-107">接下来，客户端应用程序操作该图并将修改提交给使用实体框架验证和保存数据库更新的服务操作。</span><span class="sxs-lookup"><span data-stu-id="706c7-107">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="706c7-108">在完成本演练之前，请务必阅读 "[自跟踪实体](index.md)" 页。</span><span class="sxs-lookup"><span data-stu-id="706c7-108">Before completing this walkthrough make sure you read the [Self-Tracking Entities](index.md) page.</span></span>

<span data-ttu-id="706c7-109">此演练完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="706c7-109">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="706c7-110">创建要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="706c7-110">Creates a database to access.</span></span>
-   <span data-ttu-id="706c7-111">创建包含模型的类库。</span><span class="sxs-lookup"><span data-stu-id="706c7-111">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="706c7-112">交换到 "自跟踪实体生成器" 模板。</span><span class="sxs-lookup"><span data-stu-id="706c7-112">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="706c7-113">将实体类移到单独的项目中。</span><span class="sxs-lookup"><span data-stu-id="706c7-113">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="706c7-114">创建一个 WCF 服务，该服务公开用于查询和保存实体的操作。</span><span class="sxs-lookup"><span data-stu-id="706c7-114">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="706c7-115">创建使用服务的客户端应用程序（控制台和 WPF）。</span><span class="sxs-lookup"><span data-stu-id="706c7-115">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="706c7-116">我们将在本演练中使用 Database First，但相同的技术同样适用于 Model First。</span><span class="sxs-lookup"><span data-stu-id="706c7-116">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="706c7-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="706c7-117">Pre-Requisites</span></span>

<span data-ttu-id="706c7-118">若要完成本演练，你将需要最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="706c7-118">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="706c7-119">创建数据库</span><span class="sxs-lookup"><span data-stu-id="706c7-119">Create a Database</span></span>

<span data-ttu-id="706c7-120">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="706c7-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="706c7-121">如果使用的是 Visual Studio 2012，则将创建一个 LocalDB 数据库。</span><span class="sxs-lookup"><span data-stu-id="706c7-121">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="706c7-122">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="706c7-122">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="706c7-123">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="706c7-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="706c7-124">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="706c7-124">Open Visual Studio</span></span>
-   <span data-ttu-id="706c7-125">**视图-&gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="706c7-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="706c7-126">右键单击 "**数据连接-&gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="706c7-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="706c7-127">如果尚未从服务器资源管理器连接到数据库，则需要选择**Microsoft SQL Server**作为数据源</span><span class="sxs-lookup"><span data-stu-id="706c7-127">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="706c7-128">连接到 LocalDB 或 SQL Express，具体取决于你安装的是哪个</span><span class="sxs-lookup"><span data-stu-id="706c7-128">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="706c7-129">输入**STESample**作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="706c7-129">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="706c7-130">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="706c7-130">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="706c7-131">新数据库现在将出现在服务器资源管理器</span><span class="sxs-lookup"><span data-stu-id="706c7-131">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="706c7-132">如果使用的是 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="706c7-132">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="706c7-133">在服务器资源管理器中右键单击该数据库，然后选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="706c7-133">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="706c7-134">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="706c7-134">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="706c7-135">如果使用的是 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="706c7-135">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="706c7-136">选择**数据&gt; Transact-sql 编辑器-&gt; 新建查询连接 ...**</span><span class="sxs-lookup"><span data-stu-id="706c7-136">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="706c7-137">输入 **。\\SQLEXPRESS**作为服务器名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-137">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="706c7-138">从 "查询编辑器" 顶部的下拉菜单中选择 " **STESample** " 数据库</span><span class="sxs-lookup"><span data-stu-id="706c7-138">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="706c7-139">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行 SQL** "。</span><span class="sxs-lookup"><span data-stu-id="706c7-139">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="706c7-140">创建模型</span><span class="sxs-lookup"><span data-stu-id="706c7-140">Create the Model</span></span>

<span data-ttu-id="706c7-141">首先，我们需要一个项目来放置模型。</span><span class="sxs-lookup"><span data-stu-id="706c7-141">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="706c7-142">**文件-&gt;&gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="706c7-142">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="706c7-143">从左窗格中选择 " **Visual C\#** ，**然后选择" 类库 "**</span><span class="sxs-lookup"><span data-stu-id="706c7-143">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="706c7-144">输入**STESample**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-144">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="706c7-145">现在，我们将在 EF 设计器中创建一个简单的模型来访问数据库：</span><span class="sxs-lookup"><span data-stu-id="706c7-145">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="706c7-146">**项目-&gt; "添加新项 ..."**</span><span class="sxs-lookup"><span data-stu-id="706c7-146">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="706c7-147">从左窗格中选择 "**数据**"，然后**ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="706c7-147">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="706c7-148">输入**BloggingModel**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-148">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="706c7-149">选择 "**从数据库生成**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="706c7-149">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="706c7-150">输入在上一部分中创建的数据库的连接信息</span><span class="sxs-lookup"><span data-stu-id="706c7-150">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="706c7-151">输入 **"bloggingcontext"** 作为连接字符串的名称，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="706c7-151">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="706c7-152">选中 "**表**" 旁边的框，然后单击 "**完成**"</span><span class="sxs-lookup"><span data-stu-id="706c7-152">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="706c7-153">交换到粘贴代码生成</span><span class="sxs-lookup"><span data-stu-id="706c7-153">Swap to STE Code Generation</span></span>

<span data-ttu-id="706c7-154">现在，我们需要禁用默认代码生成并交换到自跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="706c7-154">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="706c7-155">如果使用的是 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="706c7-155">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="706c7-156">展开**解决方案资源管理器**中的**BloggingModel** ，删除**BloggingModel.tt**和**BloggingModel.Context.tt**
    *这将禁用默认代码生成*</span><span class="sxs-lookup"><span data-stu-id="706c7-156">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="706c7-157">右键单击 EF 设计器图面上的空白区域，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="706c7-157">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="706c7-158">从左窗格中选择 "**联机**"，然后搜索**粘贴生成器**</span><span class="sxs-lookup"><span data-stu-id="706c7-158">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="706c7-159">选择**粘贴生成器 For C\#** 模板，输入**STETemplate**作为名称，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="706c7-159">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="706c7-160">**STETemplate.tt**和**STETemplate.Context.tt**文件添加到了 BloggingModel 文件下</span><span class="sxs-lookup"><span data-stu-id="706c7-160">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="706c7-161">如果使用的是 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="706c7-161">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="706c7-162">右键单击 EF 设计器图面上的空白区域，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="706c7-162">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="706c7-163">从左窗格中选择 "**代码**"，然后**ADO.NET 自跟踪实体生成器**</span><span class="sxs-lookup"><span data-stu-id="706c7-163">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="706c7-164">输入**STETemplate**作为名称，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="706c7-164">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="706c7-165">**STETemplate.tt**和**STETemplate.Context.tt**文件会直接添加到你的项目</span><span class="sxs-lookup"><span data-stu-id="706c7-165">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="706c7-166">将实体类型移到单独的项目中</span><span class="sxs-lookup"><span data-stu-id="706c7-166">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="706c7-167">若要使用自跟踪实体，客户端应用程序需要访问模型中生成的实体类。</span><span class="sxs-lookup"><span data-stu-id="706c7-167">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="706c7-168">由于我们不想将整个模型公开给客户端应用程序，因此我们要将实体类移到单独的项目中。</span><span class="sxs-lookup"><span data-stu-id="706c7-168">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="706c7-169">第一步是停止生成现有项目中的实体类：</span><span class="sxs-lookup"><span data-stu-id="706c7-169">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="706c7-170">在**解决方案资源管理器**中右键单击 " **STETemplate.tt** "，然后选择 "**属性**"</span><span class="sxs-lookup"><span data-stu-id="706c7-170">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="706c7-171">在 "**属性**" 窗口中，清除 " **CustomTool** " 属性中的**TextTemplatingFileGenerator**</span><span class="sxs-lookup"><span data-stu-id="706c7-171">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="706c7-172">在**解决方案资源管理器**中展开 " **STETemplate.tt** "，并删除其下嵌套的所有文件</span><span class="sxs-lookup"><span data-stu-id="706c7-172">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="706c7-173">接下来，我们将添加一个新项目，并在其中生成实体类</span><span class="sxs-lookup"><span data-stu-id="706c7-173">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="706c7-174">**文件-&gt; 添加&gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="706c7-174">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="706c7-175">从左窗格中选择 " **Visual C\#** ，**然后选择" 类库 "**</span><span class="sxs-lookup"><span data-stu-id="706c7-175">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="706c7-176">输入**STESample**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-176">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="706c7-177">**项目-&gt; 添加现有项 .。。**</span><span class="sxs-lookup"><span data-stu-id="706c7-177">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="706c7-178">导航到**STESample**项目文件夹</span><span class="sxs-lookup"><span data-stu-id="706c7-178">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="706c7-179">选择查看**所有文件（\*\*）**</span><span class="sxs-lookup"><span data-stu-id="706c7-179">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="706c7-180">选择**STETemplate.tt**文件</span><span class="sxs-lookup"><span data-stu-id="706c7-180">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="706c7-181">单击 "**添加**" 按钮旁边的下拉箭头，然后选择 "**添加为链接**"。</span><span class="sxs-lookup"><span data-stu-id="706c7-181">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![添加链接模板](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="706c7-183">我们还将确保在上下文相同的命名空间中生成实体类。</span><span class="sxs-lookup"><span data-stu-id="706c7-183">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="706c7-184">这只是减少了我们需要在应用程序中添加的 using 语句的数量。</span><span class="sxs-lookup"><span data-stu-id="706c7-184">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="706c7-185">在**解决方案资源管理器**中右键单击链接的**STETemplate.tt** ，然后选择 "**属性**"</span><span class="sxs-lookup"><span data-stu-id="706c7-185">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="706c7-186">在 "**属性**" 窗口中，将**自定义工具命名空间**设置为**STESample**</span><span class="sxs-lookup"><span data-stu-id="706c7-186">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="706c7-187">粘贴模板生成的代码将需要引用 system.exception**才能进行编译**。</span><span class="sxs-lookup"><span data-stu-id="706c7-187">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="706c7-188">在可序列化实体类型上使用的 WCF **DataContract**和**DataMember**特性需要此库。</span><span class="sxs-lookup"><span data-stu-id="706c7-188">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="706c7-189">右键单击**解决方案资源管理器**中的**STESample**项目，然后选择 "**添加引用 ...** "</span><span class="sxs-lookup"><span data-stu-id="706c7-189">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="706c7-190">在 Visual Studio 2012 中，选中 " **system.web** " 旁边的框，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-190">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="706c7-191">在 Visual Studio 2010 中，选择 " **System.web** " 并单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-191">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="706c7-192">最后，具有中的上下文的项目将需要对实体类型的引用。</span><span class="sxs-lookup"><span data-stu-id="706c7-192">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="706c7-193">在**解决方案资源管理器**中右键单击 " **STESample** " 项目，然后选择 "**添加引用 ...** "</span><span class="sxs-lookup"><span data-stu-id="706c7-193">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="706c7-194">在 Visual Studio 2012 中，从左窗格中选择 "**解决方案**"，选中 " **STESample** " 旁边的框，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-194">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="706c7-195">在 Visual Studio 2010 中，选择 "**项目**" 选项卡，选择 " **STESample** "，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-195">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="706c7-196">将实体类型移动到单独的项目的另一种方法是移动模板文件，而不是将其链接到其默认位置。</span><span class="sxs-lookup"><span data-stu-id="706c7-196">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="706c7-197">如果执行此操作，则需要更新模板中的**inputFile**变量，以提供 edmx 文件的相对路径（在本示例中，为 **..\\BloggingModel**）。</span><span class="sxs-lookup"><span data-stu-id="706c7-197">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="706c7-198">创建 WCF 服务</span><span class="sxs-lookup"><span data-stu-id="706c7-198">Create a WCF Service</span></span>

<span data-ttu-id="706c7-199">现在是时候添加 WCF 服务来公开数据，接下来我们将创建该项目。</span><span class="sxs-lookup"><span data-stu-id="706c7-199">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="706c7-200">**文件-&gt; 添加&gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="706c7-200">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="706c7-201">从左窗格中选择 " **Visual C\#** ，然后选择" **WCF 服务应用程序**"</span><span class="sxs-lookup"><span data-stu-id="706c7-201">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="706c7-202">输入**STESample**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-202">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="706c7-203">添加对**system.web**程序集的引用</span><span class="sxs-lookup"><span data-stu-id="706c7-203">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="706c7-204">添加对**STESample**和**STESample**项目的引用</span><span class="sxs-lookup"><span data-stu-id="706c7-204">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="706c7-205">需要将 EF 连接字符串复制到此项目中，以便在运行时找到它。</span><span class="sxs-lookup"><span data-stu-id="706c7-205">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="706c7-206">打开 **STESample **项目的**app.config**文件并复制**connectionStrings**元素</span><span class="sxs-lookup"><span data-stu-id="706c7-206">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="706c7-207">在**STESample**项目中，将**connectionStrings**元素粘贴为**web.config**文件的**configuration**元素的子元素</span><span class="sxs-lookup"><span data-stu-id="706c7-207">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="706c7-208">现在是时候实现实际服务了。</span><span class="sxs-lookup"><span data-stu-id="706c7-208">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="706c7-209">打开**IService1.cs**并将内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="706c7-209">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="706c7-210">打开**Service1**并将内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="706c7-210">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="706c7-211">从控制台应用程序使用服务</span><span class="sxs-lookup"><span data-stu-id="706c7-211">Consume the Service from a Console Application</span></span>

<span data-ttu-id="706c7-212">让我们创建一个使用我们的服务的控制台应用程序。</span><span class="sxs-lookup"><span data-stu-id="706c7-212">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="706c7-213">**文件-&gt;&gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="706c7-213">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="706c7-214">从左窗格中选择 " **Visual C\#** ，然后选择"**控制台应用程序**"</span><span class="sxs-lookup"><span data-stu-id="706c7-214">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="706c7-215">输入**STESample**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-215">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="706c7-216">添加对**STESample**项目的引用</span><span class="sxs-lookup"><span data-stu-id="706c7-216">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="706c7-217">我们需要对 WCF 服务的服务引用</span><span class="sxs-lookup"><span data-stu-id="706c7-217">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="706c7-218">在**解决方案资源管理器**中右键单击 " **ConsoleTest** " 项目，然后选择 "**添加服务引用 ...**</span><span class="sxs-lookup"><span data-stu-id="706c7-218">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="706c7-219">单击**发现**</span><span class="sxs-lookup"><span data-stu-id="706c7-219">Click **Discover**</span></span>
-   <span data-ttu-id="706c7-220">输入**BloggingService**作为命名空间，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-220">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="706c7-221">现在，我们可以编写一些代码来使用该服务。</span><span class="sxs-lookup"><span data-stu-id="706c7-221">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="706c7-222">打开**Program.cs**并将内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="706c7-222">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="706c7-223">现在可以运行应用程序来查看其实际运行情况。</span><span class="sxs-lookup"><span data-stu-id="706c7-223">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="706c7-224">在**解决方案资源管理器**中右键单击 " **ConsoleTest** " 项目，然后选择 "**调试-&gt; 启动新实例**"</span><span class="sxs-lookup"><span data-stu-id="706c7-224">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="706c7-225">当应用程序执行时，将看到以下输出。</span><span class="sxs-lookup"><span data-stu-id="706c7-225">You'll see the following output when the application executes.</span></span>

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="706c7-226">从 WPF 应用程序使用服务</span><span class="sxs-lookup"><span data-stu-id="706c7-226">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="706c7-227">让我们创建一个使用我们的服务的 WPF 应用程序。</span><span class="sxs-lookup"><span data-stu-id="706c7-227">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="706c7-228">**文件-&gt;&gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="706c7-228">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="706c7-229">从左窗格中选择 " **Visual C\#** ，然后选择" **WPF 应用程序**"</span><span class="sxs-lookup"><span data-stu-id="706c7-229">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="706c7-230">输入**STESample**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-230">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="706c7-231">添加对**STESample**项目的引用</span><span class="sxs-lookup"><span data-stu-id="706c7-231">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="706c7-232">我们需要对 WCF 服务的服务引用</span><span class="sxs-lookup"><span data-stu-id="706c7-232">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="706c7-233">在**解决方案资源管理器**中右键单击 " **WPFTest** " 项目，然后选择 "**添加服务引用 ...**</span><span class="sxs-lookup"><span data-stu-id="706c7-233">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="706c7-234">单击**发现**</span><span class="sxs-lookup"><span data-stu-id="706c7-234">Click **Discover**</span></span>
-   <span data-ttu-id="706c7-235">输入**BloggingService**作为命名空间，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="706c7-235">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="706c7-236">现在，我们可以编写一些代码来使用该服务。</span><span class="sxs-lookup"><span data-stu-id="706c7-236">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="706c7-237">打开**mainwindow.xaml**并将内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="706c7-237">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="706c7-238">打开 Mainwindow.xaml （**MainWindow.xaml.cs**）的隐藏代码，并将内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="706c7-238">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="706c7-239">现在可以运行应用程序来查看其实际运行情况。</span><span class="sxs-lookup"><span data-stu-id="706c7-239">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="706c7-240">在**解决方案资源管理器**中右键单击 " **WPFTest** " 项目，然后选择 "**调试-&gt; 启动新实例**"</span><span class="sxs-lookup"><span data-stu-id="706c7-240">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="706c7-241">你可以使用屏幕操作数据，并使用 "**保存**" 按钮通过服务保存数据</span><span class="sxs-lookup"><span data-stu-id="706c7-241">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF 主窗口](~/ef6/media/wpf.png)
