---
title: WPF 入门 - EF Core
description: 将 WPF 与 Entity Framework Core 结合使用的入门教程
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 1198da5c9564663ca26392b33462c727275a432d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619307"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="f8fc3-103">WPF 入门</span><span class="sxs-lookup"><span data-stu-id="f8fc3-103">Getting Started with WPF</span></span>

<span data-ttu-id="f8fc3-104">此分步演练演示如何将 POCO 类型绑定到“主要详细信息”窗体中的 WPF 控件。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-104">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="f8fc3-105">应用程序使用实体框架 API，用数据库中的数据填充对象、跟踪更改并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-105">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="f8fc3-106">该模型定义了两种参与一对多关系的类型：类别（主体\\主要）和产品（依赖\\详细信息）。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-106">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="f8fc3-107">WPF 数据绑定框架允许在相关对象之间导航：在主视图中选择行将导致详细信息视图使用相应的子数据进行更新。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="f8fc3-108">本演练中的屏幕截图和代码清单取自 Visual Studio 2019 16.6.5。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="f8fc3-109">可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF)中查看此文章的示例。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-109">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="f8fc3-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="f8fc3-110">Pre-Requisites</span></span>

* <span data-ttu-id="f8fc3-111">需要将 Visual Studio 2019 16.3 或更高版本随 .NET 桌面工作负载一起安装才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-111">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="f8fc3-112">有关安装最新版本的 Visual Studio 的详细信息，请参阅[安装 Visual Studio](/visualstudio/install/install-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-112">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="f8fc3-113">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="f8fc3-113">Create the Application</span></span>

