---
title: 用 WPF 进行数据绑定-EF6
description: 实体框架6中的 WPF 的数据绑定
author: ajcvickers
ms.date: 05/19/2020
uid: ef6/fundamentals/databinding/wpf
ms.openlocfilehash: b6dc484d09340671dfcd841e34234d55e9808af2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065649"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="bdd89-103">使用 WPF 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="bdd89-103">Databinding with WPF</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bdd89-104">**此文档仅对 .NET Framework 上的 WPF 有效**</span><span class="sxs-lookup"><span data-stu-id="bdd89-104">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="bdd89-105">本文档介绍 .NET Framework 上的 WPF 的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="bdd89-105">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="bdd89-106">对于新的 .NET Core 项目，建议使用 [EF Core](xref:core/index) 而不是实体框架6。</span><span class="sxs-lookup"><span data-stu-id="bdd89-106">For new .NET Core projects, we recommend you use [EF Core](xref:core/index) instead of Entity Framework 6.</span></span> <span data-ttu-id="bdd89-107">下面是有关 EF Core 中的数据绑定的文档： [与 WPF 入门](xref:core/get-started/wpf)。</span><span class="sxs-lookup"><span data-stu-id="bdd89-107">The documentation for databinding in EF Core is here: [Getting Started with WPF](xref:core/get-started/wpf).</span></span>

<span data-ttu-id="bdd89-108">此分步演练演示如何将 POCO 类型绑定到 "主/详细信息" 窗体中的 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="bdd89-108">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="bdd89-109">应用程序使用实体框架 API，用数据库中的数据填充对象、跟踪更改并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-109">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="bdd89-110">该模型定义了两种参与一对多关系的类型： **类别** (主体 \\ 主) 和 **产品** (依赖 \\ 详细信息) 。</span><span class="sxs-lookup"><span data-stu-id="bdd89-110">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="bdd89-111">然后，使用 Visual Studio 工具将模型中定义的类型绑定到 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="bdd89-111">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="bdd89-112">WPF 数据绑定框架允许在相关对象之间导航：在主视图中选择行将导致详细信息视图使用相应的子数据进行更新。</span><span class="sxs-lookup"><span data-stu-id="bdd89-112">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="bdd89-113">本演练中的屏幕截图和代码清单取自 Visual Studio 2013，但你可以通过 Visual Studio 2012 或 Visual Studio 2010 完成此演练。</span><span class="sxs-lookup"><span data-stu-id="bdd89-113">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="bdd89-114">使用 "对象" 选项创建 WPF 数据源</span><span class="sxs-lookup"><span data-stu-id="bdd89-114">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="bdd89-115">使用早期版本的实体框架我们在基于使用 EF 设计器创建的模型创建新数据源时，建议使用 **数据库** 选项。</span><span class="sxs-lookup"><span data-stu-id="bdd89-115">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="bdd89-116">这是因为设计器将生成派生自 EntityObject 的 ObjectContext 和实体类的上下文。</span><span class="sxs-lookup"><span data-stu-id="bdd89-116">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="bdd89-117">使用数据库选项有助于编写与此 API 图面交互的最佳代码。</span><span class="sxs-lookup"><span data-stu-id="bdd89-117">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="bdd89-118">Visual Studio 2012 和 Visual Studio 2013 的 EF 设计器生成一个从 DbContext 派生的上下文以及简单的 POCO 实体类。</span><span class="sxs-lookup"><span data-stu-id="bdd89-118">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="bdd89-119">在 Visual Studio 2010 中，我们建议使用 DbContext，如本演练稍后所述，换到使用的代码生成模板。</span><span class="sxs-lookup"><span data-stu-id="bdd89-119">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="bdd89-120">使用 DbContext API 图面时，应在创建新的数据源时使用 **Object** 选项，如本演练中所示。</span><span class="sxs-lookup"><span data-stu-id="bdd89-120">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="bdd89-121">如果需要，可以针对用 EF 设计器创建的模型， [恢复为基于 ObjectContext 的代码生成](xref:ef6/modeling/designer/codegen/legacy-objectcontext) 。</span><span class="sxs-lookup"><span data-stu-id="bdd89-121">If needed, you can [revert to ObjectContext based code generation](xref:ef6/modeling/designer/codegen/legacy-objectcontext) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="bdd89-122">先决条件</span><span class="sxs-lookup"><span data-stu-id="bdd89-122">Pre-Requisites</span></span>

