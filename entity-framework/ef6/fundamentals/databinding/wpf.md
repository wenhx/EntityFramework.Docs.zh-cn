---
title: 与 WPF 数据绑定 - EF6
author: divega
ms.date: 04/02/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: 6908e2a7597d0c199620c6015ed3ea06226c5ea9
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639146"
---
> [!IMPORTANT]
> <span data-ttu-id="7f440-102">**本文档仅在 .NET 框架上的 WPF 上有效**</span><span class="sxs-lookup"><span data-stu-id="7f440-102">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="7f440-103">本文档介绍 .NET 框架上 WPF 的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="7f440-103">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="7f440-104">对于新的 .NET 核心项目，我们建议您使用[EF Core](/ef/core)而不是实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="7f440-104">For new .NET Core projects, we recommend you use [EF Core](/ef/core) instead of Entity Framework 6.</span></span> <span data-ttu-id="7f440-105">EF Core 中的数据绑定文档在["问题#778"](https://github.com/dotnet/EntityFramework.Docs/issues/778)中跟踪。</span><span class="sxs-lookup"><span data-stu-id="7f440-105">The documentation for databinding in EF Core is tracked in [Issue #778](https://github.com/dotnet/EntityFramework.Docs/issues/778).</span></span>

# <a name="databinding-with-wpf"></a><span data-ttu-id="7f440-106">使用 WPF 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="7f440-106">Databinding with WPF</span></span>
<span data-ttu-id="7f440-107">此分步演练演示如何以"主详细信息"形式将 POCO 类型绑定到 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="7f440-107">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="7f440-108">应用程序使用实体框架 API 使用数据库中的数据填充对象、跟踪更改并将数据保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="7f440-108">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="7f440-109">模型定义了参与一对多关系的两种类型：**类别**（主体\\主控形状）**和产品**（相关\\详细信息）。</span><span class="sxs-lookup"><span data-stu-id="7f440-109">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="7f440-110">然后，Visual Studio 工具用于将模型中定义的类型绑定到 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="7f440-110">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="7f440-111">WPF 数据绑定框架支持相关对象之间的导航：选择主视图中的行会导致详细视图使用相应的子数据进行更新。</span><span class="sxs-lookup"><span data-stu-id="7f440-111">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="7f440-112">本演练中的屏幕截图和代码列表取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 完成本演练。</span><span class="sxs-lookup"><span data-stu-id="7f440-112">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="7f440-113">使用"对象"选项创建 WPF 数据源</span><span class="sxs-lookup"><span data-stu-id="7f440-113">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="7f440-114">对于以前版本的实体框架，我们曾建议在基于使用 EF 设计器创建的模型创建新数据源时使用**数据库**选项。</span><span class="sxs-lookup"><span data-stu-id="7f440-114">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="7f440-115">这是因为设计器将生成从 ObjectContext 派生的上下文和派生自实体对象的实体类。</span><span class="sxs-lookup"><span data-stu-id="7f440-115">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="7f440-116">使用"数据库"选项将帮助您编写用于与此 API 曲面交互的最佳代码。</span><span class="sxs-lookup"><span data-stu-id="7f440-116">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="7f440-117">2012 年 Visual Studio 的 EF 设计人员和 2013 年可视化工作室生成源自 DbContext 的上下文以及简单的 POCO 实体类。</span><span class="sxs-lookup"><span data-stu-id="7f440-117">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="7f440-118">使用 Visual Studio 2010，我们建议交换到代码生成模板，该模板使用 DbContext，如本演练后面的所述。</span><span class="sxs-lookup"><span data-stu-id="7f440-118">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="7f440-119">使用 DbContext API 曲面时，应在创建新数据源时使用**Object**选项，如本演练所示。</span><span class="sxs-lookup"><span data-stu-id="7f440-119">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="7f440-120">如果需要，您可以为使用 EF 设计器创建的模型[还原到基于 ObjectContext 的代码生成](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。</span><span class="sxs-lookup"><span data-stu-id="7f440-120">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7f440-121">先决条件</span><span class="sxs-lookup"><span data-stu-id="7f440-121">Pre-Requisites</span></span>

<span data-ttu-id="7f440-122">您需要安装 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成本演练。</span><span class="sxs-lookup"><span data-stu-id="7f440-122">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="7f440-123">如果您使用的是 Visual Studio 2010，则还必须安装 NuGet。</span><span class="sxs-lookup"><span data-stu-id="7f440-123">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="7f440-124">有关详细信息，请参阅安装[NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="7f440-124">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="7f440-125">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="7f440-125">Create the Application</span></span>

-   <span data-ttu-id="7f440-126">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f440-126">Open Visual Studio</span></span>
-   <span data-ttu-id="7f440-127">**文件&gt;-&gt;新建 - 项目...**</span><span class="sxs-lookup"><span data-stu-id="7f440-127">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="7f440-128">在左侧窗格中选择 **Windows，** 在右侧窗格中选择 **WPF 应用程序**</span><span class="sxs-lookup"><span data-stu-id="7f440-128">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="7f440-129">输入 **WPF 与 EFSample** 作为名称</span><span class="sxs-lookup"><span data-stu-id="7f440-129">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="7f440-130">选择 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="7f440-130">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="7f440-131">安装实体框架 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="7f440-131">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="7f440-132">在解决方案资源管理器中，右键单击**WinForms 与 EFSample**项目</span><span class="sxs-lookup"><span data-stu-id="7f440-132">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="7f440-133">选择 **"管理 NuGet 包..."...**</span><span class="sxs-lookup"><span data-stu-id="7f440-133">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="7f440-134">在"管理 NuGet 包"对话框中，选择 **"联机**"选项卡并选择 **"实体框架**"包</span><span class="sxs-lookup"><span data-stu-id="7f440-134">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="7f440-135">单击"**安装"**</span><span class="sxs-lookup"><span data-stu-id="7f440-135">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="7f440-136">除了实体框架程序集外，还添加了对 System.组件模型.Data注释的引用。</span><span class="sxs-lookup"><span data-stu-id="7f440-136">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="7f440-137">如果项目具有对 System.Data.Entity 的引用，则在安装实体框架包时将删除该项目。</span><span class="sxs-lookup"><span data-stu-id="7f440-137">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="7f440-138">系统.Data.实体程序集不再用于实体框架 6 应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f440-138">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="7f440-139">定义模型</span><span class="sxs-lookup"><span data-stu-id="7f440-139">Define a Model</span></span>

<span data-ttu-id="7f440-140">在本演练中，您可以选择使用代码优先或 EF 设计器实现模型。</span><span class="sxs-lookup"><span data-stu-id="7f440-140">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="7f440-141">完成以下两节之一。</span><span class="sxs-lookup"><span data-stu-id="7f440-141">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="7f440-142">选项 1：首先使用代码定义模型</span><span class="sxs-lookup"><span data-stu-id="7f440-142">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="7f440-143">本节演示如何使用 Code First 创建模型及其关联的数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-143">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="7f440-144">如果您希望使用数据库优先使用 EF 设计器从数据库反向工程模型，请跳到下一部分（**选项 2：使用数据库优先定义模型）**</span><span class="sxs-lookup"><span data-stu-id="7f440-144">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="7f440-145">使用代码优先开发时，通常先编写定义概念（域）模型的 .NET 框架类。</span><span class="sxs-lookup"><span data-stu-id="7f440-145">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="7f440-146">向 **WPFEFSample**添加新类：</span><span class="sxs-lookup"><span data-stu-id="7f440-146">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="7f440-147">右键单击项目名称</span><span class="sxs-lookup"><span data-stu-id="7f440-147">Right-click on the project name</span></span>
    -   <span data-ttu-id="7f440-148">选择 **"添加**"，然后**选择"新建项目**"</span><span class="sxs-lookup"><span data-stu-id="7f440-148">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="7f440-149">选择 **"类"** 并输入类名称 **的产品** </span><span class="sxs-lookup"><span data-stu-id="7f440-149">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="7f440-150">将 **产品** 类定义替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="7f440-150">Replace the **Product** class definition with the following code:</span></span>

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

<span data-ttu-id="7f440-151">**产品**类上的 **"产品"** 类和 **"类别"** 属性是导航属性。 **Products**</span><span class="sxs-lookup"><span data-stu-id="7f440-151">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="7f440-152">在实体框架中，导航属性提供了一种导航两种实体类型之间的关系的方法。</span><span class="sxs-lookup"><span data-stu-id="7f440-152">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="7f440-153">除了定义实体之外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt;属性的类。</span><span class="sxs-lookup"><span data-stu-id="7f440-153">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="7f440-154">DbSet&lt;TEntity&gt;属性让上下文知道要在模型中包括哪些类型。</span><span class="sxs-lookup"><span data-stu-id="7f440-154">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="7f440-155">DbContext 派生类型的实例在运行时管理实体对象，其中包括将对象与数据库中的数据填充、更改跟踪和将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-155">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="7f440-156">向具有以下定义的项目添加新**的 ProductContext**类：</span><span class="sxs-lookup"><span data-stu-id="7f440-156">Add a new **ProductContext** class to the project with the following definition:</span></span>

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

<span data-ttu-id="7f440-157">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="7f440-157">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="7f440-158">选项 2：首先使用数据库定义模型</span><span class="sxs-lookup"><span data-stu-id="7f440-158">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="7f440-159">本节演示如何使用数据库 First 使用 EF 设计器从数据库反向工程模型。</span><span class="sxs-lookup"><span data-stu-id="7f440-159">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="7f440-160">如果完成了上一节（**选项 1：使用代码优先定义模型），** 则跳过此部分并直接转到 **"延迟加载**"部分。</span><span class="sxs-lookup"><span data-stu-id="7f440-160">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="7f440-161">创建现有数据库</span><span class="sxs-lookup"><span data-stu-id="7f440-161">Create an Existing Database</span></span>

<span data-ttu-id="7f440-162">通常，当您将现有数据库作为目标时，该数据库将创建，但本演练需要创建要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-162">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="7f440-163">与 Visual Studio 一起安装的数据库服务器因已安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="7f440-163">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="7f440-164">如果您使用的是 Visual Studio 2010，您将创建一个 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-164">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="7f440-165">如果您使用的是 Visual Studio 2012，则您将创建一个[本地数据库](https://msdn.microsoft.com/library/hh510202.aspx)数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-165">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="7f440-166">让我们继续生成数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-166">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="7f440-167">**视图&gt;- 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="7f440-167">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7f440-168">右键单击**数据连接&gt;- 添加连接...**</span><span class="sxs-lookup"><span data-stu-id="7f440-168">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="7f440-169">如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源</span><span class="sxs-lookup"><span data-stu-id="7f440-169">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![更改数据源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="7f440-171">连接到本地数据库或 SQL Express，具体取决于已安装的哪个，然后输入 **"产品**"作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="7f440-171">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![添加连接本地数据库](~/ef6/media/addconnectionlocaldb.png)

    ![添加连接快讯](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="7f440-174">选择 **"确定"，** 系统将询问您是否要创建新数据库，请选择"**是**"</span><span class="sxs-lookup"><span data-stu-id="7f440-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![创建数据库](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="7f440-176">新数据库现在将显示在服务器资源管理器中，右键单击它并选择 **"新查询"**</span><span class="sxs-lookup"><span data-stu-id="7f440-176">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="7f440-177">将以下 SQL 复制到新查询中，然后右键单击查询并选择 **"执行"**</span><span class="sxs-lookup"><span data-stu-id="7f440-177">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="7f440-178">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="7f440-178">Reverse Engineer Model</span></span>

<span data-ttu-id="7f440-179">我们将利用实体框架设计器（作为 Visual Studio 的一部分）创建我们的模型。</span><span class="sxs-lookup"><span data-stu-id="7f440-179">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="7f440-180">**项目&gt;- 添加新项目...**</span><span class="sxs-lookup"><span data-stu-id="7f440-180">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="7f440-181">从左侧菜单中选择**数据**，然后**ADO.NET实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="7f440-181">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="7f440-182">输入**产品模型**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="7f440-182">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="7f440-183">这将启动**实体数据模型向导**</span><span class="sxs-lookup"><span data-stu-id="7f440-183">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="7f440-184">选择 **"从数据库生成**"，然后单击 **"下一步"**</span><span class="sxs-lookup"><span data-stu-id="7f440-184">Select **Generate from Database** and click **Next**</span></span>

    ![选择模型内容](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="7f440-186">选择与在第一部分中创建的数据库的连接，输入**ProductContext**作为连接字符串的名称，然后单击"**下一步"**</span><span class="sxs-lookup"><span data-stu-id="7f440-186">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![选择您的连接](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="7f440-188">单击"表格"旁边的复选框导入所有表，然后单击"完成"</span><span class="sxs-lookup"><span data-stu-id="7f440-188">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![选择对象](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="7f440-190">反向工程过程完成后，新模型将添加到项目中，并打开，供您在实体框架设计器中查看。</span><span class="sxs-lookup"><span data-stu-id="7f440-190">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="7f440-191">App.config 文件也已添加到项目中，并包含数据库的连接详细信息。</span><span class="sxs-lookup"><span data-stu-id="7f440-191">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="7f440-192">视觉工作室 2010 中的其他步骤</span><span class="sxs-lookup"><span data-stu-id="7f440-192">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="7f440-193">如果您在 Visual Studio 2010 中工作，则需要更新 EF 设计器才能使用 EF6 代码生成。</span><span class="sxs-lookup"><span data-stu-id="7f440-193">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="7f440-194">右键单击 EF 设计器中模型的空白点，然后选择 **"添加代码生成项..."**</span><span class="sxs-lookup"><span data-stu-id="7f440-194">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="7f440-195">从左侧菜单**中选择"联机模板"** 并搜索**DbContext**</span><span class="sxs-lookup"><span data-stu-id="7f440-195">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="7f440-196">选择**EF 6.x DbContext\#生成器为 C ，** 输入**产品模型**作为名称，然后单击"添加"</span><span class="sxs-lookup"><span data-stu-id="7f440-196">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="7f440-197">更新数据绑定的代码生成</span><span class="sxs-lookup"><span data-stu-id="7f440-197">Updating code generation for data binding</span></span>

<span data-ttu-id="7f440-198">EF 使用 T4 模板从模型生成代码。</span><span class="sxs-lookup"><span data-stu-id="7f440-198">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="7f440-199">视觉工作室附带或从 Visual Studio 库下载的模板用于一般用途。</span><span class="sxs-lookup"><span data-stu-id="7f440-199">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="7f440-200">这意味着从这些模板生成的实体具有简单的 ICollection&lt;T&gt;属性。</span><span class="sxs-lookup"><span data-stu-id="7f440-200">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="7f440-201">但是，使用 WPF 执行数据绑定时，最好将**可观察集合**用于集合属性，以便 WPF 可以跟踪对集合所做的更改。</span><span class="sxs-lookup"><span data-stu-id="7f440-201">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="7f440-202">为此，我们将修改模板以使用可观察集合。</span><span class="sxs-lookup"><span data-stu-id="7f440-202">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="7f440-203">打开**解决方案资源管理器**并查找**产品模型.edmx**文件</span><span class="sxs-lookup"><span data-stu-id="7f440-203">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="7f440-204">查找将嵌套在 ProductModel.edmx 文件下的**ProductModel.tt**文件</span><span class="sxs-lookup"><span data-stu-id="7f440-204">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 产品模型模板](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="7f440-206">双击ProductModel.tt文件以在可视化工作室编辑器中打开该文件</span><span class="sxs-lookup"><span data-stu-id="7f440-206">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="7f440-207">查找 **"ICollection"** 的两个事件并将其替换为"**可观察集合**"。</span><span class="sxs-lookup"><span data-stu-id="7f440-207">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="7f440-208">这些位于大约 296 和 484 线。</span><span class="sxs-lookup"><span data-stu-id="7f440-208">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="7f440-209">查找"**哈希集**"的第一次出现与"**可观察集合"替换**。</span><span class="sxs-lookup"><span data-stu-id="7f440-209">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="7f440-210">此事件大约位于第 50 行。</span><span class="sxs-lookup"><span data-stu-id="7f440-210">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="7f440-211">**不要**替换代码后面找到的第二个哈希集。</span><span class="sxs-lookup"><span data-stu-id="7f440-211">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="7f440-212">查找"**系统.集合.通用**"的唯一匹配项并将其替换为"**系统.集合.objectModel"。**</span><span class="sxs-lookup"><span data-stu-id="7f440-212">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="7f440-213">这大约位于第 424 行。</span><span class="sxs-lookup"><span data-stu-id="7f440-213">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="7f440-214">保存ProductModel.tt文件。</span><span class="sxs-lookup"><span data-stu-id="7f440-214">Save the ProductModel.tt file.</span></span> <span data-ttu-id="7f440-215">这应会导致重新生成实体的代码。</span><span class="sxs-lookup"><span data-stu-id="7f440-215">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="7f440-216">如果代码未自动重新生成，则右键单击ProductModel.tt并选择"运行自定义工具"。</span><span class="sxs-lookup"><span data-stu-id="7f440-216">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="7f440-217">如果您现在打开Category.cs文件（嵌套在ProductModel.tt下），那么您应该看到产品集合具有 **"可观察&lt;集合产品&gt;**"的类型。</span><span class="sxs-lookup"><span data-stu-id="7f440-217">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="7f440-218">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="7f440-218">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="7f440-219">延迟加载</span><span class="sxs-lookup"><span data-stu-id="7f440-219">Lazy Loading</span></span>

<span data-ttu-id="7f440-220">**产品**类上的 **"产品"** 类和 **"类别"** 属性是导航属性。 **Products**</span><span class="sxs-lookup"><span data-stu-id="7f440-220">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="7f440-221">在实体框架中，导航属性提供了一种导航两种实体类型之间的关系的方法。</span><span class="sxs-lookup"><span data-stu-id="7f440-221">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="7f440-222">EF 为您提供了在首次访问导航属性时自动从数据库加载相关实体的选项。</span><span class="sxs-lookup"><span data-stu-id="7f440-222">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="7f440-223">使用这种类型的加载（称为延迟加载），请注意，如果内容尚未在上下文中，则第一次访问每个导航属性时，将对数据库执行单独的查询。</span><span class="sxs-lookup"><span data-stu-id="7f440-223">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="7f440-224">使用 POCO 实体类型时，EF 通过在运行时创建派生代理类型的实例，然后在类中重写虚拟属性来添加加载挂钩，从而实现延迟加载。</span><span class="sxs-lookup"><span data-stu-id="7f440-224">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="7f440-225">要获取相关对象的延迟加载，必须声明导航属性 getter 为**公共**和**虚拟**（在 Visual Basic 中**可重写**），并且不得**密封**类（在 Visual Basic 中**不可重写**）。</span><span class="sxs-lookup"><span data-stu-id="7f440-225">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="7f440-226">使用数据库时，第一个导航属性会自动成为虚拟属性，以启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="7f440-226">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="7f440-227">在"代码第一"部分中，我们选择出于同样的原因使导航属性成为虚拟</span><span class="sxs-lookup"><span data-stu-id="7f440-227">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="7f440-228">将对象绑定到控件</span><span class="sxs-lookup"><span data-stu-id="7f440-228">Bind Object to Controls</span></span>

<span data-ttu-id="7f440-229">将模型中定义的类添加为此 WPF 应用程序的数据源。</span><span class="sxs-lookup"><span data-stu-id="7f440-229">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="7f440-230">双击解决方案资源管理器中的**MainWindow.xaml**以打开主窗体</span><span class="sxs-lookup"><span data-stu-id="7f440-230">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="7f440-231">从主菜单中，选择 **"项目&gt;- 添加新数据源...**</span><span class="sxs-lookup"><span data-stu-id="7f440-231">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="7f440-232">（在 Visual Studio 2010 中，您需要选择**数据 -&gt;添加新数据源...**</span><span class="sxs-lookup"><span data-stu-id="7f440-232">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="7f440-233">在"选择数据源类型窗口"中，选择**对象**并单击 **"下一步**"</span><span class="sxs-lookup"><span data-stu-id="7f440-233">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="7f440-234">在"选择数据对象"对话框中，展开**WPF 与 EFSample** 两次并选择 **"类别"**</span><span class="sxs-lookup"><span data-stu-id="7f440-234">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="7f440-235">*无需选择**产品**数据源，因为我们将通过 **"产品\*\*\*\*"在类别**数据源上的属性获取它*</span><span class="sxs-lookup"><span data-stu-id="7f440-235">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![选择数据对象](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="7f440-237">单击 **“完成”**。</span><span class="sxs-lookup"><span data-stu-id="7f440-237">Click **Finish.**</span></span>
-   <span data-ttu-id="7f440-238">*如果"数据源"窗口未显示，则选择 **"查看 -&gt;其他&gt;Windows- 数据源**"，* 在 MainWindow.xaml 窗口旁边打开数据源。</span><span class="sxs-lookup"><span data-stu-id="7f440-238">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="7f440-239">按引脚图标，以便数据源窗口不会自动隐藏。</span><span class="sxs-lookup"><span data-stu-id="7f440-239">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="7f440-240">如果窗口已可见，则可能需要点击刷新按钮。</span><span class="sxs-lookup"><span data-stu-id="7f440-240">You may need to hit the refresh button if the window was already visible.</span></span>

    ![“数据源”](~/ef6/media/datasources.png)

-   <span data-ttu-id="7f440-242">选择 **"类别"** 数据源并将其拖动到窗体上。</span><span class="sxs-lookup"><span data-stu-id="7f440-242">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="7f440-243">当我们拖动此源时发生了以下情况：</span><span class="sxs-lookup"><span data-stu-id="7f440-243">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="7f440-244">**类别ViewSource**资源和**类别数据网格**控件已添加到 XAML</span><span class="sxs-lookup"><span data-stu-id="7f440-244">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="7f440-245">父网格元素上的 DataContext 属性设置为"[静态资源类别**ViewSource** ]"。</span><span class="sxs-lookup"><span data-stu-id="7f440-245">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="7f440-246">**类别 ViewSource**资源用作外部\\父网格元素的绑定源。</span><span class="sxs-lookup"><span data-stu-id="7f440-246"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="7f440-247">然后，内部网格元素从父网格继承 DataContext 值（类别 DataGrid 的 ItemsSource 属性设置为"{绑定}"）</span><span class="sxs-lookup"><span data-stu-id="7f440-247">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

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

## <a name="adding-a-details-grid"></a><span data-ttu-id="7f440-248">添加详细信息网格</span><span class="sxs-lookup"><span data-stu-id="7f440-248">Adding a Details Grid</span></span>

<span data-ttu-id="7f440-249">现在，我们有一个网格来显示类别，让我们添加一个详细信息网格来显示关联的产品。</span><span class="sxs-lookup"><span data-stu-id="7f440-249">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="7f440-250">从 **"类别"** 数据源下选择 **"产品**"属性，并将其拖动到窗体上。</span><span class="sxs-lookup"><span data-stu-id="7f440-250">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="7f440-251">**类别 ProductsViewSource**资源和**产品数据网格**将添加到 XAML</span><span class="sxs-lookup"><span data-stu-id="7f440-251">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="7f440-252">此资源的绑定路径设置为产品</span><span class="sxs-lookup"><span data-stu-id="7f440-252">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="7f440-253">WPF 数据绑定框架可确保产品**DataGrid**中仅显示与所选类别相关的产品</span><span class="sxs-lookup"><span data-stu-id="7f440-253">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="7f440-254">从"工具箱"中，将**按钮**拖动到窗体。</span><span class="sxs-lookup"><span data-stu-id="7f440-254">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="7f440-255">将 **"名称"** 属性**设置为按钮"保存**"，将 **"内容"** 属性设置为 **"保存**"。</span><span class="sxs-lookup"><span data-stu-id="7f440-255">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="7f440-256">窗体应如下所示：</span><span class="sxs-lookup"><span data-stu-id="7f440-256">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="7f440-258">添加处理数据交互的代码</span><span class="sxs-lookup"><span data-stu-id="7f440-258">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="7f440-259">是时候将一些事件处理程序添加到主窗口了。</span><span class="sxs-lookup"><span data-stu-id="7f440-259">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="7f440-260">在 XAML 窗口中，单击**&lt;窗口**元素，这将选择主窗口</span><span class="sxs-lookup"><span data-stu-id="7f440-260">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="7f440-261">在 **"属性"** 窗口中选择右上角**的事件**，然后双击 **"已加载**"标签右侧的文本框</span><span class="sxs-lookup"><span data-stu-id="7f440-261">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主窗口属性](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="7f440-263">此外，通过双击设计器中的"**保存**"按钮，添加 **"保存**"按钮的 Click 事件。</span><span class="sxs-lookup"><span data-stu-id="7f440-263">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="7f440-264">这带来了表单背后的代码，我们现在将编辑代码以使用 ProductContext 执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="7f440-264">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="7f440-265">更新主窗口的代码，如下所示。</span><span class="sxs-lookup"><span data-stu-id="7f440-265">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="7f440-266">该代码声明**产品上下文**的长期运行实例。</span><span class="sxs-lookup"><span data-stu-id="7f440-266">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="7f440-267">**ProductContext**对象用于查询数据并将其保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="7f440-267">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="7f440-268">然后，从重写的**On关闭**方法调用**ProductContext**实例上的**Dispose（）。**</span><span class="sxs-lookup"><span data-stu-id="7f440-268">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="7f440-269">代码注释提供有关代码功能的详细信息。</span><span class="sxs-lookup"><span data-stu-id="7f440-269"> The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-wpf-application"></a><span data-ttu-id="7f440-270">测试 WPF 应用程序</span><span class="sxs-lookup"><span data-stu-id="7f440-270">Test the WPF Application</span></span>

-   <span data-ttu-id="7f440-271">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f440-271">Compile and run the application.</span></span> <span data-ttu-id="7f440-272">如果您先使用代码，您将看到为您创建了**WPFwithEFSample.ProductContext**数据库。</span><span class="sxs-lookup"><span data-stu-id="7f440-272">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="7f440-273">在顶部网格中输入类别名称，在底部网格中输入产品名称*不要在 ID 列中输入任何内容，因为主键由数据库生成*</span><span class="sxs-lookup"><span data-stu-id="7f440-273">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![包含新类别和产品的主窗口](~/ef6/media/screen1.png)

-   <span data-ttu-id="7f440-275">按 **"保存"** 按钮将数据保存到数据库</span><span class="sxs-lookup"><span data-stu-id="7f440-275">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="7f440-276">调用 DbContext 的**保存更改 （）** 后，使用数据库生成的值填充 ID。</span><span class="sxs-lookup"><span data-stu-id="7f440-276">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="7f440-277">因为我们在**保存更改（）** 后调用**Refresh（）** **DataGrid**控件也使用新值进行更新。</span><span class="sxs-lookup"><span data-stu-id="7f440-277">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![已填充了已填充了"已填充的"主窗口"](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="7f440-279">其他资源</span><span class="sxs-lookup"><span data-stu-id="7f440-279">Additional Resources</span></span>

<span data-ttu-id="7f440-280">要了解有关使用 WPF 绑定到集合的数据的详细信息，请参阅 WPF 文档中[的本主题](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)。</span><span class="sxs-lookup"><span data-stu-id="7f440-280">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
