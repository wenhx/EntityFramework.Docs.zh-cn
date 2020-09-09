---
title: 通过 WinForms 进行数据绑定-EF6
description: 实体框架6中的 WinForms 的数据绑定
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: 934ea2ae488a7df128746a0607037d85e9b23771
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616344"
---
# <a name="databinding-with-winforms"></a>使用 WinForms 进行数据绑定
此分步演练演示了如何将 POCO 类型绑定到 Window 窗体 (WinForms) 控件。 应用程序使用实体框架使用数据库中的数据填充对象、跟踪更改并将数据保存到数据库。

该模型定义了两种参与一对多关系的类型：类别 (主体 \\ 主) 和产品 (依赖 \\ 详细信息) 。 然后，使用 Visual Studio 工具将模型中定义的类型绑定到 WinForms 控件。 WinForms 数据绑定框架允许在相关对象之间导航：在主视图中选择行后，详细信息视图将更新为相应的子数据。

本演练中的屏幕截图和代码清单取自 Visual Studio 2013，但你可以通过 Visual Studio 2012 或 Visual Studio 2010 完成此演练。

## <a name="pre-requisites"></a>先决条件

需要安装 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 才能完成此演练。

如果你使用的是 Visual Studio 2010，则还必须安装 NuGet。 有关详细信息，请参阅 [安装 NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)。

## <a name="create-the-application"></a>创建应用程序

-   打开 Visual Studio
-   **文件- &gt; 新建- &gt; 项目 ...。**
-   在左侧窗格中选择 " **windows** "，在右窗格中选择 " **windows FormsApplication** "
-   输入 **WinFormswithEFSample** 作为名称
-   选择“确定”

## <a name="install-the-entity-framework-nuget-package"></a>安装实体框架 NuGet 包

-   在解决方案资源管理器中，右键单击 **WinFormswithEFSample** 项目
-   选择 "**管理 NuGet 包 ...** "
-   在 "管理 NuGet 包" 对话框中，选择 " **联机** " 选项卡，然后选择 " **EntityFramework** " 包
-   单击“安装”   
    > [!NOTE]
    > 除了 EntityFramework 程序集之外，还添加了对 System.componentmodel 的引用。 如果项目具有对 EntityFramework 的引用，则在安装包时将被删除。 System.web 程序集不再用于实体框架6应用程序。

## <a name="implementing-ilistsource-for-collections"></a>为集合实现 IListSource

当使用 Windows 窗体时，集合属性必须实现 IListSource 接口以启用使用排序的双向数据绑定。 为此，我们将扩展 ObservableCollection 以添加 IListSource 功能。

-   将 **ObservableListSource** 类添加到项目：
    -   右键单击项目名称
    -   选择 "**添加 &gt; 新项**"
    -   选择 **类** ，并输入 **ObservableListSource** 作为类名
-   将默认生成的代码替换为以下代码：