1. <span data-ttu-id="f8fc3-114">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8fc3-114">Open Visual Studio</span></span>
2. <span data-ttu-id="f8fc3-115">在“开始”窗口上，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-115">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="f8fc3-116">搜索“WPF”，选择“WPF 应用(.NET Core)”，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-116">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="f8fc3-117">在下一个屏幕中，为项目命名（例如“GetStartedWPF”），然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-117">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="f8fc3-118">安装实体框架 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="f8fc3-118">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="f8fc3-119">右键单击解决方案，然后选择“管理解决方案的 NuGet 包…”</span><span class="sxs-lookup"><span data-stu-id="f8fc3-119">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![管理 NuGet 包](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="f8fc3-121">在搜索框中键入“`entityframeworkcore.sqlite`”。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-121">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="f8fc3-122">选择“Microsoft.EntityFrameworkCore.Sqlite”包。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-122">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="f8fc3-123">在右窗格中选中项目，然后单击“安装”</span><span class="sxs-lookup"><span data-stu-id="f8fc3-123">Check the project in the right pane and click **Install**</span></span>

    ![Sqlite 包](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="f8fc3-125">重复上述步骤，搜索 `entityframeworkcore.proxies` 并安装“Microsoft.EntityFrameworkCore.Proxies”。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-125">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="f8fc3-126">安装 Sqlite 包后，将自动下拉相关的 Microsoft.EntityFrameworkCore 基础包。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-126">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="f8fc3-127">Microsoft.EntityFrameworkCore.Proxies 包提供对“延迟加载”数据的支持。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-127">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="f8fc3-128">这意味着，如果具有包含子实体的实体，则在初始加载时仅提取父项。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-128">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="f8fc3-129">代理检测何时尝试访问子实体，并根据需要自动加载它们。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-129">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="f8fc3-130">定义模型</span><span class="sxs-lookup"><span data-stu-id="f8fc3-130">Define a Model</span></span>

<span data-ttu-id="f8fc3-131">在本演练中，你将使用“Code First”实现模型。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-131">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="f8fc3-132">这意味着，EF Core 将基于你定义的 C# 类创建数据库表和架构。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-132">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="f8fc3-133">添加新类。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-133">Add a new class.</span></span> <span data-ttu-id="f8fc3-134">为其命名：`Product.cs` 并按如下所示填充：</span><span class="sxs-lookup"><span data-stu-id="f8fc3-134">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="f8fc3-135">接下来，添加一个名为 `Category.cs` 的类，并使用以下代码进行填充：</span><span class="sxs-lookup"><span data-stu-id="f8fc3-135">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="f8fc3-136">“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-136">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="f8fc3-137">在实体框架中，导航属性提供了一种在两个实体类型之间导航关系的方法。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-137">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="f8fc3-138">除了定义实体外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt; 属性的类。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-138">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="f8fc3-139">DbSet&lt;TEntity&gt; 属性让上下文知道要包括在模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-139">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="f8fc3-140">DbContext 派生类型的实例在运行时管理实体对象，其中包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-140">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="f8fc3-141">使用以下定义向项目添加新的 `ProductContext.cs` 类：</span><span class="sxs-lookup"><span data-stu-id="f8fc3-141">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="f8fc3-142">`DbSet` 通知 EF Core 哪些 C# 实体应映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-142">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="f8fc3-143">可通过多种方式配置 EF Core `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-143">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="f8fc3-144">你可以从以下位置了解相关信息：[配置 DbContext](xref:core/miscellaneous/configuring-dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-144">You can read about them in: [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="f8fc3-145">此示例使用 `OnConfiguring` 重写来指定 Sqlite 数据文件。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-145">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="f8fc3-146">`UseLazyLoadingProxies` 调用指示 EF Core 实现延迟加载，因此从父实体访问时，会自动加载子实体。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-146">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="f8fc3-147">按 CTRL+SHIFT+B 或导航到“生成”&gt;“生成解决方案”来编译项目。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-147">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="f8fc3-148">了解使数据库和 EF Core 模型保持同步的不同方式：[管理数据库架构](xref:core/managing-schemas/index)。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-148">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](xref:core/managing-schemas/index).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="f8fc3-149">延迟加载</span><span class="sxs-lookup"><span data-stu-id="f8fc3-149">Lazy Loading</span></span>

<span data-ttu-id="f8fc3-150">“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-150">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="f8fc3-151">在 Entity Framework Core 中，导航属性提供了一种在两个实体类型之间导航关系的方法。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-151">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="f8fc3-152">EF Core 提供了在首次访问导航属性时自动从数据库加载相关实体的选项。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-152">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="f8fc3-153">如果使用此类型的加载（称为“延迟加载”），请注意，首次访问每个导航属性时，将对数据库执行单独的查询（如果内容不在上下文中）。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-153">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="f8fc3-154">使用“普通旧 C# 对象”(POCO) 实体类型时，EF Core 通过在运行时创建派生代理类型的实例，然后重写类中的虚拟属性以添加加载挂钩，来实现延迟加载。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-154">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="f8fc3-155">若要获取相关对象的延迟加载，必须将导航属性 getter 声明为“公共”和“虚拟”（在 Visual Basic 中为 Overridable），并且不得密封类（在 Visual Basic 中为 NotOverridable）。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-155">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="f8fc3-156">使用 Database First 时，会自动将导航属性设为虚拟，以启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-156">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="f8fc3-157">将对象绑定到控件</span><span class="sxs-lookup"><span data-stu-id="f8fc3-157">Bind Object to Controls</span></span>

<span data-ttu-id="f8fc3-158">将在模型中定义的类作为此 WPF 应用程序的数据源添加。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-158">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="f8fc3-159">在“解决方案资源管理器”中双击 MainWindow.xaml 以打开主窗体</span><span class="sxs-lookup"><span data-stu-id="f8fc3-159">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="f8fc3-160">选择“XAML”选项卡以编辑 XAML。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-160">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="f8fc3-161">紧接开始 `Window` 标记之后添加以下源，以连接到 EF Core 实体。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-161">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="f8fc3-162">这样会为“父”类别设置源，并为“详细信息”产品设置第二个源。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-162">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="f8fc3-163">接下来，紧接结束 `Window.Resources` 标记之后，将以下标记添加到 XAML。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-163">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="f8fc3-164">请注意，`CategoryId` 设置为 `ReadOnly`，因为它是由数据库分配的，无法更改。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-164">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="f8fc3-165">添加“详细信息”网格</span><span class="sxs-lookup"><span data-stu-id="f8fc3-165">Adding a Details Grid</span></span>

<span data-ttu-id="f8fc3-166">现在，网格可以显示类别，可以添加“详细信息”网格来显示产品。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-166">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="f8fc3-167">最后，将 click 事件中的 `Save` 按钮和电线添加到 `Button_Click`。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-167">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="f8fc3-168">设计视图应如下所示：</span><span class="sxs-lookup"><span data-stu-id="f8fc3-168">Your design view should look like this:</span></span>

![WPF 设计器的屏幕截图](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="f8fc3-170">添加处理数据交互的代码</span><span class="sxs-lookup"><span data-stu-id="f8fc3-170">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="f8fc3-171">现在可以向主窗口添加一些事件处理程序了。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-171">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="f8fc3-172">在“XAML”窗口中，单击“&lt;窗口&gt;”元素，以选择主窗口。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-172">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="f8fc3-173">在“属性”窗口中，选择右上角的“事件”，然后双击“已加载”标签右侧的文本框。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-173">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![主窗口属性](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="f8fc3-175">这会使你转到窗体隐藏的代码，现在我们将编辑代码以使用 `ProductContext` 执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-175">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="f8fc3-176">更新代码，如下所示。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-176">Update the code as shown below.</span></span>

<span data-ttu-id="f8fc3-177">代码声明 `ProductContext` 的长时间运行的实例。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-177">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="f8fc3-178">`ProductContext` 对象用于查询数据并将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-178">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="f8fc3-179">然后，从重写的 `OnClosing` 方法中调用 `ProductContext` 实例上的 `Dispose()` 方法。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-179">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="f8fc3-180">代码注释说明了每个步骤的作用。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-180"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="f8fc3-181">代码使用对 `EnsureCreated()` 的调用在首次运行时生成数据库。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-181">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="f8fc3-182">这对于演示是可接受的，但在生产应用中，应查看[迁移](xref:core/managing-schemas/migrations/index)来管理你的架构。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-182">This is acceptable for demos, but in production apps you should look at [migrations](xref:core/managing-schemas/migrations/index) to manage your schema.</span></span> <span data-ttu-id="f8fc3-183">代码还会同步执行，因为它使用本地 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-183">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="f8fc3-184">对于通常涉及远程服务器的生产方案，请考虑使用 `Load` 和 `SaveChanges` 方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-184">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="f8fc3-185">测试 WPF 应用程序</span><span class="sxs-lookup"><span data-stu-id="f8fc3-185">Test the WPF Application</span></span>

<span data-ttu-id="f8fc3-186">按 F5 或选择“调试”&gt;“开始调试”以编译和运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-186">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="f8fc3-187">数据库应会自动使用名为 `products.db` 的文件创建。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-187">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="f8fc3-188">输入类别名称并按 Enter，然后将产品添加到下部网格。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-188">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="f8fc3-189">单击“保存”，并使用数据库提供的 ID 查看网格刷新。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-189">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="f8fc3-190">突出显示某行，然后按 Delete 以删除该行。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-190">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="f8fc3-191">单击“保存”后，将删除实体。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-191">The entity will be deleted when you click **Save**.</span></span>

![正在运行的应用程序](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="f8fc3-193">属性更改通知</span><span class="sxs-lookup"><span data-stu-id="f8fc3-193">Property Change Notification</span></span>

<span data-ttu-id="f8fc3-194">此示例依赖于四个步骤来将实体与 UI 同步。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-194">This example relies on four steps to synchronize the entities with the UI.</span></span>

1. <span data-ttu-id="f8fc3-195">初始调用 `_context.Categories.Load()` 加载类别数据。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-195">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="f8fc3-196">延迟加载代理加载相关的产品数据。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-196">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="f8fc3-197">EF Core 的内置更改跟踪在调用 `_context.SaveChanges()` 时对实体进行必要的修改（包括插入和删除）。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-197">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="f8fc3-198">对 `DataGridView.Items.Refresh()` 的调用将使用新生成的 ID 强制重载。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-198">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="f8fc3-199">这适用于我们的入门示例，但对于其他方案，你可能需要其他代码。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-199">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="f8fc3-200">WPF 控件通过读取实体上的字段和属性来呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-200">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="f8fc3-201">当你在用户界面 (UI) 中编辑某个值时，该值将传递给你的实体。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-201">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="f8fc3-202">直接在实体上更改属性值（例如从数据库中加载它）时，WPF 不会立即反映 UI 中的更改。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-202">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="f8fc3-203">必须向呈现引擎通知更改。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-203">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="f8fc3-204">项目通过手动调用 `Refresh()` 来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-204">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="f8fc3-205">自动发出此通知的简单方法是实现 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) 接口。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-205">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="f8fc3-206">WPF 组件会自动检测接口并注册更改事件。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-206">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="f8fc3-207">实体负责引发这些事件。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-207">The entity is responsible for raising these events.</span></span>

> [!TIP]
> <span data-ttu-id="f8fc3-208">若要详细了解如何处理更改，请参阅：[如何实现属性更改通知](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-208">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f8fc3-209">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f8fc3-209">Next Steps</span></span>

<span data-ttu-id="f8fc3-210">了解有关[配置 DbContext](xref:core/miscellaneous/configuring-dbcontext) 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f8fc3-210">Learn more about [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
