---
title: 与 WPF-EF6 的数据绑定
author: divega
ms.date: 10/23/2016
ms.assetid: e90d48e6-bea5-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 1933988277d3be8fecc02fced3293f2b7f80c901
ms.sourcegitcommit: ae399f9f3d1bae2c446b552247bd3af3ca5a2cf9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2018
ms.locfileid: "48575660"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="d9d94-102">使用 WPF 数据绑定</span><span class="sxs-lookup"><span data-stu-id="d9d94-102">Databinding with WPF</span></span>
<span data-ttu-id="d9d94-103">此分步演练说明如何将 POCO 类型绑定到"母版-详细信息"窗体中的 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="d9d94-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="d9d94-104">应用程序使用 Entity Framework Api 填充数据库中的数据对象、 跟踪更改，然后将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9d94-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="d9d94-105">模型定义一个对多关系中参与的两种类型：**类别**(主体\\master) 和**产品**(相关\\详细信息)。</span><span class="sxs-lookup"><span data-stu-id="d9d94-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="d9d94-106">然后，Visual Studio 工具用于绑定到 WPF 控件模型中定义的类型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="d9d94-107">WPF 数据绑定框架，相关对象之间导航： 在母版视图中选择行导致要使用相应的子数据更新的详细信息视图。</span><span class="sxs-lookup"><span data-stu-id="d9d94-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="d9d94-108">从 Visual Studio 2013 创建屏幕快照和在本演练中的代码列表，但你可以完成此演练使用 Visual Studio 2012 或 Visual Studio 2010。</span><span class="sxs-lookup"><span data-stu-id="d9d94-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="d9d94-109">使用 Object 选项来创建的 WPF 数据源</span><span class="sxs-lookup"><span data-stu-id="d9d94-109">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="d9d94-110">我们使用建议使用与以前版本的实体框架**数据库**选项时创建新的数据源基于使用 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-110">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="d9d94-111">这是因为设计器将生成派生自 ObjectContext 的上下文和从 entityobject 继承派生的实体类。</span><span class="sxs-lookup"><span data-stu-id="d9d94-111">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="d9d94-112">使用数据库选项可帮助你编写与此 API 图面进行交互的最佳代码。</span><span class="sxs-lookup"><span data-stu-id="d9d94-112">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="d9d94-113">用于 Visual Studio 2012 和 Visual Studio 2013 EF 设计器生成派生自 DbContext 和简单的 POCO 实体类的上下文。</span><span class="sxs-lookup"><span data-stu-id="d9d94-113">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="d9d94-114">使用 Visual Studio 2010，我们建议交换到使用 DbContext，稍后在本演练中所述的代码生成模板。</span><span class="sxs-lookup"><span data-stu-id="d9d94-114">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="d9d94-115">使用 DbContext API 外围应用时应使用**对象**选项时创建新的数据源，如本演练中所示。</span><span class="sxs-lookup"><span data-stu-id="d9d94-115">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="d9d94-116">如果需要可以[还原到基于 ObjectContext 代码生成](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)使用 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-116">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="d9d94-117">系统必备组件</span><span class="sxs-lookup"><span data-stu-id="d9d94-117">Pre-Requisites</span></span>

