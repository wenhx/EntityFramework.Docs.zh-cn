---
title: Code First 现有数据库-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
ms.openlocfilehash: 61980bbd1f236f496a9d4fd92aa52264f1454615
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182622"
---
# <a name="code-first-to-an-existing-database"></a>Code First 到现有数据库
此视频和分步演练提供了面向现有数据库 Code First 开发的简介。 Code First 允许使用 C @ no__t 或 VB.Net 类定义模型。 另外，还可以使用类和属性上的属性或使用 Fluent API 来执行其他配置。

## <a name="watch-the-video"></a>观看视频
此视频[现在适用于第9频道](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-)。

## <a name="pre-requisites"></a>先决条件

你将需要安装**Visual Studio 2012**或**Visual Studio 2013**才能完成此演练。

还需要安装**适用于 Visual Studio 的 Entity Framework Tools** **6.1** （或更高版本）。 有关安装最新版本的 Entity Framework Tools 的信息，请参阅[获取实体框架](~/ef6/fundamentals/install.md)。

## <a name="1-create-an-existing-database"></a>1.创建现有数据库

通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。

接下来，生成数据库。

-   打开 Visual Studio
-   **视图-&gt; 服务器资源管理器**
-   右键单击 "**数据连接-&gt; 添加连接 ...** "
-   如果尚未从**服务器资源管理器**连接到数据库，则需要选择**Microsoft SQL Server**作为数据源

    ![选择数据源](~/ef6/media/selectdatasource.png)

-   连接到 LocalDB 实例，并输入**博客**作为数据库名称

    ![LocalDB 连接](~/ef6/media/localdbconnection.png)

-   选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**

    !["创建数据库" 对话框](~/ef6/media/createdatabasedialog.png)

-   新数据库现在将出现在服务器资源管理器中，右键单击该数据库并选择 "**新建查询**"
-   将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2.创建应用程序

为简单起见，我们将构建一个使用 Code First 执行数据访问的基本控制台应用程序：

-   打开 Visual Studio
-   **文件-&gt; &gt; 项目 。**
-   从左侧菜单和**控制台应用程序**选择**Windows**
-   输入**CodeFirstExistingDatabaseSample**作为名称
-   选择“确定”

 

## <a name="3-reverse-engineer-model"></a>3.反向工程模型

我们将使用 Visual Studio Entity Framework Tools，帮助我们生成一些用于映射到数据库的初始代码。 这些工具只是生成代码，你也可以根据需要手动键入代码。

-   **项目-@no__t "添加新项 ..."**
-   从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**
-   输入 **"bloggingcontext"** 作为名称，然后单击 **"确定"**
-   这将启动**实体数据模型向导**
-   选择 "**从数据库 Code First** "，然后单击 "**下一步**"

    ![向导 One CFE](~/ef6/media/wizardonecfe.png)

-   选择与在第一部分中创建的数据库的连接，然后单击 "**下一步**"

    ![向导两个 CFE](~/ef6/media/wizardtwocfe.png)

-   单击 "**表**" 旁边的复选框以导入所有表，然后单击 "**完成**"

    ![向导三个 CFE](~/ef6/media/wizardthreecfe.png)

反向工程过程完成后，会向项目中添加大量项目，让我们看看添加的内容。

### <a name="configuration-file"></a>配置文件

已将 App.config 文件添加到项目中，此文件包含现有数据库的连接字符串。

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

@no__t 0You'll 还会注意到配置文件中的其他一些设置，这些是默认的 EF 设置，告诉 Code First 在何处创建数据库。由于我们要映射到现有数据库，因此在应用程序中将忽略这些设置。 *

### <a name="derived-context"></a>派生上下文

已将 **"bloggingcontext"** 类添加到项目。 上下文表示与数据库的会话，从而使我们能够查询并保存数据。
上下文为模型中的每个类型都公开了**DbSet @ no__t-1TEntity @ no__t** 。 你还会注意到，默认构造函数使用**名称 =** 语法调用基构造函数。 这会告知 Code First 应从配置文件加载要用于此上下文的连接字符串。

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

当使用配置文件中的连接字符串时，@no__t 0You 应始终使用**name =** 语法。这可以确保在连接字符串不存在时，实体框架将引发，而不是按约定创建新数据库。 *

### <a name="model-classes"></a>模型类

最后，还将**博客**和**Post**类添加到了项目中。 这些是构成模型的域类。 你将看到应用于类的数据批注，用于指定 Code First 约定不与现有数据库的结构一致的配置。 例如，你将在**Blog.Name**和**Blog**上看到**StringLength**批注，因为它们在数据库中的最大长度为**200** （Code First 默认值是使用数据库提供程序支持的多长度）SQL Server）中的**nvarchar （max）** 。

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4.读取 & 写入数据

现在，我们有了一个模型，可以使用它来访问某些数据了。 在**Program.cs**中实现**Main**方法，如下所示。 此代码创建一个新的上下文实例，然后使用它来插入新的**博客**。 然后，它使用 LINQ 查询从按**标题**字母顺序排序的数据库中检索所有**博客**。

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
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>如果数据库发生了更改，该怎么办？

"Code First 到数据库" 向导旨在生成一组可以进行调整和修改的起始点。 如果数据库架构发生更改，则可以手动编辑类，也可以执行其他反向工程来覆盖类。

## <a name="using-code-first-migrations-to-an-existing-database"></a>使用现有数据库的 Code First 迁移

如果要对现有数据库使用 Code First 迁移，请参阅[Code First 迁移到现有数据库](~/ef6/modeling/code-first/migrations/existing-database.md)。

## <a name="summary"></a>总结

在本演练中，我们使用现有数据库查看 Code First 开发。 我们使用了 Visual Studio Entity Framework Tools 来反向工程映射到数据库并可用于存储和检索数据的一组类。