<span data-ttu-id="bdd89-123">需要安装 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="bdd89-123">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="bdd89-124">如果你使用的是 Visual Studio 2010，则还必须安装 NuGet。</span><span class="sxs-lookup"><span data-stu-id="bdd89-124">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="bdd89-125">有关详细信息，请参阅 [安装 NuGet](/nuget/install-nuget-client-tools)。</span><span class="sxs-lookup"><span data-stu-id="bdd89-125">For more information, see [Installing NuGet](/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="bdd89-126">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="bdd89-126">Create the Application</span></span>

- <span data-ttu-id="bdd89-127">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdd89-127">Open Visual Studio</span></span>
- <span data-ttu-id="bdd89-128">**文件- &gt; 新建- &gt; 项目 ...。**</span><span class="sxs-lookup"><span data-stu-id="bdd89-128">**File -&gt; New -&gt; Project….**</span></span>
- <span data-ttu-id="bdd89-129">在 **Windows**   左侧窗格中选择 "Windows"，然后在右窗格中选择 " **WPFApplication** "</span><span class="sxs-lookup"><span data-stu-id="bdd89-129">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
- <span data-ttu-id="bdd89-130">输入 **WPFwithEFSample**   作为名称</span><span class="sxs-lookup"><span data-stu-id="bdd89-130">Enter **WPFwithEFSample** as the name</span></span>
- <span data-ttu-id="bdd89-131">选择 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="bdd89-131">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="bdd89-132">安装实体框架 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="bdd89-132">Install the Entity Framework NuGet package</span></span>

- <span data-ttu-id="bdd89-133">在解决方案资源管理器中，右键单击 **WinFormswithEFSample** 项目</span><span class="sxs-lookup"><span data-stu-id="bdd89-133">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
- <span data-ttu-id="bdd89-134">选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="bdd89-134">Select **Manage NuGet Packages…**</span></span>
- <span data-ttu-id="bdd89-135">在 "管理 NuGet 包" 对话框中，选择 " **联机** " 选项卡，然后选择 " **EntityFramework** " 包</span><span class="sxs-lookup"><span data-stu-id="bdd89-135">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
- <span data-ttu-id="bdd89-136">单击“安装” </span><span class="sxs-lookup"><span data-stu-id="bdd89-136">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="bdd89-137">除了 EntityFramework 程序集之外，还添加了对 System.componentmodel 的引用。</span><span class="sxs-lookup"><span data-stu-id="bdd89-137">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="bdd89-138">如果项目具有对 EntityFramework 的引用，则在安装包时将被删除。</span><span class="sxs-lookup"><span data-stu-id="bdd89-138">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="bdd89-139">System.web 程序集不再用于实体框架6应用程序。</span><span class="sxs-lookup"><span data-stu-id="bdd89-139">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="bdd89-140">定义模型</span><span class="sxs-lookup"><span data-stu-id="bdd89-140">Define a Model</span></span>

<span data-ttu-id="bdd89-141">在本演练中，您可以使用 Code First 或 EF 设计器选择实现模型。</span><span class="sxs-lookup"><span data-stu-id="bdd89-141">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="bdd89-142">完成以下两个部分中的一个。</span><span class="sxs-lookup"><span data-stu-id="bdd89-142">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="bdd89-143">选项1：使用 Code First 定义模型</span><span class="sxs-lookup"><span data-stu-id="bdd89-143">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="bdd89-144">本部分说明如何使用 Code First 创建模型及其关联的数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-144">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="bdd89-145">跳到下一节 (\*\*选项2：使用 Database First 定义模型) \*\* 如果你想要使用 Database First 从使用 EF 设计器的数据库反向工程模型</span><span class="sxs-lookup"><span data-stu-id="bdd89-145">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="bdd89-146">使用 Code First 开发时，通常首先编写 .NET Framework 类来定义概念 (域) 模型。</span><span class="sxs-lookup"><span data-stu-id="bdd89-146">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

- <span data-ttu-id="bdd89-147">向 WPFwithEFSample 添加新类 **：**</span><span class="sxs-lookup"><span data-stu-id="bdd89-147">Add a new class to the **WPFwithEFSample:**</span></span>
  - <span data-ttu-id="bdd89-148">右键单击项目名称</span><span class="sxs-lookup"><span data-stu-id="bdd89-148">Right-click on the project name</span></span>
  - <span data-ttu-id="bdd89-149">选择 "**添加**"，然后选择 "**新建项**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-149">Select **Add**, then **New Item**</span></span>
  - <span data-ttu-id="bdd89-150">选择**类**并输入 **Product**   类名的产品</span><span class="sxs-lookup"><span data-stu-id="bdd89-150">Select **Class** and enter **Product** for the class name</span></span>
- <span data-ttu-id="bdd89-151">将 **Product**   类定义替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="bdd89-151">Replace the **Product** class definition with the following code:</span></span>

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

- Add a **Category** class with the following definition:

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

<span data-ttu-id="bdd89-152">“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="bdd89-152">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="bdd89-153">在实体框架中，导航属性提供了一种在两个实体类型之间导航关系的方法。</span><span class="sxs-lookup"><span data-stu-id="bdd89-153">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="bdd89-154">除了定义实体外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt; 属性的类。</span><span class="sxs-lookup"><span data-stu-id="bdd89-154">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="bdd89-155">DbSet&lt;TEntity&gt; 属性让上下文知道要包括在模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="bdd89-155">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="bdd89-156">DbContext 派生类型的实例在运行时管理实体对象，其中包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-156">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

- <span data-ttu-id="bdd89-157">使用以下定义将新的 **ProductContext** 类添加到项目：</span><span class="sxs-lookup"><span data-stu-id="bdd89-157">Add a new **ProductContext** class to the project with the following definition:</span></span>

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

<span data-ttu-id="bdd89-158">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="bdd89-158">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="bdd89-159">选项2：使用 Database First 定义模型</span><span class="sxs-lookup"><span data-stu-id="bdd89-159">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="bdd89-160">本部分说明如何使用 Database First 从使用 EF 设计器的数据库中对模型进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="bdd89-160">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="bdd89-161">如果完成了上一节 (\*\*选项1：使用 Code First 定义模型) \*\*，则跳过此部分，直接转到 **延迟加载** 部分。</span><span class="sxs-lookup"><span data-stu-id="bdd89-161">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="bdd89-162">创建现有数据库</span><span class="sxs-lookup"><span data-stu-id="bdd89-162">Create an Existing Database</span></span>

<span data-ttu-id="bdd89-163">通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-163">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="bdd89-164">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="bdd89-164">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

- <span data-ttu-id="bdd89-165">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-165">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
- <span data-ttu-id="bdd89-166">如果使用的是 Visual Studio 2012，则将创建一个 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) 数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-166">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="bdd89-167">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-167">Let's go ahead and generate the database.</span></span>