<span data-ttu-id="d9d94-118">需要具有 Visual Studio 2013，Visual Studio 2012 或 Visual Studio 2010 安装来完成本演练。</span><span class="sxs-lookup"><span data-stu-id="d9d94-118">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="d9d94-119">如果使用 Visual Studio 2010，您还必须安装 NuGet。</span><span class="sxs-lookup"><span data-stu-id="d9d94-119">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="d9d94-120">有关详细信息，请参阅[安装 NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="d9d94-120">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="d9d94-121">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="d9d94-121">Create the Application</span></span>

-   <span data-ttu-id="d9d94-122">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9d94-122">Open Visual Studio</span></span>
-   <span data-ttu-id="d9d94-123">**文件-&gt;新增-&gt;项目...**</span><span class="sxs-lookup"><span data-stu-id="d9d94-123">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="d9d94-124">选择**Windows**的左窗格中并**WPFApplication**右窗格中</span><span class="sxs-lookup"><span data-stu-id="d9d94-124">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="d9d94-125">输入**WPFwithEFSample**作为名称</span><span class="sxs-lookup"><span data-stu-id="d9d94-125">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="d9d94-126">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="d9d94-126">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="d9d94-127">安装 Entity Framework NuGet 包</span><span class="sxs-lookup"><span data-stu-id="d9d94-127">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="d9d94-128">在解决方案资源管理器，右键单击**WinFormswithEFSample**项目</span><span class="sxs-lookup"><span data-stu-id="d9d94-128">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="d9d94-129">选择**管理 NuGet 包...**</span><span class="sxs-lookup"><span data-stu-id="d9d94-129">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="d9d94-130">在管理 NuGet 包对话框中，选择**联机**选项卡，选择**EntityFramework**包</span><span class="sxs-lookup"><span data-stu-id="d9d94-130">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="d9d94-131">单击**安装**</span><span class="sxs-lookup"><span data-stu-id="d9d94-131">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="d9d94-132">除了 EntityFramework 程序集也添加到 System.ComponentModel.DataAnnotations 的引用。</span><span class="sxs-lookup"><span data-stu-id="d9d94-132">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="d9d94-133">如果项目具有对 System.Data.Entity 的引用，然后它将删除在安装 EntityFramework 包。</span><span class="sxs-lookup"><span data-stu-id="d9d94-133">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="d9d94-134">System.data.entity 的引用程序集不能再用于 Entity Framework 6 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d9d94-134">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="d9d94-135">定义模型</span><span class="sxs-lookup"><span data-stu-id="d9d94-135">Define a Model</span></span>

<span data-ttu-id="d9d94-136">在本演练中你可以选择实现使用 Code First 或 EF 设计器的模型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-136">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="d9d94-137">完成以下两个部分之一。</span><span class="sxs-lookup"><span data-stu-id="d9d94-137">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="d9d94-138">选项 1： 定义使用 Code First 模型</span><span class="sxs-lookup"><span data-stu-id="d9d94-138">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="d9d94-139">本部分演示如何创建模型和其关联的数据库使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="d9d94-139">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="d9d94-140">请跳到下一节 (**选项 2： 定义模型使用 Database First)** 如果您更愿意使用 Database First 进行反向工程使用 EF 设计器从数据库模型</span><span class="sxs-lookup"><span data-stu-id="d9d94-140">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="d9d94-141">使用 Code First 开发时通常首先编写.NET Framework 类定义概念 （域） 模型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-141">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="d9d94-142">添加到一个新类**WPFwithEFSample:**</span><span class="sxs-lookup"><span data-stu-id="d9d94-142">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="d9d94-143">右键单击项目名称</span><span class="sxs-lookup"><span data-stu-id="d9d94-143">Right-click on the project name</span></span>
    -   <span data-ttu-id="d9d94-144">选择**添加**，然后**新项**</span><span class="sxs-lookup"><span data-stu-id="d9d94-144">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="d9d94-145">选择**类**并输入**产品**的类名</span><span class="sxs-lookup"><span data-stu-id="d9d94-145">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="d9d94-146">替换**产品**类定义替换以下代码：</span><span class="sxs-lookup"><span data-stu-id="d9d94-146">Replace the **Product** class definition with the following code:</span></span>

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

<span data-ttu-id="d9d94-147">**产品**上的属性**类别**类并**类别**属性**产品**类是导航属性。</span><span class="sxs-lookup"><span data-stu-id="d9d94-147">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="d9d94-148">在实体框架中，导航属性提供一种导航两个实体类型之间的关系方法。</span><span class="sxs-lookup"><span data-stu-id="d9d94-148">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="d9d94-149">除了定义实体，还需要定义一个类，派生自 DbContext，并公开 DbSet&lt;TEntity&gt;属性。</span><span class="sxs-lookup"><span data-stu-id="d9d94-149">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="d9d94-150">DbSet&lt;TEntity&gt;属性使知道你想要在模型中包含哪些的类型的上下文。</span><span class="sxs-lookup"><span data-stu-id="d9d94-150">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="d9d94-151">在运行时，其中包括填充数据库中的数据的对象期间将 DbContext 派生类型的实例管理的实体对象，到数据库更改跟踪，并保留数据。</span><span class="sxs-lookup"><span data-stu-id="d9d94-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="d9d94-152">添加一个新**ProductContext**类具有以下定义的项目：</span><span class="sxs-lookup"><span data-stu-id="d9d94-152">Add a new **ProductContext** class to the project with the following definition:</span></span>

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="d9d94-153">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="d9d94-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="d9d94-154">选项 2： 定义模型使用 Database First</span><span class="sxs-lookup"><span data-stu-id="d9d94-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="d9d94-155">本部分演示如何使用第一个数据库进行反向工程，使用 EF 设计器从数据库模型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="d9d94-156">如果你在完成上一节 (**选项 1： 使用 Code First 定义模型)**，然后跳过此部分并直接转到**延迟加载**部分。</span><span class="sxs-lookup"><span data-stu-id="d9d94-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="d9d94-157">创建现有数据库</span><span class="sxs-lookup"><span data-stu-id="d9d94-157">Create an Existing Database</span></span>

<span data-ttu-id="d9d94-158">通常当你面向的现有数据库，则将已创建，但我们需要在本演练中创建的数据库访问。</span><span class="sxs-lookup"><span data-stu-id="d9d94-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="d9d94-159">随 Visual Studio 一起安装的数据库服务器是已安装的 Visual Studio 的版本而异：</span><span class="sxs-lookup"><span data-stu-id="d9d94-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="d9d94-160">如果您使用的 Visual Studio 2010 系统将创建一个 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="d9d94-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="d9d94-161">如果你正在使用 Visual Studio 2012，则您将创建[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)数据库。</span><span class="sxs-lookup"><span data-stu-id="d9d94-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="d9d94-162">让我们继续并生成的数据库。</span><span class="sxs-lookup"><span data-stu-id="d9d94-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="d9d94-163">**视图-&gt;服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="d9d94-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="d9d94-164">右键单击**数据连接-&gt;添加连接...**</span><span class="sxs-lookup"><span data-stu-id="d9d94-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="d9d94-165">如果你尚未连接到数据库服务器资源管理器之前将需要选择 Microsoft SQL Server 作为数据源</span><span class="sxs-lookup"><span data-stu-id="d9d94-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![更改数据源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="d9d94-167">连接到 LocalDB 或 SQL Express，具体取决于哪一个已安装，并输入**产品**作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="d9d94-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![添加连接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![添加连接 Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="d9d94-170">选择**确定**并将你想要创建新数据库，请选择要求你**是**</span><span class="sxs-lookup"><span data-stu-id="d9d94-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![创建数据库](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="d9d94-172">新数据库现在将出现在服务器资源管理器，右键单击它并选择**新查询**</span><span class="sxs-lookup"><span data-stu-id="d9d94-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="d9d94-173">将下面的 SQL 复制到新的查询，然后右键单击查询并选择**Execute**</span><span class="sxs-lookup"><span data-stu-id="d9d94-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="d9d94-174">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="d9d94-174">Reverse Engineer Model</span></span>

<span data-ttu-id="d9d94-175">我们将使用实体框架设计器，它是作为 Visual Studio 的一部分，若要创建我们的模型。</span><span class="sxs-lookup"><span data-stu-id="d9d94-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="d9d94-176">**项目-&gt;添加新项...**</span><span class="sxs-lookup"><span data-stu-id="d9d94-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="d9d94-177">选择**数据**左侧的菜单，然后**ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="d9d94-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="d9d94-178">输入**ProductModel**作为名称，然后单击**确定**</span><span class="sxs-lookup"><span data-stu-id="d9d94-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="d9d94-179">这将启动**实体数据模型向导**</span><span class="sxs-lookup"><span data-stu-id="d9d94-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="d9d94-180">选择**从数据库生成**单击**下一步**</span><span class="sxs-lookup"><span data-stu-id="d9d94-180">Select **Generate from Database** and click **Next**</span></span>

    ![选择模型内容](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="d9d94-182">选择连接到第一个部分中创建的数据库中，输入**ProductContext**作为名称的连接字符串和单击**下一步**</span><span class="sxs-lookup"><span data-stu-id="d9d94-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![选择您的连接](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="d9d94-184">单击表导入的所有表并单击完成旁边的复选框</span><span class="sxs-lookup"><span data-stu-id="d9d94-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![选择对象](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="d9d94-186">反向工程过程完成后的新模型添加到你的项目，并打开，以便在实体框架设计器中查看。</span><span class="sxs-lookup"><span data-stu-id="d9d94-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="d9d94-187">此外具有已 App.config 文件添加到你的项目数据库的连接详细信息。</span><span class="sxs-lookup"><span data-stu-id="d9d94-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="d9d94-188">Visual Studio 2010 中的其他步骤</span><span class="sxs-lookup"><span data-stu-id="d9d94-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="d9d94-189">如果你正在 Visual Studio 2010 中将需要更新 EF 设计器使用 EF6 代码生成。</span><span class="sxs-lookup"><span data-stu-id="d9d94-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="d9d94-190">在您的模型在 EF 设计器中的某一空白点上右键单击并选择**添加代码生成项...**</span><span class="sxs-lookup"><span data-stu-id="d9d94-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="d9d94-191">选择**联机模板**从左侧的菜单并搜索**DbContext**</span><span class="sxs-lookup"><span data-stu-id="d9d94-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="d9d94-192">选择**EF 6.x DbContext Generator 于 c 语言\#，** 输入**ProductsModel**作为名称并单击添加</span><span class="sxs-lookup"><span data-stu-id="d9d94-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="d9d94-193">更新数据绑定的代码生成</span><span class="sxs-lookup"><span data-stu-id="d9d94-193">Updating code generation for data binding</span></span>

<span data-ttu-id="d9d94-194">EF 从您的模型使用 T4 模板生成代码。</span><span class="sxs-lookup"><span data-stu-id="d9d94-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="d9d94-195">随 Visual Studio 或从 Visual Studio 库中下载的模板适用于常规用途使用。</span><span class="sxs-lookup"><span data-stu-id="d9d94-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="d9d94-196">这意味着从这些模板生成的实体具有简单 ICollection&lt;T&gt;属性。</span><span class="sxs-lookup"><span data-stu-id="d9d94-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="d9d94-197">但是，执行数据绑定使用 WPF 时，需使用**ObservableCollection**集合属性，以便该 WPF 可以跟踪对集合所做的更改。</span><span class="sxs-lookup"><span data-stu-id="d9d94-197">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="d9d94-198">为此，我们将修改为使用 ObservableCollection 的模板。</span><span class="sxs-lookup"><span data-stu-id="d9d94-198">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="d9d94-199">打开**解决方案资源管理器**并找到**ProductModel.edmx**文件</span><span class="sxs-lookup"><span data-stu-id="d9d94-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="d9d94-200">查找**ProductModel.tt**将嵌套在 ProductModel.edmx 文件下的文件</span><span class="sxs-lookup"><span data-stu-id="d9d94-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 产品模型模板](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="d9d94-202">双击 ProductModel.tt 文件以在 Visual Studio 编辑器中打开它</span><span class="sxs-lookup"><span data-stu-id="d9d94-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="d9d94-203">查找和替换的两个实例"**ICollection**"with"**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="d9d94-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="d9d94-204">这些文件位于大约有 296 和 484 的行。</span><span class="sxs-lookup"><span data-stu-id="d9d94-204">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="d9d94-205">查找和替换第一个匹配项"**HashSet**"with"**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="d9d94-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="d9d94-206">这种情况非常大约位于第 50 行。</span><span class="sxs-lookup"><span data-stu-id="d9d94-206">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="d9d94-207">**不这样做**替换 HashSet 更高版本的代码中找到的第二个匹配项。</span><span class="sxs-lookup"><span data-stu-id="d9d94-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="d9d94-208">查找和替换的唯一匹配项"**System.Collections.Generic**"with"**System.Collections.ObjectModel**"。</span><span class="sxs-lookup"><span data-stu-id="d9d94-208">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="d9d94-209">这是大约位于行 424。</span><span class="sxs-lookup"><span data-stu-id="d9d94-209">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="d9d94-210">保存 ProductModel.tt 文件。</span><span class="sxs-lookup"><span data-stu-id="d9d94-210">Save the ProductModel.tt file.</span></span> <span data-ttu-id="d9d94-211">这应该会导致重新生成的实体的代码。</span><span class="sxs-lookup"><span data-stu-id="d9d94-211">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="d9d94-212">如果不自动重新生成代码，然后 ProductModel.tt 右键单击并选择"运行自定义工具"。</span><span class="sxs-lookup"><span data-stu-id="d9d94-212">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="d9d94-213">如果现在打开 Category.cs 文件 （其中嵌套在 ProductModel.tt 下），则你应看到产品集合具有类型**ObservableCollection&lt;产品&gt;**。</span><span class="sxs-lookup"><span data-stu-id="d9d94-213">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="d9d94-214">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="d9d94-214">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="d9d94-215">延迟加载</span><span class="sxs-lookup"><span data-stu-id="d9d94-215">Lazy Loading</span></span>

<span data-ttu-id="d9d94-216">**产品**上的属性**类别**类并**类别**属性**产品**类是导航属性。</span><span class="sxs-lookup"><span data-stu-id="d9d94-216">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="d9d94-217">在实体框架中，导航属性提供一种导航两个实体类型之间的关系方法。</span><span class="sxs-lookup"><span data-stu-id="d9d94-217">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="d9d94-218">EF 提供了一个选项的相关的实体从数据库中加载自动首次访问导航属性。</span><span class="sxs-lookup"><span data-stu-id="d9d94-218">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="d9d94-219">使用此类型的加载 （称为延迟加载），注意，访问每个导航属性的第一次一个单独的查询时将执行针对数据库内容尚不在上下文中。</span><span class="sxs-lookup"><span data-stu-id="d9d94-219">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="d9d94-220">使用 POCO 实体类型时，EF 可以通过在运行时期间创建派生的代理类型的实例，然后重写虚拟属性添加加载挂钩在类中的实现延迟加载。</span><span class="sxs-lookup"><span data-stu-id="d9d94-220">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="d9d94-221">若要获取相关对象的延迟加载，您必须声明导航属性 getter 作为**公共**和**虚拟**(**Overridable**在 Visual Basic 中)，而且不能为您的类**密封**(**NotOverridable**在 Visual Basic 中)。</span><span class="sxs-lookup"><span data-stu-id="d9d94-221">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="d9d94-222">使用数据库时将自动进行虚拟若要启用延迟加载第一个导航属性。</span><span class="sxs-lookup"><span data-stu-id="d9d94-222">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="d9d94-223">在代码优先部分中我们所做的导航属性虚拟出于同样的原因</span><span class="sxs-lookup"><span data-stu-id="d9d94-223">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="d9d94-224">将对象绑定到控件</span><span class="sxs-lookup"><span data-stu-id="d9d94-224">Bind Object to Controls</span></span>

<span data-ttu-id="d9d94-225">添加此 WPF 应用程序的数据源作为模型中定义的类。</span><span class="sxs-lookup"><span data-stu-id="d9d94-225">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="d9d94-226">双击**MainWindow.xaml**在解决方案资源管理器打开主窗体</span><span class="sxs-lookup"><span data-stu-id="d9d94-226">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="d9d94-227">从主菜单中，选择**项目-&gt;添加新数据源...**</span><span class="sxs-lookup"><span data-stu-id="d9d94-227">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="d9d94-228">(需要在 Visual Studio 2010 中，选择要**数据-&gt;添加新数据源...**)</span><span class="sxs-lookup"><span data-stu-id="d9d94-228">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="d9d94-229">在选择数据源 Typewindow 中，选择**对象**单击**下一步**</span><span class="sxs-lookup"><span data-stu-id="d9d94-229">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="d9d94-230">在选择数据对象对话框中，展开**WPFwithEFSample**两次，然后选择**类别**</span><span class="sxs-lookup"><span data-stu-id="d9d94-230">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="d9d94-231">*若要选择无需**产品**数据源，因为会对通过此**产品**的属性**类别**数据源*</span><span class="sxs-lookup"><span data-stu-id="d9d94-231">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![选择数据对象](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="d9d94-233">单击**完成。**</span><span class="sxs-lookup"><span data-stu-id="d9d94-233">Click **Finish.**</span></span>
-   <span data-ttu-id="d9d94-234">数据源窗口打开 MainWindow.xaml 窗口的旁边*如果未显示数据源窗口，选择\*\*视图-&gt;其他 Windows-&gt;数据源*\*\*</span><span class="sxs-lookup"><span data-stu-id="d9d94-234">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="d9d94-235">按固定图标，以便数据源窗口不会不会自动隐藏。</span><span class="sxs-lookup"><span data-stu-id="d9d94-235">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="d9d94-236">您可能需要按刷新按钮，如果窗口已可见。</span><span class="sxs-lookup"><span data-stu-id="d9d94-236">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Data Sources](~/ef6/media/datasources.png)

-   <span data-ttu-id="d9d94-238">选择**类别**数据源，并将其拖动窗体上。</span><span class="sxs-lookup"><span data-stu-id="d9d94-238">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="d9d94-239">我们拖动到此源时，将发生以下的情况：</span><span class="sxs-lookup"><span data-stu-id="d9d94-239">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="d9d94-240">**CategoryViewSource**资源并**categoryDataGrid**控件添加到 XAML</span><span class="sxs-lookup"><span data-stu-id="d9d94-240">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="d9d94-241">父网格元素的 DataContext 属性设置为"{StaticResource **categoryViewSource** }"。</span><span class="sxs-lookup"><span data-stu-id="d9d94-241">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span> <span data-ttu-id="d9d94-242">**CategoryViewSource**资源用作绑定源的外部\\父网格元素。</span><span class="sxs-lookup"><span data-stu-id="d9d94-242">The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="d9d94-243">内部的网格元素然后从父网格 （categoryDataGrid 的 ItemsSource 属性设置为"{Binding}"） 继承的 DataContext 值</span><span class="sxs-lookup"><span data-stu-id="d9d94-243">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a><span data-ttu-id="d9d94-244">添加详细信息网格</span><span class="sxs-lookup"><span data-stu-id="d9d94-244">Adding a Details Grid</span></span>

<span data-ttu-id="d9d94-245">现在，我们已有一个网格，可以让我们来显示类别添加详细信息网格以显示相关联的产品。</span><span class="sxs-lookup"><span data-stu-id="d9d94-245">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="d9d94-246">选择**产品**属性从下的**类别**数据源，并将其拖动窗体上。</span><span class="sxs-lookup"><span data-stu-id="d9d94-246">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="d9d94-247">**CategoryProductsViewSource**资源并**productDataGrid**网格添加到 XAML</span><span class="sxs-lookup"><span data-stu-id="d9d94-247">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="d9d94-248">此资源的绑定路径设置为产品</span><span class="sxs-lookup"><span data-stu-id="d9d94-248">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="d9d94-249">WPF 数据绑定框架可确保，与所选类别相关的唯一产品显示在**productDataGrid**</span><span class="sxs-lookup"><span data-stu-id="d9d94-249">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="d9d94-250">从工具箱拖动**按钮**到窗体。</span><span class="sxs-lookup"><span data-stu-id="d9d94-250">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="d9d94-251">设置**名称**属性设置为**buttonSave**并**内容**属性设置为**保存**。</span><span class="sxs-lookup"><span data-stu-id="d9d94-251">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="d9d94-252">在窗体应类似于以下所示：</span><span class="sxs-lookup"><span data-stu-id="d9d94-252">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="d9d94-254">添加代码来处理数据交互</span><span class="sxs-lookup"><span data-stu-id="d9d94-254">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="d9d94-255">就可以将一些事件处理程序添加到主窗口。</span><span class="sxs-lookup"><span data-stu-id="d9d94-255">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="d9d94-256">在 XAML 窗口中，单击**&lt;窗口**元素，这将选择主窗口</span><span class="sxs-lookup"><span data-stu-id="d9d94-256">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="d9d94-257">在中**属性**窗口中选择**事件**右上角，然后双击右侧的文本框**Loaded**标签</span><span class="sxs-lookup"><span data-stu-id="d9d94-257">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主窗口属性](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="d9d94-259">此外将添加**单击**事件**保存**通过双击设计器中的保存按钮的按钮。</span><span class="sxs-lookup"><span data-stu-id="d9d94-259">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="d9d94-260">转到代码隐藏窗体中，我们现在将编辑代码以使用 ProductContext 执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="d9d94-260">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="d9d94-261">更新代码的主窗口，如下所示。</span><span class="sxs-lookup"><span data-stu-id="d9d94-261">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="d9d94-262">该代码声明了的长时间运行的实例**ProductContext**。</span><span class="sxs-lookup"><span data-stu-id="d9d94-262">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="d9d94-263">**ProductContext**对象用于查询并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9d94-263">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="d9d94-264">**Dispose （)** 上**ProductContext**实例然后称为从重写**OnClosing**方法。</span><span class="sxs-lookup"><span data-stu-id="d9d94-264">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span> <span data-ttu-id="d9d94-265">代码注释提供了有关代码的作用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="d9d94-265">The code comments provide details about what the code does.</span></span>

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a><span data-ttu-id="d9d94-266">测试的 WPF 应用程序</span><span class="sxs-lookup"><span data-stu-id="d9d94-266">Test the WPF Application</span></span>

-   <span data-ttu-id="d9d94-267">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="d9d94-267">Compile and run the application.</span></span> <span data-ttu-id="d9d94-268">如果使用 Code First，那么，你将看到**WPFwithEFSample.ProductContext**为您创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d9d94-268">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="d9d94-269">在底部网格中的顶级网格和产品名称中输入类别名称*不要输入任何内容 ID 在列中，因为由数据库生成的主键*</span><span class="sxs-lookup"><span data-stu-id="d9d94-269">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![使用新类别和产品的主窗口](~/ef6/media/screen1.png)

-   <span data-ttu-id="d9d94-271">按**保存**按钮以将数据保存到数据库</span><span class="sxs-lookup"><span data-stu-id="d9d94-271">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="d9d94-272">调用的 DbContext 的后面**savechanges （)**，Id 将填入数据库生成值。</span><span class="sxs-lookup"><span data-stu-id="d9d94-272">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="d9d94-273">因为我们调用**Refresh()** 后**savechanges （)** **DataGrid**控件更新使用新值。</span><span class="sxs-lookup"><span data-stu-id="d9d94-273">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![使用 Id 填充主窗口](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="d9d94-275">其他资源</span><span class="sxs-lookup"><span data-stu-id="d9d94-275">Additional Resources</span></span>

<span data-ttu-id="d9d94-276">若要了解有关数据绑定到集合使用 WPF 的详细信息，请参阅[本主题](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)WPF 文档中。</span><span class="sxs-lookup"><span data-stu-id="d9d94-276">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