*此类启用双向数据绑定和排序。类从 ObservableCollection T 派生 &lt; &gt; ，并添加 IListSource 的显式实现。实现 IListSource 的执行 getlist ( # A1 方法，以返回保持与 ObservableCollection 同步的 IBindingList 实现。对 tobindinglist 获得生成的 IBindingList 实现支持排序。对 tobindinglist 获得扩展方法是在 EntityFramework 程序集中定义的。*

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

## <a name="define-a-model"></a>定义模型

在本演练中，您可以使用 Code First 或 EF 设计器选择实现模型。 完成以下两个部分中的一个。

### <a name="option-1-define-a-model-using-code-first"></a>选项1：使用 Code First 定义模型

本部分说明如何使用 Code First 创建模型及其关联的数据库。 跳到下一节 (**选项2：使用 Database First 定义模型) ** 如果你想要使用 Database First 从使用 EF 设计器的数据库反向工程模型

使用 Code First 开发时，通常首先编写 .NET Framework 类来定义概念 (域) 模型。

-   将新 **产品** 类添加到项目
-   将默认生成的代码替换为以下代码：

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

-   将 **Category** 类添加到项目。
-   将默认生成的代码替换为以下代码：

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

除了定义实体外，还需要定义派生自**DbContext**的类，并公开**DbSet &lt; TEntity &gt; **属性。 **DbSet**属性让上下文知道要包括在模型中的类型。 **DbContext**和**DbSet**类型是在 EntityFramework 程序集中定义的。

DbContext 派生类型的实例在运行时管理实体对象，其中包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。

-   向项目中添加一个新的 **ProductContext** 类。
-   将默认生成的代码替换为以下代码：

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

编译该项目。

### <a name="option-2-define-a-model-using-database-first"></a>选项2：使用 Database First 定义模型

本部分说明如何使用 Database First 从使用 EF 设计器的数据库中对模型进行反向工程。 如果完成了上一节 (**选项1：使用 Code First 定义模型) **，则跳过此部分，直接转到 **延迟加载** 部分。

#### <a name="create-an-existing-database"></a>创建现有数据库

通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。

随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：

-   如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。
-   如果使用的是 Visual Studio 2012，则将创建一个 [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) 数据库。

接下来，生成数据库。

-   **视图- &gt; 服务器资源管理器**
-   右键单击 "**数据连接- &gt; 添加连接 ...** "
-   如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源

    ![更改数据源](~/ef6/media/changedatasource.png)

-   连接到 LocalDB 或 SQL Express，具体取决于已安装的数据，并输入 **Products** 作为数据库名称

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
-   从左侧菜单中选择 " **数据** "，然后 **ADO.NET 实体数据模型**
-   输入 **ProductModel** 作为名称，然后单击 **"确定"**
-   这将启动 **实体数据模型向导**
-   选择 "**从数据库生成**"，然后单击 "**下一步**"

    ![选择模型内容](~/ef6/media/choosemodelcontents.png)

-   选择在第一部分中创建的数据库的连接，输入 " **ProductContext** " 作为连接字符串的名称，然后单击 "**下一步**"

    ![选择连接](~/ef6/media/chooseyourconnection.png)

-   单击 "表" 旁边的复选框以导入所有表，然后单击 "完成"

    ![选择对象](~/ef6/media/chooseyourobjects.png)

反向工程过程完成后，会将新模型添加到项目中，并打开，以便在 Entity Framework Designer 中查看。 还使用数据库的连接详细信息将 App.config 文件添加到项目。

#### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步骤

如果使用的是 Visual Studio 2010，则需要更新 EF 设计器以使用 EF6 代码生成。

-   在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "
-   从左侧菜单中选择 " **联机模板** "，然后搜索 **DbContext**
-   选择 **适用于 C 的 EF DbContext 生成器 \# ，** 输入 **ProductsModel** 作为名称，然后单击 "添加"

#### <a name="updating-code-generation-for-data-binding"></a>更新数据绑定的代码生成

EF 使用 T4 模板从模型生成代码。 Visual Studio 附带的模板或从 Visual Studio 库下载的模板专用于一般用途。 这意味着从这些模板生成的实体具有简单的 ICollection &lt; T &gt; 属性。 但是，在执行数据绑定时，需要具有实现 IListSource 的集合属性。 这就是我们创建上述 ObservableListSource 类的原因，现在我们要修改模板以使用此类。

-   打开 **解决方案资源管理器** 并查找 **ProductModel** 文件
-   查找 **ProductModel.tt** 文件，该文件将嵌套在 ProductModel 文件下

    ![产品型号模板](~/ef6/media/productmodeltemplate.png)

-   双击 ProductModel.tt 文件以在 Visual Studio 编辑器中将其打开
-   查找并将 "**ICollection**" 的两个匹配项替换为 "**ObservableListSource**"。 这些代码位于大约第296行和第484行。
-   查找并将 "**HashSet**" 的第一个匹配项替换为 "**ObservableListSource**"。 此事件大约位于第50行。 **请勿** 替换稍后在代码中找到的第二个 HashSet。
-   保存 ProductModel.tt 文件。 这会导致重新生成实体的代码。 如果代码未自动重新生成，则右键单击 ProductModel.tt 并选择 "运行自定义工具"。

如果你现在打开 Category.cs 文件 (该文件嵌套在) ProductModel.tt 下，则你应该会看到 Products 集合具有 type **ObservableListSource &lt; 产品 &gt; **。

编译该项目。

## <a name="lazy-loading"></a>延迟加载

“类别”类上的“产品”属性和“产品”类上的“类别”属性是导航属性。 在实体框架中，导航属性提供了一种在两个实体类型之间导航关系的方法。

当首次访问导航属性时，EF 使你可以选择自动从数据库加载相关实体。 如果使用此类型的加载（称为“延迟加载”），请注意，首次访问每个导航属性时，将对数据库执行单独的查询（如果内容不在上下文中）。

使用 POCO 实体类型时，EF 通过在运行时创建派生代理类型的实例，然后重写类中的虚拟属性来添加加载挂钩来实现延迟加载。 若要获取相关对象的延迟加载，必须在 Visual Basic) 中将导航属性 getter 声明为**公共**和**虚拟** (可**重写**，并且不能 (**NotOverridable**中的 Visual Basic) 中**密封**类。 使用时，会自动将 Database First 导航属性设为虚拟，以启用延迟加载。 在 "Code First" 部分中，我们选择将导航属性设置为虚拟，因为同一原因

## <a name="bind-object-to-controls"></a>将对象绑定到控件

将在模型中定义的类添加为此 WinForms 应用程序的数据源。

-   从主菜单中，选择 "**项目- &gt; 添加新数据源 ...** "
     (在 Visual Studio 2010 中，需要选择 " **数据- &gt; 添加新数据源 ...** ") 
-   在 "选择数据源类型" 窗口中，选择 "**对象**"，然后单击 "**下一步**"
-   在 "选择数据对象" 对话框中，展开 **WinFormswithEFSample** 两次，然后选择 " **类别** "，因为我们将通过类别数据源上的产品属性来获取产品数据源。

    ![数据源](~/ef6/media/datasource.png)

-   单击“完成”。 
    如果 "数据源" 窗口未显示，请选择 " **查看"- &gt; 其他窗口- &gt; 数据源**
-   按固定图标，使 "数据源" 窗口不会自动隐藏。 如果窗口已显示，可能需要按 "刷新" 按钮。

    ![数据源2](~/ef6/media/datasource2.png)

-   在解决方案资源管理器中，双击 **Form1.cs** 文件以在设计器中打开主窗体。
-   选择 **类别** 数据源并将其拖到窗体上。 默认情况下，会将新的 DataGridView (**categoryDataGridView**) 和导航工具栏控件添加到设计器中。 这些控件绑定到 BindingSource (**categoryBindingSource**) 和绑定导航器 (**categoryBindingNavigator**) 组件创建。
-   编辑 **categoryDataGridView**上的列。 我们想要将 " **类别 id** " 列设置为只读。 保存数据后，数据库将生成 " **类别 id** " 属性的值。
    -   右键单击 DataGridView 控件并选择 "编辑列"。
    -   选择 "类别 Id" 列并将 "ReadOnly" 设置为 True
    -   按 "确定"
-   从类别数据源下选择 "产品"，并将其拖到窗体上。 ProductDataGridView 和为 productbindingsource 将添加到窗体中。
-   编辑 productDataGridView 上的列。 我们想要隐藏类别 Id 和类别列，并将 ProductId 设置为只读。 在保存数据后，数据库将生成 ProductId 属性的值。
    -   右键单击 DataGridView 控件，然后选择 " **编辑列 ...**"。
    -   选择 " **ProductId** " 列并将 " **ReadOnly** " 设置为 **True**。
    -   选择 " **类别 id** " 列并按 " **删除** " 按钮。 对 **Category** 列执行相同的操作。
    -   按“确定”。

    到目前为止，我们在设计器中将 DataGridView 控件与 BindingSource 组件相关联。 在下一部分中，我们将向隐藏代码中添加代码，以便将 categoryBindingSource 设置为 DbContext 当前跟踪的实体集合。 当我们从类别中拖放产品时，WinForms 会将 productsBindingSource 属性设置为 "categoryBindingSource" 和 "productsBindingSource" 属性设置为 "产品"。 由于此绑定，只会在 productDataGridView 中显示属于当前选定类别的产品。
-   通过单击鼠标右键并选择 "**启用**"，启用导航工具栏上的 "**保存**" 按钮。

    ![窗体1设计器](~/ef6/media/form1-designer.png)

-   双击按钮，为 "保存" 按钮添加事件处理程序。 这将添加事件处理程序，并将你带入窗体的隐藏代码。 将在下一节中添加 **categoryBindingNavigatorSaveItem \_ Click** 事件处理程序的代码。

## <a name="add-the-code-that-handles-data-interaction"></a>添加处理数据交互的代码

现在，我们将添加代码以使用 ProductContext 来执行数据访问。 更新主窗体窗口的代码，如下所示。

该代码声明了 ProductContext 的长时间运行的实例。 ProductContext 对象用于查询数据并将数据保存到数据库。 然后，从重写的 OnClosing 方法中调用 ProductContext 实例上的 Dispose ( # A1 方法。 代码注释提供有关代码的作用的详细信息。

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

## <a name="test-the-windows-forms-application"></a>测试 Windows 窗体应用程序

-   编译并运行应用程序，可以测试功能。

    ![保存前窗体1](~/ef6/media/form1beforesave.png)

-   保存存储生成的密钥后，会显示在屏幕上。

    ![保存后窗体1](~/ef6/media/form1aftersave.png)

-   如果使用 Code First，则还会看到为你创建 **WinFormswithEFSample. ProductContext** 数据库。

    ![服务器对象资源管理器](~/ef6/media/serverobjexplorer.png)
