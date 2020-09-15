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
# <a name="getting-started-with-wpf"></a>WPF 入门

此分步演练演示如何将 POCO 类型绑定到“主要详细信息”窗体中的 WPF 控件。 应用程序使用实体框架 API，用数据库中的数据填充对象、跟踪更改并将数据保存到数据库。

该模型定义了两种参与一对多关系的类型：类别（主体\\主要）和产品（依赖\\详细信息）。 WPF 数据绑定框架允许在相关对象之间导航：在主视图中选择行将导致详细信息视图使用相应的子数据进行更新。

本演练中的屏幕截图和代码清单取自 Visual Studio 2019 16.6.5。

> [!TIP]
> 可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF)中查看此文章的示例。

## <a name="pre-requisites"></a>先决条件

* 需要将 Visual Studio 2019 16.3 或更高版本随 .NET 桌面工作负载一起安装才能完成此演练。
    
    有关安装最新版本的 Visual Studio 的详细信息，请参阅[安装 Visual Studio](/visualstudio/install/install-visual-studio)。

## <a name="create-the-application"></a>创建应用程序

1. 打开 Visual Studio
2. 在“开始”窗口上，选择“创建新项目”。
3. 搜索“WPF”，选择“WPF 应用(.NET Core)”，然后选择“下一步”。
4. 在下一个屏幕中，为项目命名（例如“GetStartedWPF”），然后选择“创建”。

## <a name="install-the-entity-framework-nuget-packages"></a>安装实体框架 NuGet 包

1. 右键单击解决方案，然后选择“管理解决方案的 NuGet 包…”

    ![管理 NuGet 包](_static/wpf-tutorial-nuget.jpg)

1. 在搜索框中键入“`entityframeworkcore.sqlite`”。
1. 选择“Microsoft.EntityFrameworkCore.Sqlite”包。
1. 在右窗格中选中项目，然后单击“安装”

    ![Sqlite 包](_static/wpf-tutorial-sqlite.jpg)

1. 重复上述步骤，搜索 `entityframeworkcore.proxies` 并安装“Microsoft.EntityFrameworkCore.Proxies”。

> [!NOTE]
> 安装 Sqlite 包后，将自动下拉相关的 Microsoft.EntityFrameworkCore 基础包。 Microsoft.EntityFrameworkCore.Proxies 包提供对“延迟加载”数据的支持。 这意味着，如果具有包含子实体的实体，则在初始加载时仅提取父项。 代理检测何时尝试访问子实体，并根据需要自动加载它们。 

## <a name="define-a-model"></a>定义模型

在本演练中，你将使用“Code First”实现模型。 这意味着，EF Core 将基于你定义的 C# 类创建数据库表和架构。

添加新类。 为其命名：`Product.cs` 并按如下所示填充：

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

接下来，添加一个名为 `Category.cs` 的类，并使用以下代码进行填充：

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。 在实体框架中，导航属性提供了一种在两个实体类型之间导航关系的方法。

除了定义实体外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt; 属性的类。 DbSet&lt;TEntity&gt; 属性让上下文知道要包括在模型中的类型。

DbContext 派生类型的实例在运行时管理实体对象，其中包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。