- <span data-ttu-id="bdd89-168">**视图- &gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="bdd89-168">**View -&gt; Server Explorer**</span></span>
- <span data-ttu-id="bdd89-169">右键单击 "**数据连接- &gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="bdd89-169">Right click on **Data Connections -&gt; Add Connection…**</span></span>
- <span data-ttu-id="bdd89-170">如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源</span><span class="sxs-lookup"><span data-stu-id="bdd89-170">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![更改数据源](~/ef6/media/changedatasource.png)

- <span data-ttu-id="bdd89-172">连接到 LocalDB 或 SQL Express，具体取决于已安装的数据，并输入 **Products** 作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="bdd89-172">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![添加连接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![添加连接 Express](~/ef6/media/addconnectionexpress.png)

- <span data-ttu-id="bdd89-175">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="bdd89-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![创建数据库](~/ef6/media/createdatabase.png)

- <span data-ttu-id="bdd89-177">新数据库现在将出现在服务器资源管理器中，右键单击该数据库并选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-177">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
- <span data-ttu-id="bdd89-178">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-178">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="bdd89-179">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="bdd89-179">Reverse Engineer Model</span></span>

<span data-ttu-id="bdd89-180">我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。</span><span class="sxs-lookup"><span data-stu-id="bdd89-180">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

- <span data-ttu-id="bdd89-181">**项目- &gt; 添加新项 .。。**</span><span class="sxs-lookup"><span data-stu-id="bdd89-181">**Project -&gt; Add New Item…**</span></span>
- <span data-ttu-id="bdd89-182">从左侧菜单中选择 " **数据** "，然后 **ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="bdd89-182">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
- <span data-ttu-id="bdd89-183">输入 **ProductModel** 作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="bdd89-183">Enter **ProductModel** as the name and click **OK**</span></span>
- <span data-ttu-id="bdd89-184">这将启动 **实体数据模型向导**</span><span class="sxs-lookup"><span data-stu-id="bdd89-184">This launches the **Entity Data Model Wizard**</span></span>
- <span data-ttu-id="bdd89-185">选择 "**从数据库生成**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-185">Select **Generate from Database** and click **Next**</span></span>

    ![选择模型内容](~/ef6/media/choosemodelcontents.png)

- <span data-ttu-id="bdd89-187">选择在第一部分中创建的数据库的连接，输入 " **ProductContext** " 作为连接字符串的名称，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-187">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![选择连接](~/ef6/media/chooseyourconnection.png)

- <span data-ttu-id="bdd89-189">单击 "表" 旁边的复选框以导入所有表，然后单击 "完成"</span><span class="sxs-lookup"><span data-stu-id="bdd89-189">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![选择对象](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="bdd89-191">反向工程过程完成后，会将新模型添加到项目中，并打开，以便在 Entity Framework Designer 中查看。</span><span class="sxs-lookup"><span data-stu-id="bdd89-191">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="bdd89-192">还使用数据库的连接详细信息将 App.config 文件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="bdd89-192">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="bdd89-193">Visual Studio 2010 中的其他步骤</span><span class="sxs-lookup"><span data-stu-id="bdd89-193">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="bdd89-194">如果使用的是 Visual Studio 2010，则需要更新 EF 设计器以使用 EF6 代码生成。</span><span class="sxs-lookup"><span data-stu-id="bdd89-194">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

- <span data-ttu-id="bdd89-195">在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="bdd89-195">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
- <span data-ttu-id="bdd89-196">从左侧菜单中选择 " **联机模板** "，然后搜索 **DbContext**</span><span class="sxs-lookup"><span data-stu-id="bdd89-196">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
- <span data-ttu-id="bdd89-197">选择 **适用于 C 的 EF DbContext 生成器 \# ，** 输入 **ProductsModel** 作为名称，然后单击 "添加"</span><span class="sxs-lookup"><span data-stu-id="bdd89-197">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="bdd89-198">更新数据绑定的代码生成</span><span class="sxs-lookup"><span data-stu-id="bdd89-198">Updating code generation for data binding</span></span>

<span data-ttu-id="bdd89-199">EF 使用 T4 模板从模型生成代码。</span><span class="sxs-lookup"><span data-stu-id="bdd89-199">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="bdd89-200">Visual Studio 附带的模板或从 Visual Studio 库下载的模板专用于一般用途。</span><span class="sxs-lookup"><span data-stu-id="bdd89-200">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="bdd89-201">这意味着从这些模板生成的实体具有简单的 ICollection &lt; T &gt; 属性。</span><span class="sxs-lookup"><span data-stu-id="bdd89-201">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="bdd89-202">但是，使用 WPF 进行数据绑定时，需要使用 **ObservableCollection** 作为集合属性，以便 WPF 可以跟踪对集合所做的更改。</span><span class="sxs-lookup"><span data-stu-id="bdd89-202">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="bdd89-203">为此，我们将修改模板以使用 ObservableCollection。</span><span class="sxs-lookup"><span data-stu-id="bdd89-203">To this end we will to modify the templates to use ObservableCollection.</span></span>

- <span data-ttu-id="bdd89-204">打开 **解决方案资源管理器** 并查找 **ProductModel** 文件</span><span class="sxs-lookup"><span data-stu-id="bdd89-204">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
- <span data-ttu-id="bdd89-205">查找 **ProductModel.tt** 文件，该文件将嵌套在 ProductModel 文件下</span><span class="sxs-lookup"><span data-stu-id="bdd89-205">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WPF 产品型号模板](~/ef6/media/wpfproductmodeltemplate.png)

- <span data-ttu-id="bdd89-207">双击 ProductModel.tt 文件以在 Visual Studio 编辑器中将其打开</span><span class="sxs-lookup"><span data-stu-id="bdd89-207">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
- <span data-ttu-id="bdd89-208">查找并将 "**ICollection**" 的两个匹配项替换为 "**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="bdd89-208">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="bdd89-209">它们大约位于第296行和第484行。</span><span class="sxs-lookup"><span data-stu-id="bdd89-209">These are located approximately at lines 296 and 484.</span></span>
- <span data-ttu-id="bdd89-210">查找并将 "**HashSet**" 的第一个匹配项替换为 "**ObservableCollection**"。</span><span class="sxs-lookup"><span data-stu-id="bdd89-210">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="bdd89-211">此事件大约位于第50行。</span><span class="sxs-lookup"><span data-stu-id="bdd89-211">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="bdd89-212">**请勿** 替换稍后在代码中找到的第二个 HashSet。</span><span class="sxs-lookup"><span data-stu-id="bdd89-212">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
- <span data-ttu-id="bdd89-213">查找并将 "System.collections.objectmodel.collection" 的唯一匹配项替换**为 "\*\*\*\*System.Collections.ObjectModel**"。</span><span class="sxs-lookup"><span data-stu-id="bdd89-213">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="bdd89-214">此位置大致位于第424行。</span><span class="sxs-lookup"><span data-stu-id="bdd89-214">This is located approximately at line 424.</span></span>
- <span data-ttu-id="bdd89-215">保存 ProductModel.tt 文件。</span><span class="sxs-lookup"><span data-stu-id="bdd89-215">Save the ProductModel.tt file.</span></span> <span data-ttu-id="bdd89-216">这会导致重新生成实体的代码。</span><span class="sxs-lookup"><span data-stu-id="bdd89-216">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="bdd89-217">如果代码未自动重新生成，则右键单击 ProductModel.tt 并选择 "运行自定义工具"。</span><span class="sxs-lookup"><span data-stu-id="bdd89-217">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="bdd89-218">如果你现在打开 Category.cs 文件 (该文件嵌套在) ProductModel.tt 下，则你应该会看到 Products 集合具有 type \*\*ObservableCollection &lt; 产品 &gt; \*\*。</span><span class="sxs-lookup"><span data-stu-id="bdd89-218">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="bdd89-219">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="bdd89-219">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="bdd89-220">延迟加载</span><span class="sxs-lookup"><span data-stu-id="bdd89-220">Lazy Loading</span></span>

<span data-ttu-id="bdd89-221">“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="bdd89-221">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="bdd89-222">在实体框架中，导航属性提供了一种在两个实体类型之间导航关系的方法。</span><span class="sxs-lookup"><span data-stu-id="bdd89-222">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="bdd89-223">当首次访问导航属性时，EF 使你可以选择自动从数据库加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="bdd89-223">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="bdd89-224">如果使用此类型的加载（称为“延迟加载”），请注意，首次访问每个导航属性时，将对数据库执行单独的查询（如果内容不在上下文中）。</span><span class="sxs-lookup"><span data-stu-id="bdd89-224">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="bdd89-225">使用 POCO 实体类型时，EF 通过在运行时创建派生代理类型的实例，然后重写类中的虚拟属性来添加加载挂钩来实现延迟加载。</span><span class="sxs-lookup"><span data-stu-id="bdd89-225">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="bdd89-226">若要获取相关对象的延迟加载，必须在 Visual Basic) 中将导航属性 getter 声明为**公共**和**虚拟** (可**重写**，并且不能 (**NotOverridable**中的 Visual Basic) 中**密封**类。</span><span class="sxs-lookup"><span data-stu-id="bdd89-226">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="bdd89-227">使用时，会自动将 Database First 导航属性设为虚拟，以启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="bdd89-227">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="bdd89-228">在 "Code First" 部分中，我们选择将导航属性设置为虚拟，因为同一原因</span><span class="sxs-lookup"><span data-stu-id="bdd89-228">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="bdd89-229">将对象绑定到控件</span><span class="sxs-lookup"><span data-stu-id="bdd89-229">Bind Object to Controls</span></span>

<span data-ttu-id="bdd89-230">将在模型中定义的类作为此 WPF 应用程序的数据源添加。</span><span class="sxs-lookup"><span data-stu-id="bdd89-230">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

- <span data-ttu-id="bdd89-231">在“解决方案资源管理器”中双击 MainWindow.xaml 以打开主窗体</span><span class="sxs-lookup"><span data-stu-id="bdd89-231">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
- <span data-ttu-id="bdd89-232">从主菜单中，选择 "**项目- &gt; 添加新数据源 ...** "</span><span class="sxs-lookup"><span data-stu-id="bdd89-232">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="bdd89-233"> (在 Visual Studio 2010 中，需要选择 " **数据- &gt; 添加新数据源 ...** ") </span><span class="sxs-lookup"><span data-stu-id="bdd89-233">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
- <span data-ttu-id="bdd89-234">在 "选择数据源" Typewindow 中，选择 "**对象**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-234">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
- <span data-ttu-id="bdd89-235">在 "选择数据对象" 对话框中，展开**WPFwithEFSample**   两次，然后选择 "**类别**"</span><span class="sxs-lookup"><span data-stu-id="bdd89-235">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="bdd89-236">*不需要选择**产品**数据源，因为我们将通过**类别**数据源上的**产品**属性来访问它。*</span><span class="sxs-lookup"><span data-stu-id="bdd89-236">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![选择数据对象](~/ef6/media/selectdataobjects.png)

- <span data-ttu-id="bdd89-238">单击“完成”。 </span><span class="sxs-lookup"><span data-stu-id="bdd89-238">Click **Finish.**</span></span>
- <span data-ttu-id="bdd89-239">如果 "数据源" 窗口未显示，请在 "Mainwindow.xaml" 窗口旁打开 "数据源" 窗口 *，选择 "**查看"-" &gt; 其他 Windows- &gt; 数据源**"*</span><span class="sxs-lookup"><span data-stu-id="bdd89-239">The Data Sources window is opened next to the MainWindow.xaml window   *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
- <span data-ttu-id="bdd89-240">按固定图标，使 "数据源" 窗口不会自动隐藏。</span><span class="sxs-lookup"><span data-stu-id="bdd89-240">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="bdd89-241">如果窗口已显示，可能需要按 "刷新" 按钮。</span><span class="sxs-lookup"><span data-stu-id="bdd89-241">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Data Sources](~/ef6/media/datasources.png)

