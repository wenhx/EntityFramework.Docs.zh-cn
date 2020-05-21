---
title: 用 WPF 进行数据绑定-EF6
author: divega
ms.date: 05/19/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: c8e9e9a0810d8192e5184abbc7df2e74e4ee7edb
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672908"
---
# <a name="databinding-with-wpf"></a>使用 WPF 进行数据绑定

> [!IMPORTANT]
> **此文档仅对 .NET Framework 上的 WPF 有效**
>
> 本文档介绍 .NET Framework 上的 WPF 的数据绑定。 对于新的 .NET Core 项目，建议使用[EF Core](/ef/core)而不是实体框架6。 EF Core 中的数据绑定的文档将在[#778 问题](https://github.com/dotnet/EntityFramework.Docs/issues/778)中进行跟踪。

此分步演练演示如何将 POCO 类型绑定到 "主/详细信息" 窗体中的 WPF 控件。 应用程序使用实体框架 Api 使用数据库中的数据填充对象、跟踪更改并将数据保存到数据库。

该模型定义了两种参与一对多关系的类型：**类别**（主体 \\ 主）和**产品**（依赖 \\ 详细信息）。 然后，使用 Visual Studio 工具将模型中定义的类型绑定到 WPF 控件。 WPF 数据绑定框架允许在相关对象之间导航：在主视图中选择行将导致详细信息视图与相应的子数据一起更新。

本演练中的屏幕截图和代码清单取自 Visual Studio 2013，但你可以通过 Visual Studio 2012 或 Visual Studio 2010 完成此演练。

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>使用 "对象" 选项创建 WPF 数据源

使用早期版本的实体框架我们在基于使用 EF 设计器创建的模型创建新数据源时，建议使用**数据库**选项。 这是因为设计器将生成派生自 EntityObject 的 ObjectContext 和实体类的上下文。 使用数据库选项有助于编写与此 API 图面交互的最佳代码。

Visual Studio 2012 和 Visual Studio 2013 的 EF 设计器生成一个从 DbContext 派生的上下文以及简单的 POCO 实体类。 在 Visual Studio 2010 中，我们建议使用 DbContext，如本演练稍后所述，换到使用的代码生成模板。

使用 DbContext API 图面时，应在创建新的数据源时使用**Object**选项，如本演练中所示。

如果需要，可以针对用 EF 设计器创建的模型，[恢复为基于 ObjectContext 的代码生成](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。

## <a name="pre-requisites"></a>先决条件

需要安装 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成此演练。

如果你使用的是 Visual Studio 2010，则还必须安装 NuGet。 有关详细信息，请参阅[安装 NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools)。  

## <a name="create-the-application"></a>创建应用程序

-   打开 Visual Studio
-   **文件- &gt; 新建- &gt; 项目 ...。**
-   在 **Windows**   左侧窗格中选择 "Windows"，然后在右窗格中选择 " **WPFApplication** "
-   输入 **WPFwithEFSample**   作为名称
-   选择 **"确定"**

## <a name="install-the-entity-framework-nuget-package"></a>安装实体框架 NuGet 包

-   在解决方案资源管理器中，右键单击**WinFormswithEFSample**项目
-   选择 "**管理 NuGet 包 ...** "
-   在 "管理 NuGet 包" 对话框中，选择 "**联机**" 选项卡，然后选择 " **EntityFramework** " 包
-   单击“安装”   
    >[!NOTE]
    > 除了 EntityFramework 程序集之外，还添加了对 System.componentmodel 的引用。 如果项目具有对 EntityFramework 的引用，则在安装包时将被删除。 System.web 程序集不再用于实体框架6应用程序。

## <a name="define-a-model"></a>定义模型

在本演练中，您可以使用 Code First 或 EF 设计器选择实现模型。 完成以下两个部分中的一个。

### <a name="option-1-define-a-model-using-code-first"></a>选项1：使用 Code First 定义模型

本部分说明如何使用 Code First 创建模型及其关联的数据库。 如果要 Database First 使用 EF 设计器从数据库中反向工程模型，请跳到下一节（**选项2：使用 Database First 定义模型）**

使用 Code First 开发时，通常首先编写定义概念（域）模型 .NET Framework 类。

-   向 WPFwithEFSample 添加新类 **：**
    -   右键单击项目名称
    -   选择 "**添加**"，然后选择 "**新建项**"
    -   选择**类**并输入 **Product**   类名的产品
-   将 **Product**   类定义替换为以下代码：

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

**Product**类上 "**类别**类" 和 "**类别**" 属性的 "**产品**" 属性是导航属性。 在实体框架中，导航属性提供了一种方法，用于在两个实体类型之间导航关系。

除了定义实体外，还需要定义派生自 DbContext 的类，并公开 DbSet &lt; TEntity &gt; 属性。 DbSet &lt; TEntity &gt; 属性使上下文知道要在模型中包括的类型。

DbContext 派生类型的实例在运行时管理实体对象，这包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。

-   使用以下定义将新的**ProductContext**类添加到项目：

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

### <a name="option-2-define-a-model-using-database-first"></a>选项2：使用 Database First 定义模型

本部分说明如何使用 Database First 从使用 EF 设计器的数据库中对模型进行反向工程。 如果已完成上一部分（**选项1：使用 Code First 定义模型）**，则跳过此部分，直接转到**延迟加载**部分。

#### <a name="create-an-existing-database"></a>创建现有数据库

通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。

随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：

-   如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。
-   如果使用的是 Visual Studio 2012，则将创建一个[LocalDB](https://msdn.microsoft.com/library/hh510202.aspx)数据库。

接下来，生成数据库。

-   **视图- &gt; 服务器资源管理器**
-   右键单击 "**数据连接- &gt; 添加连接 ...** "
-   如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源

    ![更改数据源](~/ef6/media/changedatasource.png)

-   连接到 LocalDB 或 SQL Express，具体取决于已安装的数据，并输入**Products**作为数据库名称

    ![添加连接 LocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![添加连接 Express](~/ef6/media/addconnectionexpress.png)

-   选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**

    ![创建数据库](~/ef6/media/createdatabase.png)

-   新数据库现在将出现在服务器资源管理器中，右键单击该数据库并选择 "**新建查询**"
-   将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"

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

我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。

-   **项目- &gt; 添加新项 .。。**
-   从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**
-   输入**ProductModel**作为名称，然后单击 **"确定"**
-   这将启动**实体数据模型向导**
-   选择 "**从数据库生成**"，然后单击 "**下一步**"

    ![选择模型内容](~/ef6/media/choosemodelcontents.png)

-   选择在第一部分中创建的数据库的连接，输入 " **ProductContext** " 作为连接字符串的名称，然后单击 "**下一步**"

    ![选择连接](~/ef6/media/chooseyourconnection.png)

-   单击 "表" 旁边的复选框以导入所有表，然后单击 "完成"

    ![选择对象](~/ef6/media/chooseyourobjects.png)

反向工程过程完成后，会将新模型添加到项目中，并打开，以便在 Entity Framework Designer 中查看。 App.config 文件也已添加到您的项目中，其中包含数据库的连接详细信息。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步骤

如果使用的是 Visual Studio 2010，则需要更新 EF 设计器以使用 EF6 代码生成。

-   在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "
-   从左侧菜单中选择 "**联机模板**"，然后搜索**DbContext**
-   选择**适用于 C 的 EF DbContext 生成器 \# ，** 输入**ProductsModel**作为名称，然后单击 "添加"

#### <a name="updating-code-generation-for-data-binding"></a>更新数据绑定的代码生成

EF 使用 T4 模板从模型生成代码。 Visual Studio 附带的模板或从 Visual Studio 库下载的模板专用于一般用途。 这意味着从这些模板生成的实体具有简单的 ICollection &lt; T &gt; 属性。 但是，使用 WPF 进行数据绑定时，需要使用**ObservableCollection**作为集合属性，以便 WPF 可以跟踪对集合所做的更改。 为此，我们将修改模板以使用 ObservableCollection。

-   打开**解决方案资源管理器**并查找**ProductModel**文件
-   查找**ProductModel.tt**文件，该文件将嵌套在 ProductModel 文件下

    ![WPF 产品型号模板](~/ef6/media/wpfproductmodeltemplate.png)

-   双击 ProductModel.tt 文件以在 Visual Studio 编辑器中将其打开
-   查找并将 "**ICollection**" 的两个匹配项替换为 "**ObservableCollection**"。 它们大约位于第296行和第484行。
-   查找并将 "**HashSet**" 的第一个匹配项替换为 "**ObservableCollection**"。 此事件大约位于第50行。 **请勿**替换稍后在代码中找到的第二个 HashSet。
-   查找并将 "System.collections.objectmodel.collection" 的唯一匹配项替换**为 "****System.Collections.ObjectModel**"。 此位置大致位于第424行。
-   保存 ProductModel.tt 文件。 这会导致重新生成实体的代码。 如果代码未自动重新生成，则右键单击 ProductModel.tt 并选择 "运行自定义工具"。

如果现在打开 Category.cs 文件（该文件嵌套在 ProductModel.tt 下），则应看到 Products 集合具有类型**ObservableCollection &lt; 产品 &gt; **。

编译该项目。

## <a name="lazy-loading"></a>延迟加载

**Product**类上 "**类别**类" 和 "**类别**" 属性的 "**产品**" 属性是导航属性。 在实体框架中，导航属性提供了一种方法，用于在两个实体类型之间导航关系。

当首次访问导航属性时，EF 使你可以选择自动从数据库加载相关实体。 使用这种类型的加载（称为 "延迟加载"）时，请注意，第一次访问每个导航属性时，将对数据库执行单独的查询（如果内容尚未出现在上下文中）。

使用 POCO 实体类型时，EF 通过在运行时创建派生代理类型的实例，然后重写类中的虚拟属性来添加加载挂钩来实现延迟加载。 若要获取相关对象的延迟加载，必须将导航属性 getter 声明为**公共**和**虚拟**（在 Visual Basic 中可**重写**），并且不能**密封**类（Visual Basic**NotOverridable** ）。 使用时，会自动将 Database First 导航属性设为虚拟，以启用延迟加载。 在 "Code First" 部分中，我们选择将导航属性设置为虚拟，因为同一原因

## <a name="bind-object-to-controls"></a>将对象绑定到控件

将在模型中定义的类作为此 WPF 应用程序的数据源添加。

-   在解决方案资源管理器中双击 " **mainwindow.xaml** " 以打开主窗体
-   从主菜单中，选择 "**项目- &gt; 添加新数据源 ...** "
    （在 Visual Studio 2010 中，需要选择 "**数据- &gt; 添加新数据源 ...**"）
-   在 "选择数据源" Typewindow 中，选择 "**对象**"，然后单击 "**下一步**"
-   在 "选择数据对象" 对话框中，展开**WPFwithEFSample**   两次，然后选择 "**类别**"  
    *不需要选择**产品**数据源，因为我们将通过**类别**数据源上的**产品**属性来访问它。*  

    ![选择数据对象](~/ef6/media/selectdataobjects.png)

-   单击 **“完成”**。
-   如果 "数据源" 窗口未显示，请在 "Mainwindow.xaml" 窗口旁打开 "数据源" 窗口 *，选择 "**查看"-" &gt; 其他 Windows- &gt; 数据源**"*
-   按固定图标，使 "数据源" 窗口不会自动隐藏。 如果窗口已显示，可能需要按 "刷新" 按钮。

    ![Data Sources](~/ef6/media/datasources.png)

-   选择 **类别**数据源并将其拖到窗体上。

拖动此源时，会发生以下情况：

-   **CategoryViewSource**资源和**categoryDataGrid**控件添加到了 XAML 
-   父网格元素上的 DataContext 属性已设置为 "{StaticResource **categoryViewSource** }"。**CategoryViewSource**资源用作外部 \\ 父网格元素的绑定源。 然后，内部网格元素从父网格继承 DataContext 值（categoryDataGrid 的 System.windows.controls.itemscontrol.itemssource 属性设置为 "{Binding}"）

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

现在，我们有了一个显示类别的网格，接下来要添加详细信息网格以显示关联的产品。

-   从 **类别**数据源下面选择 **Products**属性，并将其拖到窗体上。
    -   **CategoryProductsViewSource**资源和**productDataGrid**网格将添加到 XAML
    -   此资源的绑定路径已设置为 "产品"
    -   WPF 数据绑定框架可确保仅在**productDataGrid**中显示与所选类别相关的产品
-   从 "工具箱" 中，将**按钮**拖到窗体上。 将 "**名称**" 属性设置为**buttonSave** ，将 " **Content** " 属性设置为 "**保存**"。

窗体应该如下所示：

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>添加处理数据交互的代码

现在可以向主窗口添加一些事件处理程序。

-   在 XAML 窗口中，单击** &lt; window**元素，这将选择主窗口
-   在 "**属性**" 窗口中，选择右上角的 "**事件**"，然后双击**加载**的标签右侧的文本框

    ![主窗口属性](~/ef6/media/mainwindowproperties.png)

-   同时，通过双击设计器中的 "保存" 按钮，为 "**保存**" 按钮添加**Click**事件。 

这会使你转到窗体的隐藏代码，现在我们将编辑代码以使用 ProductContext 来执行数据访问。 更新 Mainwindow.xaml 的代码，如下所示。

该代码声明了**ProductContext**的长时间运行的实例。 **ProductContext**对象用于查询数据并将数据保存到数据库。 然后，从重写的**OnClosing**方法中调用**ProductContext**实例上的**Dispose （）** 。代码注释提供有关代码的作用的详细信息。

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

-   编译并运行该应用程序。 如果使用 Code First，则会看到为你创建**WPFwithEFSample. ProductContext**数据库。
-   在顶部网格中输入类别名称，底部网格中的产品名称*不要在 ID 列中输入任何内容，因为主键是由数据库生成的*

    ![新类别和产品的主窗口](~/ef6/media/screen1.png)

-   按 "**保存**" 按钮将数据保存到数据库

调用 DbContext 的**SaveChanges （）** 后，将用数据库生成的值填充 id。 因为我们在**SaveChanges （）** 后调用了**Refresh （）** ，所以**DataGrid**控件也将更新为新值。

![主窗口，其中填充了 Id](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>其他资源

若要了解有关使用 WPF 将数据绑定到集合的详细信息，请参阅 WPF 文档中的[此主题](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections)。  
