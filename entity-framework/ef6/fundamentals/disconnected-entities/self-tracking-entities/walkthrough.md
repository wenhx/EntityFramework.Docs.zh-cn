---
title: Self-Tracking 实体演练-EF6
description: 实体框架6的自我跟踪实体演练
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 7c50bbc9fad10a474728b03e79b685c549cf675d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065597"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="ffd1e-103">Self-Tracking 实体演练</span><span class="sxs-lookup"><span data-stu-id="ffd1e-103">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="ffd1e-104">我们不再建议使用自跟踪实体模板。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-104">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="ffd1e-105">它将仅继续用于支持现有应用程序。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-105">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="ffd1e-106">如果应用程序需要使用断开连接的实体图，请考虑其他替代方案，例如[可跟踪实体](https://trackableentities.github.io/)，它与自跟踪实体类似，社区在更积极地开发这种技术，或使用低级别更改跟踪 API 编写自定义代码。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-106">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="ffd1e-107">本演练演示了 Windows Communication Foundation (WCF) 服务公开返回实体关系图的操作的情况。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-107">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="ffd1e-108">接下来，客户端应用程序操作该图并将修改提交给使用实体框架验证和保存数据库更新的服务操作。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-108">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="ffd1e-109">在完成本演练之前，请务必阅读 " [自跟踪实体](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) " 页。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-109">Before completing this walkthrough make sure you read the [Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) page.</span></span>

<span data-ttu-id="ffd1e-110">此演练完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="ffd1e-110">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="ffd1e-111">创建要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-111">Creates a database to access.</span></span>
-   <span data-ttu-id="ffd1e-112">创建包含模型的类库。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-112">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="ffd1e-113">交换到 Self-Tracking 实体生成器模板。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-113">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="ffd1e-114">将实体类移到单独的项目中。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-114">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="ffd1e-115">创建一个 WCF 服务，该服务公开用于查询和保存实体的操作。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-115">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="ffd1e-116"> (控制台和 WPF) 创建使用该服务的客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-116">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="ffd1e-117">我们将在本演练中使用 Database First，但相同的技术同样适用于 Model First。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-117">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="ffd1e-118">先决条件</span><span class="sxs-lookup"><span data-stu-id="ffd1e-118">Pre-Requisites</span></span>

<span data-ttu-id="ffd1e-119">若要完成本演练，你将需要最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-119">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="ffd1e-120">创建数据库</span><span class="sxs-lookup"><span data-stu-id="ffd1e-120">Create a Database</span></span>

<span data-ttu-id="ffd1e-121">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="ffd1e-121">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="ffd1e-122">如果使用的是 Visual Studio 2012，则将创建一个 LocalDB 数据库。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-122">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="ffd1e-123">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-123">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="ffd1e-124">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-124">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="ffd1e-125">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffd1e-125">Open Visual Studio</span></span>
-   <span data-ttu-id="ffd1e-126">**视图- &gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-126">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="ffd1e-127">右键单击 "**数据连接- &gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="ffd1e-127">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="ffd1e-128">如果尚未从服务器资源管理器连接到数据库，则需要选择 **Microsoft SQL Server** 作为数据源</span><span class="sxs-lookup"><span data-stu-id="ffd1e-128">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="ffd1e-129">连接到 LocalDB 或 SQL Express，具体取决于你安装的是哪个</span><span class="sxs-lookup"><span data-stu-id="ffd1e-129">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="ffd1e-130">输入 **STESample** 作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="ffd1e-130">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="ffd1e-131">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="ffd1e-132">新数据库现在将出现在服务器资源管理器</span><span class="sxs-lookup"><span data-stu-id="ffd1e-132">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="ffd1e-133">如果使用的是 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="ffd1e-133">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="ffd1e-134">右键单击“服务器资源管理器”中的数据库，然后选择“新建查询”\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="ffd1e-134">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="ffd1e-135">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="ffd1e-136">如果使用的是 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="ffd1e-136">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="ffd1e-137">选择 **数据 &gt; Transact-sql SQL 编辑器- &gt; 新建查询连接 ...**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-137">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="ffd1e-138">输入 **。 \\SQLEXPRESS** 作为服务器名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-138">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="ffd1e-139">从 "查询编辑器" 顶部的下拉菜单中选择 " **STESample** " 数据库</span><span class="sxs-lookup"><span data-stu-id="ffd1e-139">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="ffd1e-140">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行 SQL** "。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-140">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