- <span data-ttu-id="bdd89-243">选择 **类别** 数据源并将其拖到窗体上。</span><span class="sxs-lookup"><span data-stu-id="bdd89-243">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="bdd89-244">拖动此源时，会发生以下情况：</span><span class="sxs-lookup"><span data-stu-id="bdd89-244">The following happened when we dragged this source:</span></span>

- <span data-ttu-id="bdd89-245">**CategoryViewSource**资源和**categoryDataGrid**控件添加到了 XAML</span><span class="sxs-lookup"><span data-stu-id="bdd89-245">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span>
- <span data-ttu-id="bdd89-246">父网格元素上的 DataContext 属性已设置为 "{StaticResource **categoryViewSource** }"。</span><span class="sxs-lookup"><span data-stu-id="bdd89-246">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="bdd89-247">**CategoryViewSource**资源用作外部 \\ 父网格元素的绑定源。</span><span class="sxs-lookup"><span data-stu-id="bdd89-247"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="bdd89-248">然后，内部网格元素从父网格继承 DataContext 值， (categoryDataGrid 的 System.windows.controls.itemscontrol.itemssource 属性设置为 "{Binding}" ) </span><span class="sxs-lookup"><span data-stu-id="bdd89-248">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

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

## <a name="adding-a-details-grid"></a><span data-ttu-id="bdd89-249">添加“详细信息”网格</span><span class="sxs-lookup"><span data-stu-id="bdd89-249">Adding a Details Grid</span></span>

