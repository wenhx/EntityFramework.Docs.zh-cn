---
title: 通过 WinForms 进行数据绑定-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
ms.openlocfilehash: 4b3eee20ff238864b94ef4edfb97c1bae0713300
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181792"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="b9bdb-102">通过 WinForms 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="b9bdb-102">Databinding with WinForms</span></span>
<span data-ttu-id="b9bdb-103">此分步演练演示如何将 POCO 类型绑定到 "主/详细信息" 窗体中的窗口窗体（WinForms）控件。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-103">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="b9bdb-104">应用程序使用实体框架使用数据库中的数据填充对象、跟踪更改并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-104">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="b9bdb-105">该模型定义了两种参与一对多关系的类型：Category （principal @ no__t-0master）和 Product （依赖项 @ no__t-1detail）。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-105">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="b9bdb-106">然后，使用 Visual Studio 工具将模型中定义的类型绑定到 WinForms 控件。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="b9bdb-107">WinForms 数据绑定框架允许在相关对象之间导航：在主视图中选择行后，详细信息视图将更新为相应的子数据。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-107">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="b9bdb-108">本演练中的屏幕截图和代码清单取自 Visual Studio 2013，但你可以通过 Visual Studio 2012 或 Visual Studio 2010 完成此演练。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="b9bdb-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="b9bdb-109">Pre-Requisites</span></span>