## <a name="create-the-model"></a><span data-ttu-id="ffd1e-141">创建模型</span><span class="sxs-lookup"><span data-stu-id="ffd1e-141">Create the Model</span></span>

<span data-ttu-id="ffd1e-142">首先，我们需要一个项目来放置模型。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-142">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="ffd1e-143">**文件- &gt; 新建- &gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-143">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="ffd1e-144">从左窗格中选择 " \*\*Visual C \# \*\* " **，然后选择 "类库"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-144">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="ffd1e-145">输入 **STESample** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-145">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="ffd1e-146">现在，我们将在 EF 设计器中创建一个简单的模型来访问数据库：</span><span class="sxs-lookup"><span data-stu-id="ffd1e-146">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="ffd1e-147">**项目- &gt; 添加新项 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-147">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="ffd1e-148">从左窗格中选择 " **数据** "，然后 **ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-148">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="ffd1e-149">输入 **BloggingModel** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-149">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="ffd1e-150">选择 "**从数据库生成**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-150">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="ffd1e-151">输入在上一部分中创建的数据库的连接信息</span><span class="sxs-lookup"><span data-stu-id="ffd1e-151">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="ffd1e-152">输入 **"bloggingcontext"** 作为连接字符串的名称，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-152">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="ffd1e-153">选中 "**表**" 旁边的框，然后单击 "**完成**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-153">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="ffd1e-154">交换到粘贴代码生成</span><span class="sxs-lookup"><span data-stu-id="ffd1e-154">Swap to STE Code Generation</span></span>