<span data-ttu-id="bdd89-250">现在，我们有了一个显示类别的网格，接下来要添加详细信息网格以显示关联的产品。</span><span class="sxs-lookup"><span data-stu-id="bdd89-250">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

- <span data-ttu-id="bdd89-251">从 **类别**数据源下面选择 **Products**属性，并将其拖到窗体上。</span><span class="sxs-lookup"><span data-stu-id="bdd89-251">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
  - <span data-ttu-id="bdd89-252">**CategoryProductsViewSource**资源和**productDataGrid**网格将添加到 XAML</span><span class="sxs-lookup"><span data-stu-id="bdd89-252">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
  - <span data-ttu-id="bdd89-253">此资源的绑定路径已设置为 "产品"</span><span class="sxs-lookup"><span data-stu-id="bdd89-253">The binding path for this resource is set to Products</span></span>
  - <span data-ttu-id="bdd89-254">WPF 数据绑定框架可确保仅在**productDataGrid**中显示与所选类别相关的产品</span><span class="sxs-lookup"><span data-stu-id="bdd89-254">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
- <span data-ttu-id="bdd89-255">从 "工具箱" 中，将 **按钮** 拖到窗体上。</span><span class="sxs-lookup"><span data-stu-id="bdd89-255">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="bdd89-256">将 " **名称** " 属性设置为 **buttonSave** ，将 " **Content** " 属性设置为 " **保存**"。</span><span class="sxs-lookup"><span data-stu-id="bdd89-256">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="bdd89-257">窗体应该如下所示：</span><span class="sxs-lookup"><span data-stu-id="bdd89-257">The form should look similar to this:</span></span>