<span data-ttu-id="b9bdb-110">需要安装 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-110">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="b9bdb-111">如果你使用的是 Visual Studio 2010，则还必须安装 NuGet。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-111">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="b9bdb-112">有关详细信息，请参阅[安装 NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-112">For more information, see [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="b9bdb-113">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="b9bdb-113">Create the Application</span></span>

-   <span data-ttu-id="b9bdb-114">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9bdb-114">Open Visual Studio</span></span>
-   <span data-ttu-id="b9bdb-115">**文件-&gt; &gt; 项目 ...。**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-115">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="b9bdb-116">在左侧窗格中选择 " **windows** "，在右窗格中选择 " **windows FormsApplication** "</span><span class="sxs-lookup"><span data-stu-id="b9bdb-116">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="b9bdb-117">输入**WinFormswithEFSample**作为名称</span><span class="sxs-lookup"><span data-stu-id="b9bdb-117">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="b9bdb-118">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="b9bdb-118">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="b9bdb-119">安装实体框架 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="b9bdb-119">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="b9bdb-120">在解决方案资源管理器中，右键单击**WinFormswithEFSample**项目</span><span class="sxs-lookup"><span data-stu-id="b9bdb-120">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="b9bdb-121">选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="b9bdb-121">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="b9bdb-122">在 "管理 NuGet 包" 对话框中，选择 "**联机**" 选项卡，然后选择 " **EntityFramework** " 包</span><span class="sxs-lookup"><span data-stu-id="b9bdb-122">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="b9bdb-123">单击 "**安装**"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-123">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="b9bdb-124">除了 EntityFramework 程序集之外，还添加了对 System.componentmodel 的引用。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-124">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="b9bdb-125">如果项目具有对 EntityFramework 的引用，则在安装包时将被删除。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-125">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="b9bdb-126">System.web 程序集不再用于实体框架6应用程序。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-126">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="b9bdb-127">为集合实现 IListSource</span><span class="sxs-lookup"><span data-stu-id="b9bdb-127">Implementing IListSource for Collections</span></span>

<span data-ttu-id="b9bdb-128">当使用 Windows 窗体时，集合属性必须实现 IListSource 接口以启用使用排序的双向数据绑定。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-128">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="b9bdb-129">为此，我们将扩展 ObservableCollection 以添加 IListSource 功能。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-129">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="b9bdb-130">将**ObservableListSource**类添加到项目：</span><span class="sxs-lookup"><span data-stu-id="b9bdb-130">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="b9bdb-131">右键单击项目名称</span><span class="sxs-lookup"><span data-stu-id="b9bdb-131">Right-click on the project name</span></span>
    -   <span data-ttu-id="b9bdb-132">选择 **&gt; 新项**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-132">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="b9bdb-133">选择**类**，并输入**ObservableListSource**作为类名</span><span class="sxs-lookup"><span data-stu-id="b9bdb-133">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="b9bdb-134">将默认生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9bdb-134">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="b9bdb-135">@no__t 0This 类启用双向数据绑定和排序。该类派生自 ObservableCollection @ no__t-0T @ no__t，并添加了 IListSource 的显式实现。实现 IListSource 的执行 getlist （）方法以返回保持与 ObservableCollection 同步的 IBindingList 实现。对 tobindinglist 获得生成的 IBindingList 实现支持排序。对 tobindinglist 获得扩展方法在 EntityFramework 程序集中定义。 \*</span><span class="sxs-lookup"><span data-stu-id="b9bdb-135">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extension method is defined in the EntityFramework assembly.*</span></span>

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a><span data-ttu-id="b9bdb-136">定义模型</span><span class="sxs-lookup"><span data-stu-id="b9bdb-136">Define a Model</span></span>

<span data-ttu-id="b9bdb-137">在本演练中，您可以使用 Code First 或 EF 设计器选择实现模型。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-137">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="b9bdb-138">完成以下两个部分中的一个。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-138">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="b9bdb-139">选项 1：使用 Code First 定义模型</span><span class="sxs-lookup"><span data-stu-id="b9bdb-139">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="b9bdb-140">本部分说明如何使用 Code First 创建模型及其关联的数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-140">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="b9bdb-141">跳到下一部分（@no__t 0Option 2：使用 Database First）定义模型（如果要 Database First 使用 EF 设计器从数据库中反向工程模型，则 \*\*）</span><span class="sxs-lookup"><span data-stu-id="b9bdb-141">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="b9bdb-142">使用 Code First 开发时，通常首先编写定义概念（域）模型 .NET Framework 类。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-142">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="b9bdb-143">将新**产品**类添加到项目</span><span class="sxs-lookup"><span data-stu-id="b9bdb-143">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="b9bdb-144">将默认生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9bdb-144">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   <span data-ttu-id="b9bdb-145">将**Category**类添加到项目。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-145">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="b9bdb-146">将默认生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9bdb-146">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

<span data-ttu-id="b9bdb-147">除了定义实体外，还需要定义派生自**DbContext**的类，并公开**DbSet @ no__t-2TEntity @ no__t**属性。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-147">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="b9bdb-148">**DbSet**属性让上下文知道要包括在模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-148">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="b9bdb-149">**DbContext**和**DbSet**类型是在 EntityFramework 程序集中定义的。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-149">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="b9bdb-150">DbContext 派生类型的实例在运行时管理实体对象，这包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-150">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="b9bdb-151">向项目中添加一个新的**ProductContext**类。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-151">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="b9bdb-152">将默认生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9bdb-152">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="b9bdb-153">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="b9bdb-154">选项 2：使用 Database First 定义模型</span><span class="sxs-lookup"><span data-stu-id="b9bdb-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="b9bdb-155">本部分说明如何使用 Database First 从使用 EF 设计器的数据库中对模型进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="b9bdb-156">如果已完成上一部分（@no__t 0Option 1：使用 Code First） \*\* 定义模型，然后跳过此部分，直接转到**延迟加载**部分。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="b9bdb-157">创建现有数据库</span><span class="sxs-lookup"><span data-stu-id="b9bdb-157">Create an Existing Database</span></span>

<span data-ttu-id="b9bdb-158">通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="b9bdb-159">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="b9bdb-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="b9bdb-160">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="b9bdb-161">如果使用的是 Visual Studio 2012，则将创建一个[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="b9bdb-162">接下来，生成数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="b9bdb-163">**视图-&gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="b9bdb-164">右键单击 "**数据连接-&gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="b9bdb-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="b9bdb-165">如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源</span><span class="sxs-lookup"><span data-stu-id="b9bdb-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![更改数据源](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="b9bdb-167">连接到 LocalDB 或 SQL Express，具体取决于已安装的数据，并输入**Products**作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="b9bdb-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![添加连接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![添加连接 Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="b9bdb-170">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![创建数据库](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="b9bdb-172">新数据库现在将出现在服务器资源管理器中，右键单击该数据库并选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="b9bdb-173">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="b9bdb-174">反向工程模型</span><span class="sxs-lookup"><span data-stu-id="b9bdb-174">Reverse Engineer Model</span></span>

<span data-ttu-id="b9bdb-175">我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="b9bdb-176">**项目-@no__t "添加新项 ..."**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="b9bdb-177">从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="b9bdb-178">输入**ProductModel**作为名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="b9bdb-179">这将启动**实体数据模型向导**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="b9bdb-180">选择 "**从数据库生成**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-180">Select **Generate from Database** and click **Next**</span></span>

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="b9bdb-182">选择在第一部分中创建的数据库的连接，输入 " **ProductContext** " 作为连接字符串的名称，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![选择连接](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="b9bdb-184">单击 "表" 旁边的复选框以导入所有表，然后单击 "完成"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![选择对象](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="b9bdb-186">反向工程过程完成后，会将新模型添加到项目中，并打开，以便在 Entity Framework Designer 中查看。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="b9bdb-187">App.config 文件也已添加到您的项目中，其中包含数据库的连接详细信息。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="b9bdb-188">Visual Studio 2010 中的其他步骤</span><span class="sxs-lookup"><span data-stu-id="b9bdb-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="b9bdb-189">如果使用的是 Visual Studio 2010，则需要更新 EF 设计器以使用 EF6 代码生成。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="b9bdb-190">在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "</span><span class="sxs-lookup"><span data-stu-id="b9bdb-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="b9bdb-191">从左侧菜单中选择 "**联机模板**"，然后搜索**DbContext**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="b9bdb-192">选择**用于 C @ no__t 的 EF DbContext 生成器-1，** 输入**ProductsModel**作为名称，然后单击 "添加"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="b9bdb-193">更新数据绑定的代码生成</span><span class="sxs-lookup"><span data-stu-id="b9bdb-193">Updating code generation for data binding</span></span>

<span data-ttu-id="b9bdb-194">EF 使用 T4 模板从模型生成代码。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="b9bdb-195">Visual Studio 附带的模板或从 Visual Studio 库下载的模板专用于一般用途。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="b9bdb-196">这意味着从这些模板生成的实体具有简单的 ICollection @ no__t-0T @ no__t-1 属性。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="b9bdb-197">但是，在执行数据绑定时，需要具有实现 IListSource 的集合属性。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-197">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="b9bdb-198">这就是我们创建上述 ObservableListSource 类的原因，现在我们要修改模板以使用此类。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-198">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="b9bdb-199">打开**解决方案资源管理器**并查找**ProductModel**文件</span><span class="sxs-lookup"><span data-stu-id="b9bdb-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="b9bdb-200">查找**ProductModel.tt**文件，该文件将嵌套在 ProductModel 文件下</span><span class="sxs-lookup"><span data-stu-id="b9bdb-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![产品型号模板](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="b9bdb-202">双击 ProductModel.tt 文件以在 Visual Studio 编辑器中将其打开</span><span class="sxs-lookup"><span data-stu-id="b9bdb-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="b9bdb-203">查找并将 "**ICollection**" 的两个匹配项替换为 "**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="b9bdb-204">这些代码位于大约第296行和第484行。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-204">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="b9bdb-205">查找并将 "**HashSet**" 的第一个匹配项替换为 "**ObservableListSource**"。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="b9bdb-206">此事件大约位于第50行。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-206">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="b9bdb-207">**请勿**替换稍后在代码中找到的第二个 HashSet。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="b9bdb-208">保存 ProductModel.tt 文件。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-208">Save the ProductModel.tt file.</span></span> <span data-ttu-id="b9bdb-209">这会导致重新生成实体的代码。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-209">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="b9bdb-210">如果代码未自动重新生成，则右键单击 ProductModel.tt 并选择 "运行自定义工具"。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-210">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="b9bdb-211">如果现在打开 Category.cs 文件（嵌套在 ProductModel.tt 下），则应看到 Products 集合的类型为**ObservableListSource @ no__t-1Product @ no__t-2**。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-211">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="b9bdb-212">编译该项目。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-212">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="b9bdb-213">延迟加载</span><span class="sxs-lookup"><span data-stu-id="b9bdb-213">Lazy Loading</span></span>

<span data-ttu-id="b9bdb-214">**Product**类上 "**类别**类" 和 "**类别**" 属性的 "**产品**" 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-214">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="b9bdb-215">在实体框架中，导航属性提供了一种方法，用于在两个实体类型之间导航关系。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-215">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="b9bdb-216">当首次访问导航属性时，EF 使你可以选择自动从数据库加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-216">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="b9bdb-217">使用这种类型的加载（称为 "延迟加载"）时，请注意，第一次访问每个导航属性时，将对数据库执行单独的查询（如果内容尚未出现在上下文中）。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-217">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="b9bdb-218">使用 POCO 实体类型时，EF 通过在运行时创建派生代理类型的实例，然后重写类中的虚拟属性来添加加载挂钩来实现延迟加载。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-218">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="b9bdb-219">若要获取相关对象的延迟加载，必须将导航属性 getter 声明为**公共**和**虚拟**（在 Visual Basic 中可**重写**），并且不能**密封**类（Visual Basic**NotOverridable** ）。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-219">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="b9bdb-220">使用时，会自动将 Database First 导航属性设为虚拟，以启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-220">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="b9bdb-221">在 "Code First" 部分中，我们选择将导航属性设置为虚拟，因为同一原因</span><span class="sxs-lookup"><span data-stu-id="b9bdb-221">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="b9bdb-222">将对象绑定到控件</span><span class="sxs-lookup"><span data-stu-id="b9bdb-222">Bind Object to Controls</span></span>

<span data-ttu-id="b9bdb-223">将在模型中定义的类添加为此 WinForms 应用程序的数据源。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-223">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="b9bdb-224">从主菜单中，选择 "**项目-&gt; 添加新数据源 ...** "</span><span class="sxs-lookup"><span data-stu-id="b9bdb-224">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="b9bdb-225">（在 Visual Studio 2010 中，需要选择 "**数据 &gt; 添加新数据源 ...** "）</span><span class="sxs-lookup"><span data-stu-id="b9bdb-225">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="b9bdb-226">在 "选择数据源类型" 窗口中，选择 "**对象**"，然后单击 "**下一步**"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-226">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="b9bdb-227">在 "选择数据对象" 对话框中，展开**WinFormswithEFSample**两次，然后选择 "**类别**"，因为我们将通过类别数据源上的产品属性来获取产品数据源。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-227">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![“数据源”](~/ef6/media/datasource.png)

-   <span data-ttu-id="b9bdb-229">单击 "**完成"。**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-229">Click **Finish.**</span></span>
    <span data-ttu-id="b9bdb-230">如果 "数据源" 窗口未显示，请选择 "**视图 &gt;" 其他 Windows &gt; 数据源**</span><span class="sxs-lookup"><span data-stu-id="b9bdb-230">If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources**</span></span>
-   <span data-ttu-id="b9bdb-231">按固定图标，使 "数据源" 窗口不会自动隐藏。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-231">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="b9bdb-232">如果窗口已显示，可能需要按 "刷新" 按钮。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-232">You may need to hit the refresh button if the window was already visible.</span></span>

    ![数据源2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="b9bdb-234">在解决方案资源管理器中，双击**Form1.cs**文件以在设计器中打开主窗体。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-234">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="b9bdb-235">选择**类别**数据源并将其拖到窗体上。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-235">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="b9bdb-236">默认情况下，新的 DataGridView （**categoryDataGridView**）和导航工具栏控件将添加到设计器中。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-236">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="b9bdb-237">这些控件绑定到创建的 BindingSource （**categoryBindingSource**）和绑定导航器（**categoryBindingNavigator**）组件。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-237">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="b9bdb-238">编辑**categoryDataGridView**上的列。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-238">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="b9bdb-239">我们想要将 "**类别 id** " 列设置为只读。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-239">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="b9bdb-240">保存数据后，数据库将生成 "**类别 id** " 属性的值。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-240">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="b9bdb-241">右键单击 DataGridView 控件并选择 "编辑列"。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-241">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="b9bdb-242">选择 "类别 Id" 列并将 "ReadOnly" 设置为 True</span><span class="sxs-lookup"><span data-stu-id="b9bdb-242">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="b9bdb-243">按 "确定"</span><span class="sxs-lookup"><span data-stu-id="b9bdb-243">Press OK</span></span>
-   <span data-ttu-id="b9bdb-244">从类别数据源下选择 "产品"，并将其拖到窗体上。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-244">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="b9bdb-245">ProductDataGridView 和为 productbindingsource 将添加到窗体中。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-245">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="b9bdb-246">编辑 productDataGridView 上的列。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-246">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="b9bdb-247">我们想要隐藏类别 Id 和类别列，并将 ProductId 设置为只读。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-247">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="b9bdb-248">在保存数据后，数据库将生成 ProductId 属性的值。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-248">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="b9bdb-249">右键单击 DataGridView 控件，然后选择 "**编辑列 ...** "。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-249">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="b9bdb-250">选择 " **ProductId** " 列并将 " **ReadOnly** " 设置为**True**。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-250">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="b9bdb-251">选择 "**类别 id** " 列并按 "**删除**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-251">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="b9bdb-252">对**Category**列执行相同的操作。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-252">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="b9bdb-253">按**确定**。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-253">Press **OK**.</span></span>

    <span data-ttu-id="b9bdb-254">到目前为止，我们在设计器中将 DataGridView 控件与 BindingSource 组件相关联。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-254">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="b9bdb-255">在下一部分中，我们将向隐藏代码中添加代码，以便将 categoryBindingSource 设置为 DbContext 当前跟踪的实体集合。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-255">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="b9bdb-256">当我们从类别中拖放产品时，WinForms 会将 productsBindingSource 属性设置为 "categoryBindingSource" 和 "productsBindingSource" 属性设置为 "产品"。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-256">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="b9bdb-257">由于此绑定，只会在 productDataGridView 中显示属于当前选定类别的产品。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-257">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="b9bdb-258">通过单击鼠标右键并选择 "**启用**"，启用导航工具栏上的 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-258">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![窗体1设计器](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="b9bdb-260">双击按钮，为 "保存" 按钮添加事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-260">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="b9bdb-261">这将添加事件处理程序，并将你带入窗体的隐藏代码。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-261">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="b9bdb-262">将在下一节中添加**categoryBindingNavigatorSaveItem @ no__t-1Click**事件处理程序的代码。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-262">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="b9bdb-263">添加处理数据交互的代码</span><span class="sxs-lookup"><span data-stu-id="b9bdb-263">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="b9bdb-264">现在，我们将添加代码以使用 ProductContext 来执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-264">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="b9bdb-265">更新主窗体窗口的代码，如下所示。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-265">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="b9bdb-266">该代码声明了 ProductContext 的长时间运行的实例。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-266">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="b9bdb-267">ProductContext 对象用于查询数据并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-267">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="b9bdb-268">然后，从重写的 OnClosing 方法中调用 ProductContext 实例上的 Dispose （）方法。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-268">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="b9bdb-269">代码注释提供有关代码的作用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-269">The code comments provide details about what the code does.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="b9bdb-270">测试 Windows 窗体应用程序</span><span class="sxs-lookup"><span data-stu-id="b9bdb-270">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="b9bdb-271">编译并运行应用程序，可以测试功能。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-271">Compile and run the application and you can test out the functionality.</span></span>

    ![保存前窗体1](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="b9bdb-273">保存存储生成的密钥后，会显示在屏幕上。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-273">After saving the store generated keys are shown on the screen.</span></span>

    ![保存后窗体1](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="b9bdb-275">如果使用 Code First，则还会看到为你创建**WinFormswithEFSample. ProductContext**数据库。</span><span class="sxs-lookup"><span data-stu-id="b9bdb-275">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![服务器对象资源管理器](~/ef6/media/serverobjexplorer.png)