<span data-ttu-id="ffd1e-155">现在，我们需要禁用默认代码生成并交换到 Self-Tracking 实体。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-155">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="ffd1e-156">如果使用的是 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="ffd1e-156">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="ffd1e-157">展开**解决方案资源管理器**中的**BloggingModel** ，删除**BloggingModel.tt**和**BloggingModel.Context.tt** 
     *这将禁用默认代码生成*</span><span class="sxs-lookup"><span data-stu-id="ffd1e-157">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="ffd1e-158">右键单击 EF 设计器图面上的空白区域，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="ffd1e-158">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="ffd1e-159">从左窗格中选择 " **联机** "，然后搜索 **粘贴生成器**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-159">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="ffd1e-160">选择**用于 C \# 的粘贴生成器**模板，输入**STETemplate**作为名称，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-160">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="ffd1e-161">**STETemplate.tt**和**STETemplate.Context.tt**文件添加到了 BloggingModel 文件下</span><span class="sxs-lookup"><span data-stu-id="ffd1e-161">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="ffd1e-162">如果使用的是 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="ffd1e-162">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="ffd1e-163">右键单击 EF 设计器图面上的空白区域，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="ffd1e-163">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="ffd1e-164">从左窗格中选择 " **代码** "，然后 **ADO.NET Self-Tracking 实体生成器**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-164">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="ffd1e-165">输入**STETemplate**作为名称，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-165">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="ffd1e-166">**STETemplate.tt**和**STETemplate.Context.tt**文件会直接添加到你的项目</span><span class="sxs-lookup"><span data-stu-id="ffd1e-166">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="ffd1e-167">将实体类型移到单独的项目中</span><span class="sxs-lookup"><span data-stu-id="ffd1e-167">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="ffd1e-168">若要使用 Self-Tracking 实体，客户端应用程序需要访问模型中生成的实体类。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-168">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="ffd1e-169">由于我们不想将整个模型公开给客户端应用程序，因此我们要将实体类移到单独的项目中。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-169">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="ffd1e-170">第一步是停止生成现有项目中的实体类：</span><span class="sxs-lookup"><span data-stu-id="ffd1e-170">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="ffd1e-171">在**解决方案资源管理器**中右键单击 " **STETemplate.tt** "，然后选择 "**属性**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-171">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="ffd1e-172">在 "**属性**" 窗口中，清除 " **CustomTool** " 属性中的**TextTemplatingFileGenerator**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-172">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="ffd1e-173">在**解决方案资源管理器**中展开 " **STETemplate.tt** "，并删除其下嵌套的所有文件</span><span class="sxs-lookup"><span data-stu-id="ffd1e-173">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="ffd1e-174">接下来，我们将添加一个新项目，并在其中生成实体类</span><span class="sxs-lookup"><span data-stu-id="ffd1e-174">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="ffd1e-175">**文件- &gt; 添加 &gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-175">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="ffd1e-176">从左窗格中选择 " \*\*Visual C \# \*\* " **，然后选择 "类库"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-176">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="ffd1e-177">输入 **STESample** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-177">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="ffd1e-178">**项目- &gt; 添加现有项 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-178">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="ffd1e-179">导航到 **STESample** 项目文件夹</span><span class="sxs-lookup"><span data-stu-id="ffd1e-179">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="ffd1e-180">选择 (查看 \*\*所有文件 \* 。 \*) \*\*</span><span class="sxs-lookup"><span data-stu-id="ffd1e-180">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="ffd1e-181">选择 **STETemplate.tt** 文件</span><span class="sxs-lookup"><span data-stu-id="ffd1e-181">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="ffd1e-182">单击 "**添加**" 按钮旁边的下拉箭头，然后选择 "**添加为链接**"。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-182">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![添加链接模板](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="ffd1e-184">我们还将确保在上下文相同的命名空间中生成实体类。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-184">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="ffd1e-185">这只是减少了我们需要在应用程序中添加的 using 语句的数量。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-185">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="ffd1e-186">在**解决方案资源管理器**中右键单击链接的**STETemplate.tt** ，然后选择 "**属性**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-186">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="ffd1e-187">在 " **属性** " 窗口中，将 **自定义工具命名空间** 设置为 **STESample**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-187">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="ffd1e-188">粘贴模板生成的代码将需要引用 system.exception **才能进行编译** 。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-188">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="ffd1e-189">在可序列化实体类型上使用的 WCF **DataContract** 和 **DataMember** 特性需要此库。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-189">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="ffd1e-190">右键单击**解决方案资源管理器**中的**STESample**项目，然后选择 "**添加引用 ...** "</span><span class="sxs-lookup"><span data-stu-id="ffd1e-190">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="ffd1e-191">在 Visual Studio 2012 中，选中 " **system.web** " 旁边的框，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-191">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="ffd1e-192">在 Visual Studio 2010 中，选择 " **System.web** " 并单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-192">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="ffd1e-193">最后，具有中的上下文的项目将需要对实体类型的引用。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-193">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="ffd1e-194">在**解决方案资源管理器**中右键单击 " **STESample** " 项目，然后选择 "**添加引用 ...** "</span><span class="sxs-lookup"><span data-stu-id="ffd1e-194">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="ffd1e-195">在 Visual Studio 2012 中，从左窗格中选择 " **解决方案** "，选中 " **STESample** " 旁边的框，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-195">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="ffd1e-196">在 Visual Studio 2010 中，选择 " **项目** " 选项卡，选择 " **STESample** "，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-196">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="ffd1e-197">将实体类型移动到单独的项目的另一种方法是移动模板文件，而不是将其链接到其默认位置。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-197">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="ffd1e-198">如果执行此操作，则需要更新模板中的 **inputFile** 变量，以提供此示例中的 edmx 文件 (相对路径，该路径为 **。 \\BloggingModel**) 。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-198">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="ffd1e-199">创建 WCF 服务</span><span class="sxs-lookup"><span data-stu-id="ffd1e-199">Create a WCF Service</span></span>

<span data-ttu-id="ffd1e-200">现在是时候添加 WCF 服务来公开数据，接下来我们将创建该项目。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-200">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="ffd1e-201">**文件- &gt; 添加 &gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-201">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="ffd1e-202">从左窗格中选择 " \*\*Visual C \# \*\* "，然后选择 " **WCF 服务应用程序**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-202">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="ffd1e-203">输入 **STESample** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-203">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="ffd1e-204">添加对 **system.web** 程序集的引用</span><span class="sxs-lookup"><span data-stu-id="ffd1e-204">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="ffd1e-205">添加对 **STESample** 和 **STESample** 项目的引用</span><span class="sxs-lookup"><span data-stu-id="ffd1e-205">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="ffd1e-206">需要将 EF 连接字符串复制到此项目中，以便在运行时找到它。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-206">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="ffd1e-207">打开 **STESample **项目的**App.Config**文件，并复制**connectionStrings**元素</span><span class="sxs-lookup"><span data-stu-id="ffd1e-207">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="ffd1e-208">将**connectionStrings**元素粘贴为**STESample**项目中**Web.Config**文件的**configuration**元素的子元素</span><span class="sxs-lookup"><span data-stu-id="ffd1e-208">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="ffd1e-209">现在是时候实现实际服务了。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-209">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="ffd1e-210">打开 **IService1.cs** 并将内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="ffd1e-210">Open **IService1.cs** and replace the contents with the following code</span></span>

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

-   <span data-ttu-id="ffd1e-211">打开 **Service1** 并将内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="ffd1e-211">Open **Service1.svc** and replace the contents with the following code</span></span>

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

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="ffd1e-212">从控制台应用程序使用服务</span><span class="sxs-lookup"><span data-stu-id="ffd1e-212">Consume the Service from a Console Application</span></span>

<span data-ttu-id="ffd1e-213">让我们创建一个使用我们的服务的控制台应用程序。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-213">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="ffd1e-214">**文件- &gt; 新建- &gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-214">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="ffd1e-215">从左窗格中选择 " **Visual C \# \*\* "，然后选择 "**控制台应用程序\*\*"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-215">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="ffd1e-216">输入 **STESample** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-216">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="ffd1e-217">添加对 **STESample** 项目的引用</span><span class="sxs-lookup"><span data-stu-id="ffd1e-217">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="ffd1e-218">我们需要对 WCF 服务的服务引用</span><span class="sxs-lookup"><span data-stu-id="ffd1e-218">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="ffd1e-219">在**解决方案资源管理器**中右键单击 " **ConsoleTest** " 项目，然后选择 "**添加服务引用 ...**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-219">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="ffd1e-220">单击 **发现**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-220">Click **Discover**</span></span>
-   <span data-ttu-id="ffd1e-221">输入 **BloggingService** 作为命名空间，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-221">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="ffd1e-222">现在，我们可以编写一些代码来使用该服务。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-222">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="ffd1e-223">打开 **Program.cs** 并将内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-223">Open **Program.cs** and replace the contents with the following code.</span></span>

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

<span data-ttu-id="ffd1e-224">现在可以运行应用程序来查看其实际运行情况。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-224">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="ffd1e-225">在**解决方案资源管理器**中右键单击 " **ConsoleTest** " 项目，然后选择 "调试" **-" &gt; 启动新实例**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-225">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="ffd1e-226">当应用程序执行时，将看到以下输出。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-226">You'll see the following output when the application executes.</span></span>

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

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="ffd1e-227">从 WPF 应用程序使用服务</span><span class="sxs-lookup"><span data-stu-id="ffd1e-227">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="ffd1e-228">让我们创建一个使用我们的服务的 WPF 应用程序。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-228">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="ffd1e-229">**文件- &gt; 新建- &gt; 项目 .。。**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-229">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="ffd1e-230">从左窗格中选择 " \*\*Visual C \# \*\* "，然后选择 " **WPF 应用程序**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-230">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="ffd1e-231">输入 **STESample** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-231">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="ffd1e-232">添加对 **STESample** 项目的引用</span><span class="sxs-lookup"><span data-stu-id="ffd1e-232">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="ffd1e-233">我们需要对 WCF 服务的服务引用</span><span class="sxs-lookup"><span data-stu-id="ffd1e-233">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="ffd1e-234">在**解决方案资源管理器**中右键单击 " **WPFTest** " 项目，然后选择 "**添加服务引用 ...**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-234">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="ffd1e-235">单击 **发现**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-235">Click **Discover**</span></span>
-   <span data-ttu-id="ffd1e-236">输入 **BloggingService** 作为命名空间，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ffd1e-236">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="ffd1e-237">现在，我们可以编写一些代码来使用该服务。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-237">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="ffd1e-238">打开 **mainwindow.xaml** 并将内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-238">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

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

-   <span data-ttu-id="ffd1e-239">打开 Mainwindow.xaml () **MainWindow.xaml.cs** 的隐藏代码，并将内容替换为以下代码</span><span class="sxs-lookup"><span data-stu-id="ffd1e-239">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

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

<span data-ttu-id="ffd1e-240">现在可以运行应用程序来查看其实际运行情况。</span><span class="sxs-lookup"><span data-stu-id="ffd1e-240">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="ffd1e-241">在**解决方案资源管理器**中右键单击 " **WPFTest** " 项目，然后选择 "调试" **-" &gt; 启动新实例**"</span><span class="sxs-lookup"><span data-stu-id="ffd1e-241">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="ffd1e-242">你可以使用屏幕操作数据，并使用 " **保存** " 按钮通过服务保存数据</span><span class="sxs-lookup"><span data-stu-id="ffd1e-242">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF 主窗口](~/ef6/media/wpf.png)
