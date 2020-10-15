---
title: Code First 新的数据库-EF6
description: Code First 到实体框架6中的新数据库
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 9d0082ac5226fff066d3e18c9164e2230c84b285
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065013"
---
# <a name="code-first-to-a-new-database"></a>对新数据库使用 Code First
此视频和分步演练提供了面向新数据库的 Code First 开发的简介。 此方案包括针对不存在的数据库和 Code First 将创建的数据库，或者 Code First 将向其中添加新表的空数据库。 Code First 允许使用 C \# 或 VB.Net 类定义模型。 还可以选择使用类和属性上的属性或使用 Fluent API 来执行其他配置。

## <a name="watch-the-video"></a>观看视频
此视频介绍了面向新数据库的 Code First 开发。 此方案包括针对不存在的数据库和 Code First 将创建的数据库，或者 Code First 将向其中添加新表的空数据库。 Code First 允许使用 c # 或 VB.Net 类定义模型。 还可以选择使用类和属性上的属性或使用 Fluent API 来执行其他配置。

**主讲人**：[Rowan Miller](https://romiller.com/)

**视频**： [wmv](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [.wmv](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>先决条件

需要至少安装 Visual Studio 2010 或 Visual Studio 2012 才能完成此演练。

如果你使用的是 Visual Studio 2010，你还需要安装 [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。

## <a name="1-create-the-application"></a>1. 创建应用程序

为了简单起见，我们将构建一个使用 Code First 执行数据访问的基本控制台应用程序。

-   打开 Visual Studio
-   **文件- &gt; 新建- &gt; 项目 .。。**
-   从左侧菜单和**控制台应用程序**选择**Windows**
-   输入 **CodeFirstNewDatabaseSample** 作为名称
-   选择“确定”

## <a name="2-create-the-model"></a>2. 创建模型

让我们使用类定义一个非常简单的模型。 我们只是在 Program.cs 文件中对其进行定义，但在实际应用程序中，您可以将类拆分为单独的文件，并可能是单独的项目。

在 Program.cs 中的 Program 类定义下，添加以下两个类。

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

你会注意到，我们正在创建两个导航属性， (博客) 的虚拟。 这将启用实体框架的延迟加载功能。 延迟加载是指在您尝试访问这些属性时，这些属性的内容将自动从数据库加载。

## <a name="3-create-a-context"></a>3. 创建上下文

现在可以定义一个派生上下文，它表示与数据库的会话，从而使我们能够查询和保存数据。 我们定义了从 DbContext 派生的上下文，并 &lt; &gt; 为模型中的每个类公开了类型化的 DbSet TEntity。

现在，我们开始使用实体框架中的类型，因此我们需要添加 EntityFramework NuGet 包。

-   **项目– &gt; 管理 NuGet 程序包 .。。**
    注意：如果你没有 "**管理 NuGet 包 ...** " 选项你应安装 [最新版本的 NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   选择 " **联机** " 选项卡
-   选择 **EntityFramework** 包
-   单击“安装” 

在 Program.cs 的顶部为 system.string 添加 using 语句。

``` csharp
using System.Data.Entity;
```

在 Program.cs 中的 Post 类下面添加以下派生上下文。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

下面是 Program.cs 应该包含的完整列表。

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

这就是我们开始存储和检索数据所需的全部代码。 很显然，幕后会出现很多情况，接下来我们将对此进行介绍，但首先我们来看看这一点。

## <a name="4-reading--writing-data"></a>4. 读取 & 写入数据

在 Program.cs 中实现 Main 方法，如下所示。 此代码创建一个新的上下文实例，然后使用它来插入新的博客。 然后，它使用 LINQ 查询从按标题字母顺序排序的数据库中检索所有博客。

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

你现在可以运行该应用程序并对其进行测试。

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>我的数据在哪里？

按惯例，DbContext 已为您创建了一个数据库。

-   如果本地 SQL Express 实例可用 (默认情况下安装 Visual Studio 2010) 则 Code First 已在该实例上创建数据库
-   如果 SQL Express 不可用，Code First 将尝试使用 Visual Studio 2012 默认安装的 [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) () 
-   数据库以派生上下文的完全限定名称命名，在本例中为 **CodeFirstNewDatabaseSample. "bloggingcontext"**

这些只是默认的约定，有多种方法可用于更改 Code First 使用的数据库，有关详细信息，请访问 **DbContext 如何发现模型和数据库连接** 主题。
你可以使用 Visual Studio 中的服务器资源管理器连接到此数据库

-   **视图- &gt; 服务器资源管理器**
-   右键单击 "**数据连接**"，然后选择 "**添加连接 ...** "
-   如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源

    ![选择数据源](~/ef6/media/selectdatasource.png)

-   连接到 LocalDB 或 SQL Express，具体取决于你安装的是哪个

现在，我们可以检查 Code First 创建的架构。

![架构初始](~/ef6/media/schemainitial.png)

DbContext 通过查看我们定义的 DbSet 属性来确定要包括在模型中的类。 然后，它使用默认的 Code First 约定集来确定表和列的名称、确定数据类型、查找主键等。 稍后在本演练中，我们将介绍如何覆盖这些约定。

## <a name="5-dealing-with-model-changes"></a>5. 处理模型更改

现在，可以对模型进行一些更改，当我们进行这些更改时，我们还需要更新数据库架构。 为此，我们将使用一项称为 Code First 迁移的功能，或使用较短的迁移。

迁移允许我们提供一组有序的步骤，这些步骤描述如何 (和降级) 的数据库架构。 其中的每个步骤（称为迁移）都包含一些描述要应用的更改的代码。 

第一步是为 "Bloggingcontext" 启用 Code First 迁移。

-   **工具- &gt; 库包管理器- &gt; 程序包管理器控制台**
-   在包管理器控制台中运行 Enable-Migrations 命令 
-   已将新的迁移文件夹添加到包含两个项目的项目中：
    -   **Configuration.cs** –此文件包含迁移将用于迁移 "bloggingcontext" 的设置。 对于本演练，我们不需要更改任何内容，但可以在此处指定种子数据，注册其他数据库的提供程序，更改将在其中生成迁移的命名空间等。
    -   ** &lt; timestamp &gt; \_ InitialCreate.cs** –这是你的第一次迁移，它表示已应用到数据库的更改，以使其成为包含博客和文章表的数据库的空数据库。 尽管我们允许 Code First 自动为我们创建这些表，但现在我们已选择迁移，它们已转换为迁移。 还在本地数据库中记录了已应用此迁移的 Code First。 文件名上的时间戳用于排序目的。

    现在，我们来对模型进行更改，将 Url 属性添加到博客类：

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   在 Package Manager Console 中运行 **Add-迁移 AddUrl** 命令。
    Add-Migration 命令将检查自上次迁移后发生的更改，并基架找到的任何更改进行新迁移。 我们可以为迁移指定一个名称;在此示例中，我们将调用迁移 "AddUrl"。
    基架代码是说，我们需要向 dbo 添加 Url 列，该 Url 列可以保存字符串数据。博客表。 如果需要，我们可以编辑基架代码，但在这种情况下不需要这样做。

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   在 Package Manager Console 中运行 " **更新数据库** " 命令。 此命令会将任何挂起的迁移应用到数据库。 已应用 InitialCreate 迁移，因此迁移只会应用新的 AddUrl 迁移。
    提示：若要查看正在对数据库执行的 SQL，可以在调用 Update-Database 时使用 **-Verbose** 开关。

此时，新的 Url 列将添加到数据库的 "博客" 表中：

![带有 Url 的架构](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. 数据批注

到目前为止，我们只是让 EF 使用其默认约定来发现模型，但有时我们的类不遵循约定，我们需要能够进一步进行配置。 有两个选项可供选择：我们将在本节中查看数据批注，然后在下一部分中查看 Fluent API。

-   让我们向模型中添加一个用户类

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   我们还需要向派生的上下文添加一个集

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   如果我们尝试添加迁移，会收到一个错误，指出 "*EntityType" User "未定义键。为此 EntityType 定义密钥。 "* 因为 EF 无法知道用户名应为用户的主密钥。
-   现在我们将使用数据批注，因此我们需要在 Program.cs 的顶部添加 using 语句

```csharp
using System.ComponentModel.DataAnnotations;
```

-   现在为用户名属性添加批注，以确定它是主键

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   使用 **Add-迁移 AddUser** 命令基架迁移，将这些更改应用到数据库
-   运行 " **更新-数据库** " 命令以将新迁移应用到数据库

新表现在已添加到数据库中：

![具有用户的架构](~/ef6/media/schemawithusers.png)

EF 支持的全部批注列表为：

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [有 requiredattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. 熟知 API

在上一部分中，我们介绍了如何使用数据批注来补充或替代约定检测到的内容。 配置模型的另一种方法是通过 Code First Fluent API。

大多数模型配置可以使用简单的数据批注完成。 Fluent API 是一种更高级的方法，它指定模型配置，该配置涵盖了数据批注可以执行的所有操作以及数据批注无法实现的更高级配置。 数据批注和 Fluent API 可一起使用。

若要访问 Fluent API 可以重写 DbContext 中的 OnModelCreating 方法。 假设我们要将用户名称栏存储在中的列重命名为显示 \_ 名称。

-   用以下代码替代 "Bloggingcontext" 上的 OnModelCreating 方法

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   使用 **Add-迁移 ChangeDisplayName** 命令基架迁移，将这些更改应用到数据库。
-   运行 " **更新-数据库** " 命令，以将新迁移应用到数据库。

DisplayName 列现在已重命名为显示 \_ 名称：

![重命名了显示名称的架构](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>摘要

在本演练中，我们将使用新数据库查看 Code First 开发。 我们使用类定义了一个模型，然后使用该模型来创建数据库并存储和检索数据。 创建数据库后，我们使用 Code First 迁移来更改模型，因为模型已演化。 我们还了解了如何使用数据批注和熟知的 API 配置模型。