![设计器窗体](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="bdd89-259">添加处理数据交互的代码</span><span class="sxs-lookup"><span data-stu-id="bdd89-259">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="bdd89-260">现在可以向主窗口添加一些事件处理程序了。</span><span class="sxs-lookup"><span data-stu-id="bdd89-260">It's time to add some event handlers to the main window.</span></span>

- <span data-ttu-id="bdd89-261">在 XAML 窗口中，单击\*\* &lt; window\*\*元素，这将选择主窗口</span><span class="sxs-lookup"><span data-stu-id="bdd89-261">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
- <span data-ttu-id="bdd89-262">在 " **属性** " 窗口中，选择右上角的 " **事件** "，然后双击 **加载** 的标签右侧的文本框</span><span class="sxs-lookup"><span data-stu-id="bdd89-262">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![主窗口属性](~/ef6/media/mainwindowproperties.png)

- <span data-ttu-id="bdd89-264">同时，通过双击设计器中的 "保存" 按钮，为 "**保存**" 按钮添加**Click**事件。</span><span class="sxs-lookup"><span data-stu-id="bdd89-264">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span>

<span data-ttu-id="bdd89-265">这会使你转到窗体的隐藏代码，现在我们将编辑代码以使用 ProductContext 来执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="bdd89-265">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="bdd89-266">更新 Mainwindow.xaml 的代码，如下所示。</span><span class="sxs-lookup"><span data-stu-id="bdd89-266">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="bdd89-267">该代码声明了 **ProductContext**的长时间运行的实例。</span><span class="sxs-lookup"><span data-stu-id="bdd89-267">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="bdd89-268">**ProductContext**对象用于查询数据并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-268">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="bdd89-269">然后，从重写的**OnClosing**方法中调用**ProductContext**实例上的**Dispose ( # B1** 。</span><span class="sxs-lookup"><span data-stu-id="bdd89-269">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="bdd89-270">代码注释提供有关代码的作用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bdd89-270"> The code comments provide details about what the code does.</span></span>

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

## <a name="test-the-wpf-application"></a><span data-ttu-id="bdd89-271">测试 WPF 应用程序</span><span class="sxs-lookup"><span data-stu-id="bdd89-271">Test the WPF Application</span></span>

- <span data-ttu-id="bdd89-272">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="bdd89-272">Compile and run the application.</span></span> <span data-ttu-id="bdd89-273">如果使用 Code First，则会看到为你创建 **WPFwithEFSample. ProductContext** 数据库。</span><span class="sxs-lookup"><span data-stu-id="bdd89-273">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
- <span data-ttu-id="bdd89-274">在顶部网格中输入类别名称，底部网格中的产品名称   *不要在 ID 列中输入任何内容，因为主键是由数据库生成的*</span><span class="sxs-lookup"><span data-stu-id="bdd89-274">Enter a category name in the top grid and product names in the bottom grid   *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![新类别和产品的主窗口](~/ef6/media/screen1.png)

- <span data-ttu-id="bdd89-276">按 " **保存** " 按钮将数据保存到数据库</span><span class="sxs-lookup"><span data-stu-id="bdd89-276">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="bdd89-277">调用 DbContext 的 \*\*SaveChanges ( # B1 \*\*后，将用数据库生成的值填充 id。</span><span class="sxs-lookup"><span data-stu-id="bdd89-277">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="bdd89-278">由于我们在**SaveChanges (** 之后调用了**Refresh ( # B1** ，因此还会更新**DataGrid**控件并将新值更新。</span><span class="sxs-lookup"><span data-stu-id="bdd89-278">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![主窗口，其中填充了 Id](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="bdd89-280">其他资源</span><span class="sxs-lookup"><span data-stu-id="bdd89-280">Additional Resources</span></span>

<span data-ttu-id="bdd89-281">若要了解有关使用 WPF 将数据绑定到集合的详细信息，请参阅 WPF 文档中的 [此主题](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) 。</span><span class="sxs-lookup"><span data-stu-id="bdd89-281">To learn more about data binding to collections using WPF, see [this topic](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