使用以下定义向项目添加新的 `ProductContext.cs` 类：

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet` 通知 EF Core 哪些 C# 实体应映射到数据库。
* 可通过多种方式配置 EF Core `DbContext`。 你可以从以下位置了解相关信息：[配置 DbContext](xref:core/miscellaneous/configuring-dbcontext)。
* 此示例使用 `OnConfiguring` 重写来指定 Sqlite 数据文件。
* `UseLazyLoadingProxies` 调用指示 EF Core 实现延迟加载，因此从父实体访问时，会自动加载子实体。

按 CTRL+SHIFT+B 或导航到“生成”&gt;“生成解决方案”来编译项目。

> [!TIP]
> 了解使数据库和 EF Core 模型保持同步的不同方式：[管理数据库架构](xref:core/managing-schemas/index)。

## <a name="lazy-loading"></a>延迟加载

“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。 在 Entity Framework Core 中，导航属性提供了一种在两个实体类型之间导航关系的方法。

EF Core 提供了在首次访问导航属性时自动从数据库加载相关实体的选项。 如果使用此类型的加载（称为“延迟加载”），请注意，首次访问每个导航属性时，将对数据库执行单独的查询（如果内容不在上下文中）。

使用“普通旧 C# 对象”(POCO) 实体类型时，EF Core 通过在运行时创建派生代理类型的实例，然后重写类中的虚拟属性以添加加载挂钩，来实现延迟加载。 若要获取相关对象的延迟加载，必须将导航属性 getter 声明为“公共”和“虚拟”（在 Visual Basic 中为 Overridable），并且不得密封类（在 Visual Basic 中为 NotOverridable）。 使用 Database First 时，会自动将导航属性设为虚拟，以启用延迟加载。 

## <a name="bind-object-to-controls"></a>将对象绑定到控件

将在模型中定义的类作为此 WPF 应用程序的数据源添加。

1. 在“解决方案资源管理器”中双击 MainWindow.xaml 以打开主窗体
1. 选择“XAML”选项卡以编辑 XAML。
1. 紧接开始 `Window` 标记之后添加以下源，以连接到 EF Core 实体。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. 这样会为“父”类别设置源，并为“详细信息”产品设置第二个源。
1. 接下来，紧接结束 `Window.Resources` 标记之后，将以下标记添加到 XAML。

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. 请注意，`CategoryId` 设置为 `ReadOnly`，因为它是由数据库分配的，无法更改。

## <a name="adding-a-details-grid"></a>添加“详细信息”网格

现在，网格可以显示类别，可以添加“详细信息”网格来显示产品。

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

最后，将 click 事件中的 `Save` 按钮和电线添加到 `Button_Click`。

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

设计视图应如下所示：

![WPF 设计器的屏幕截图](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>添加处理数据交互的代码

现在可以向主窗口添加一些事件处理程序了。

1. 在“XAML”窗口中，单击“&lt;窗口&gt;”元素，以选择主窗口。
1. 在“属性”窗口中，选择右上角的“事件”，然后双击“已加载”标签右侧的文本框。

    ![主窗口属性](_static/wpf-tutorial-loaded.jpg)

这会使你转到窗体隐藏的代码，现在我们将编辑代码以使用 `ProductContext` 执行数据访问。 更新代码，如下所示。

代码声明 `ProductContext` 的长时间运行的实例。 `ProductContext` 对象用于查询数据并将数据保存到数据库。 然后，从重写的 `OnClosing` 方法中调用 `ProductContext` 实例上的 `Dispose()` 方法。代码注释说明了每个步骤的作用。

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> 代码使用对 `EnsureCreated()` 的调用在首次运行时生成数据库。 这对于演示是可接受的，但在生产应用中，应查看[迁移](xref:core/managing-schemas/migrations/index)来管理你的架构。 代码还会同步执行，因为它使用本地 SQLite 数据库。 对于通常涉及远程服务器的生产方案，请考虑使用 `Load` 和 `SaveChanges` 方法的异步版本。

## <a name="test-the-wpf-application"></a>测试 WPF 应用程序

按 F5 或选择“调试”&gt;“开始调试”以编译和运行应用程序。 数据库应会自动使用名为 `products.db` 的文件创建。 输入类别名称并按 Enter，然后将产品添加到下部网格。 单击“保存”，并使用数据库提供的 ID 查看网格刷新。 突出显示某行，然后按 Delete 以删除该行。 单击“保存”后，将删除实体。

![正在运行的应用程序](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>属性更改通知

此示例依赖于四个步骤来将实体与 UI 同步。

1. 初始调用 `_context.Categories.Load()` 加载类别数据。
1. 延迟加载代理加载相关的产品数据。
1. EF Core 的内置更改跟踪在调用 `_context.SaveChanges()` 时对实体进行必要的修改（包括插入和删除）。
1. 对 `DataGridView.Items.Refresh()` 的调用将使用新生成的 ID 强制重载。

这适用于我们的入门示例，但对于其他方案，你可能需要其他代码。 WPF 控件通过读取实体上的字段和属性来呈现 UI。 当你在用户界面 (UI) 中编辑某个值时，该值将传递给你的实体。 直接在实体上更改属性值（例如从数据库中加载它）时，WPF 不会立即反映 UI 中的更改。 必须向呈现引擎通知更改。 项目通过手动调用 `Refresh()` 来完成此操作。 自动发出此通知的简单方法是实现 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) 接口。 WPF 组件会自动检测接口并注册更改事件。 实体负责引发这些事件。

> [!TIP]
> 若要详细了解如何处理更改，请参阅：[如何实现属性更改通知](/dotnet/framework/wpf/data/how-to-implement-property-change-notification)。

## <a name="next-steps"></a>后续步骤

了解有关[配置 DbContext](xref:core/miscellaneous/configuring-dbcontext) 的详细信息。
