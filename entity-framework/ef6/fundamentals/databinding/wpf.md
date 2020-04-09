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
> **本文档仅在 .NET 框架上的 WPF 上有效**
>
> 本文档介绍 .NET 框架上 WPF 的数据绑定。 对于新的 .NET 核心项目，我们建议您使用[EF Core](/ef/core)而不是实体框架 6。 EF Core 中的数据绑定文档在["问题#778"](https://github.com/dotnet/EntityFramework.Docs/issues/778)中跟踪。

# <a name="databinding-with-wpf"></a>使用 WPF 进行数据绑定
此分步演练演示如何以"主详细信息"形式将 POCO 类型绑定到 WPF 控件。 应用程序使用实体框架 API 使用数据库中的数据填充对象、跟踪更改并将数据保存到数据库中。

模型定义了参与一对多关系的两种类型：**类别**（主体\\主控形状）**和产品**（相关\\详细信息）。 然后，Visual Studio 工具用于将模型中定义的类型绑定到 WPF 控件。 WPF 数据绑定框架支持相关对象之间的导航：选择主视图中的行会导致详细视图使用相应的子数据进行更新。

本演练中的屏幕截图和代码列表取自 Visual Studio 2013，但您可以使用 Visual Studio 2012 或 Visual Studio 2010 完成本演练。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>使用"对象"选项创建 WPF 数据源

对于以前版本的实体框架，我们曾建议在基于使用 EF 设计器创建的模型创建新数据源时使用**数据库**选项。 这是因为设计器将生成从 ObjectContext 派生的上下文和派生自实体对象的实体类。 使用"数据库"选项将帮助您编写用于与此 API 曲面交互的最佳代码。

2012 年 Visual Studio 的 EF 设计人员和 2013 年可视化工作室生成源自 DbContext 的上下文以及简单的 POCO 实体类。 使用 Visual Studio 2010，我们建议交换到代码生成模板，该模板使用 DbContext，如本演练后面的所述。

使用 DbContext API 曲面时，应在创建新数据源时使用**Object**选项，如本演练所示。

如果需要，您可以为使用 EF 设计器创建的模型[还原到基于 ObjectContext 的代码生成](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。

## <a name="pre-requisites"></a>先决条件

您需要安装 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成本演练。

如果您使用的是 Visual Studio 2010，则还必须安装 NuGet。 有关详细信息，请参阅安装[NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。  

## <a name="create-the-application"></a>创建应用程序

-   打开 Visual Studio
-   **文件&gt;-&gt;新建 - 项目...**
-   在左侧窗格中选择 **Windows，** 在右侧窗格中选择 **WPF 应用程序**
-   输入 **WPF 与 EFSample** 作为名称
-   选择 **"确定"**

## <a name="install-the-entity-framework-nuget-package"></a>安装实体框架 NuGet 包

-   在解决方案资源管理器中，右键单击**WinForms 与 EFSample**项目
-   选择 **"管理 NuGet 包..."...**
-   在"管理 NuGet 包"对话框中，选择 **"联机**"选项卡并选择 **"实体框架**"包
-   单击"**安装"**  
    >[!NOTE]
    > 除了实体框架程序集外，还添加了对 System.组件模型.Data注释的引用。 如果项目具有对 System.Data.Entity 的引用，则在安装实体框架包时将删除该项目。 系统.Data.实体程序集不再用于实体框架 6 应用程序。

## <a name="define-a-model"></a>定义模型

在本演练中，您可以选择使用代码优先或 EF 设计器实现模型。 完成以下两节之一。

### <a name="option-1-define-a-model-using-code-first"></a>选项 1：首先使用代码定义模型

本节演示如何使用 Code First 创建模型及其关联的数据库。 如果您希望使用数据库优先使用 EF 设计器从数据库反向工程模型，请跳到下一部分（**选项 2：使用数据库优先定义模型）**

使用代码优先开发时，通常先编写定义概念（域）模型的 .NET 框架类。

-   向 **WPFEFSample**添加新类：
    -   右键单击项目名称
    -   选择 **"添加**"，然后**选择"新建项目**"
    -   选择 **"类"** 并输入类名称 **的产品** 
-   将 **产品** 类定义替换为以下代码：

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

**产品**类上的 **"产品"** 类和 **"类别"** 属性是导航属性。 **Products** 在实体框架中，导航属性提供了一种导航两种实体类型之间的关系的方法。

除了定义实体之外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt;属性的类。 DbSet&lt;TEntity&gt;属性让上下文知道要在模型中包括哪些类型。

DbContext 派生类型的实例在运行时管理实体对象，其中包括将对象与数据库中的数据填充、更改跟踪和将数据保存到数据库。

-   向具有以下定义的项目添加新**的 ProductContext**类：

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

编译该项目。

### <a name="option-2-define-a-model-using-database-first"></a>选项 2：首先使用数据库定义模型

本节演示如何使用数据库 First 使用 EF 设计器从数据库反向工程模型。 如果完成了上一节（**选项 1：使用代码优先定义模型），** 则跳过此部分并直接转到 **"延迟加载**"部分。

#### <a name="create-an-existing-database"></a>创建现有数据库

通常，当您将现有数据库作为目标时，该数据库将创建，但本演练需要创建要访问的数据库。

与 Visual Studio 一起安装的数据库服务器因已安装的 Visual Studio 版本而异：

-   如果您使用的是 Visual Studio 2010，您将创建一个 SQL Express 数据库。
-   如果您使用的是 Visual Studio 2012，则您将创建一个[本地数据库](https://msdn.microsoft.com/library/hh510202.aspx)数据库。

让我们继续生成数据库。

-   **视图&gt;- 服务器资源管理器**
-   右键单击**数据连接&gt;- 添加连接...**
-   如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源

    ![更改数据源](~/ef6/media/changedatasource.png)

-   连接到本地数据库或 SQL Express，具体取决于已安装的哪个，然后输入 **"产品**"作为数据库名称

    ![添加连接本地数据库](~/ef6/media/addconnectionlocaldb.png)

    ![添加连接快讯](~/ef6/media/addconnectionexpress.png)

-   选择 **"确定"，** 系统将询问您是否要创建新数据库，请选择"**是**"

    ![创建数据库](~/ef6/media/createdatabase.png)

-   新数据库现在将显示在服务器资源管理器中，右键单击它并选择 **"新查询"**
-   将以下 SQL 复制到新查询中，然后右键单击查询并选择 **"执行"**

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

#### <a name="reverse-engineer-model"></a>反向工程模型

我们将利用实体框架设计器（作为 Visual Studio 的一部分）创建我们的模型。

-   **项目&gt;- 添加新项目...**
-   从左侧菜单中选择**数据**，然后**ADO.NET实体数据模型**
-   输入**产品模型**作为名称，然后单击 **"确定"**
-   这将启动**实体数据模型向导**
-   选择 **"从数据库生成**"，然后单击 **"下一步"**

    ![选择模型内容](~/ef6/media/choosemodelcontents.png)

-   选择与在第一部分中创建的数据库的连接，输入**ProductContext**作为连接字符串的名称，然后单击"**下一步"**

    ![选择您的连接](~/ef6/media/chooseyourconnection.png)

-   单击"表格"旁边的复选框导入所有表，然后单击"完成"

    ![选择对象](~/ef6/media/chooseyourobjects.png)

反向工程过程完成后，新模型将添加到项目中，并打开，供您在实体框架设计器中查看。 App.config 文件也已添加到项目中，并包含数据库的连接详细信息。

#### <a name="additional-steps-in-visual-studio-2010"></a>视觉工作室 2010 中的其他步骤

如果您在 Visual Studio 2010 中工作，则需要更新 EF 设计器才能使用 EF6 代码生成。

-   右键单击 EF 设计器中模型的空白点，然后选择 **"添加代码生成项..."**
-   从左侧菜单**中选择"联机模板"** 并搜索**DbContext**
-   选择**EF 6.x DbContext\#生成器为 C ，** 输入**产品模型**作为名称，然后单击"添加"

#### <a name="updating-code-generation-for-data-binding"></a>更新数据绑定的代码生成

EF 使用 T4 模板从模型生成代码。 视觉工作室附带或从 Visual Studio 库下载的模板用于一般用途。 这意味着从这些模板生成的实体具有简单的 ICollection&lt;T&gt;属性。 但是，使用 WPF 执行数据绑定时，最好将**可观察集合**用于集合属性，以便 WPF 可以跟踪对集合所做的更改。 为此，我们将修改模板以使用可观察集合。

-   打开**解决方案资源管理器**并查找**产品模型.edmx**文件
-   查找将嵌套在 ProductModel.edmx 文件下的**ProductModel.tt**文件

    ![WPF 产品模型模板](~/ef6/media/wpfproductmodeltemplate.png)

-   双击ProductModel.tt文件以在可视化工作室编辑器中打开该文件
-   查找 **"ICollection"** 的两个事件并将其替换为"**可观察集合**"。 这些位于大约 296 和 484 线。
-   查找"**哈希集**"的第一次出现与"**可观察集合"替换**。 此事件大约位于第 50 行。 **不要**替换代码后面找到的第二个哈希集。
-   查找"**系统.集合.通用**"的唯一匹配项并将其替换为"**系统.集合.objectModel"。** 这大约位于第 424 行。
-   保存ProductModel.tt文件。 这应会导致重新生成实体的代码。 如果代码未自动重新生成，则右键单击ProductModel.tt并选择"运行自定义工具"。

如果您现在打开Category.cs文件（嵌套在ProductModel.tt下），那么您应该看到产品集合具有 **"可观察&lt;集合产品&gt;**"的类型。

编译该项目。

## <a name="lazy-loading"></a>延迟加载

**产品**类上的 **"产品"** 类和 **"类别"** 属性是导航属性。 **Products** 在实体框架中，导航属性提供了一种导航两种实体类型之间的关系的方法。

EF 为您提供了在首次访问导航属性时自动从数据库加载相关实体的选项。 使用这种类型的加载（称为延迟加载），请注意，如果内容尚未在上下文中，则第一次访问每个导航属性时，将对数据库执行单独的查询。

使用 POCO 实体类型时，EF 通过在运行时创建派生代理类型的实例，然后在类中重写虚拟属性来添加加载挂钩，从而实现延迟加载。 要获取相关对象的延迟加载，必须声明导航属性 getter 为**公共**和**虚拟**（在 Visual Basic 中**可重写**），并且不得**密封**类（在 Visual Basic 中**不可重写**）。 使用数据库时，第一个导航属性会自动成为虚拟属性，以启用延迟加载。 在"代码第一"部分中，我们选择出于同样的原因使导航属性成为虚拟

## <a name="bind-object-to-controls"></a>将对象绑定到控件

将模型中定义的类添加为此 WPF 应用程序的数据源。

-   双击解决方案资源管理器中的**MainWindow.xaml**以打开主窗体
-   从主菜单中，选择 **"项目&gt;- 添加新数据源...**
    （在 Visual Studio 2010 中，您需要选择**数据 -&gt;添加新数据源...**
-   在"选择数据源类型窗口"中，选择**对象**并单击 **"下一步**"
-   在"选择数据对象"对话框中，展开**WPF 与 EFSample** 两次并选择 **"类别"**  
    *无需选择**产品**数据源，因为我们将通过 **"产品****"在类别**数据源上的属性获取它*  

    ![选择数据对象](~/ef6/media/selectdataobjects.png)

-   单击 **“完成”**。
-   *如果"数据源"窗口未显示，则选择 **"查看 -&gt;其他&gt;Windows- 数据源**"，* 在 MainWindow.xaml 窗口旁边打开数据源。
-   按引脚图标，以便数据源窗口不会自动隐藏。 如果窗口已可见，则可能需要点击刷新按钮。

    ![“数据源”](~/ef6/media/datasources.png)

-   选择 **"类别"** 数据源并将其拖动到窗体上。

当我们拖动此源时发生了以下情况：

-   **类别ViewSource**资源和**类别数据网格**控件已添加到 XAML 
-   父网格元素上的 DataContext 属性设置为"[静态资源类别**ViewSource** ]"。**类别 ViewSource**资源用作外部\\父网格元素的绑定源。 然后，内部网格元素从父网格继承 DataContext 值（类别 DataGrid 的 ItemsSource 属性设置为"{绑定}"）

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

## <a name="adding-a-details-grid"></a>添加详细信息网格

现在，我们有一个网格来显示类别，让我们添加一个详细信息网格来显示关联的产品。

-   从 **"类别"** 数据源下选择 **"产品**"属性，并将其拖动到窗体上。
    -   **类别 ProductsViewSource**资源和**产品数据网格**将添加到 XAML
    -   此资源的绑定路径设置为产品
    -   WPF 数据绑定框架可确保产品**DataGrid**中仅显示与所选类别相关的产品
-   从"工具箱"中，将**按钮**拖动到窗体。 将 **"名称"** 属性**设置为按钮"保存**"，将 **"内容"** 属性设置为 **"保存**"。

窗体应如下所示：

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>添加处理数据交互的代码

是时候将一些事件处理程序添加到主窗口了。

-   在 XAML 窗口中，单击**&lt;窗口**元素，这将选择主窗口
-   在 **"属性"** 窗口中选择右上角**的事件**，然后双击 **"已加载**"标签右侧的文本框

    ![主窗口属性](~/ef6/media/mainwindowproperties.png)

-   此外，通过双击设计器中的"**保存**"按钮，添加 **"保存**"按钮的 Click 事件。 

这带来了表单背后的代码，我们现在将编辑代码以使用 ProductContext 执行数据访问。 更新主窗口的代码，如下所示。

该代码声明**产品上下文**的长期运行实例。 **ProductContext**对象用于查询数据并将其保存到数据库中。 然后，从重写的**On关闭**方法调用**ProductContext**实例上的**Dispose（）。**代码注释提供有关代码功能的详细信息。

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

## <a name="test-the-wpf-application"></a>测试 WPF 应用程序

-   编译并运行该应用程序。 如果您先使用代码，您将看到为您创建了**WPFwithEFSample.ProductContext**数据库。
-   在顶部网格中输入类别名称，在底部网格中输入产品名称*不要在 ID 列中输入任何内容，因为主键由数据库生成*

    ![包含新类别和产品的主窗口](~/ef6/media/screen1.png)

-   按 **"保存"** 按钮将数据保存到数据库

调用 DbContext 的**保存更改 （）** 后，使用数据库生成的值填充 ID。 因为我们在**保存更改（）** 后调用**Refresh（）** **DataGrid**控件也使用新值进行更新。

![已填充了已填充了"已填充的"主窗口"](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>其他资源

要了解有关使用 WPF 绑定到集合的数据的详细信息，请参阅 WPF 文档中[的本主题](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)。  
