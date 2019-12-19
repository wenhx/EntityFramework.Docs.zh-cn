---
title: Database First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
ms.openlocfilehash: d40cff4ddccf43a394ef4f244653372a5a89b05a
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182450"
---
# <a name="database-first"></a>Database First
此视频和分步演练提供使用实体框架 Database First 开发的简介。 Database First 允许从现有数据库对模型进行反向工程。 模型存储在 EDMX 文件（.edmx 扩展名）中，可在 Entity Framework Designer 中查看和编辑。 在应用程序中与之交互的类将自动从 EDMX 文件生成。

## <a name="watch-the-video"></a>观看视频
此视频介绍了使用实体框架进行 Database First 开发的简介。 Database First 允许从现有数据库对模型进行反向工程。 模型存储在 EDMX 文件（.edmx 扩展名）中，可在 Entity Framework Designer 中查看和编辑。 在应用程序中与之交互的类将自动从 EDMX 文件生成。

**主讲人**：[Rowan Miller](https://romiller.com/)

**视频**：[WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>先决条件

需要至少安装 Visual Studio 2010 或 Visual Studio 2012 才能完成此演练。

如果你使用的是 Visual Studio 2010，你还需要安装[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。

 

## <a name="1-create-an-existing-database"></a>1. 创建现有数据库

通常，当目标为现有数据库时，它将被创建，但在本演练中，我们需要创建一个要访问的数据库。

随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：

-   如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。
-   如果使用的是 Visual Studio 2012，则将创建一个[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)数据库。

 

接下来，生成数据库。

-   打开 Visual Studio
-   **视图-&gt; 服务器资源管理器**
-   右键单击 "**数据连接-&gt; 添加连接 ...** "
-   如果尚未从服务器资源管理器连接到数据库，则需要选择 Microsoft SQL Server 作为数据源

    ![选择数据源](~/ef6/media/selectdatasource.png)

-   连接到 LocalDB 或 SQL Express，具体取决于已安装的数据，并输入**DatabaseFirst**作为数据库名称

    ![Sql Express 连接 DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![LocalDB 连接 DF](~/ef6/media/localdbconnectiondf.png)

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
```

## <a name="2-create-the-application"></a>2. 创建应用程序

为了简单起见，我们将构建一个使用 Database First 执行数据访问的基本控制台应用程序：

-   打开 Visual Studio
-   **文件-&gt;&gt; 项目 。**
-   从左侧菜单和**控制台应用程序**选择**Windows**
-   输入**DatabaseFirstSample**作为名称
-   选择“确定”

 

## <a name="3-reverse-engineer-model"></a>3. 反向工程模型

我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。

-   **项目-&gt; "添加新项 ..."**
-   从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**
-   输入**BloggingModel**作为名称，然后单击 **"确定"**
-   这将启动**实体数据模型向导**
-   选择 "**从数据库生成**"，然后单击 "**下一步**"

    ![向导步骤1](~/ef6/media/wizardstep1.png)

-   选择在第一部分中创建的数据库的连接，输入 " **" bloggingcontext "** " 作为连接字符串的名称，然后单击 "**下一步**"

    ![向导步骤2](~/ef6/media/wizardstep2.png)

-   单击 "表" 旁边的复选框以导入所有表，然后单击 "完成"

    ![向导步骤3](~/ef6/media/wizardstep3.png)

 

反向工程过程完成后，会将新模型添加到项目中，并打开，以便在 Entity Framework Designer 中查看。 App.config 文件也已添加到您的项目中，其中包含数据库的连接详细信息。

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步骤

如果使用的是 Visual Studio 2010，升级到最新版本的实体框架需要遵循一些额外步骤。 升级很重要，因为它使你能够访问改进的 API 图面，更易于使用，并提供最新的 bug 修复。

首先，我们需要从 NuGet 获取最新版本的实体框架。

-   **项目–&gt; 管理 NuGet 程序包 ...** 
    *如果你没有 "**管理 nuget 包 ...** " 选项，则应安装[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) *
-   选择 "**联机**" 选项卡
-   选择**EntityFramework**包
-   单击 "**安装**"

接下来，我们需要交换模型，以生成使用 DbContext API 的代码，这些代码是在实体框架的更高版本中引入的。

-   在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "
-   从左侧菜单中选择 "**联机模板**"，然后搜索**DbContext**
-   选择**用于 C\#的 EF DbContext 生成器**，输入**BloggingModel**作为名称，然后单击 "**添加**"

    ![DbContext 模板](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4. 读取 & 写入数据

现在，我们有了一个模型，可以使用它来访问某些数据了。 要用于访问数据的类将根据 EDMX 文件自动生成。

*此屏幕快照来自 Visual Studio 2012，如果你使用的是 Visual Studio 2010，则 BloggingModel.tt 和 BloggingModel.Context.tt 文件将直接位于你的项目下，而不是在 EDMX 文件下嵌套。*

![生成的类 DF](~/ef6/media/generatedclassesdf.png)

 

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
 

## <a name="5-dealing-with-database-changes"></a>5. 处理数据库更改

现在，需要对数据库架构进行一些更改，我们进行这些更改时，我们还需要更新模型以反映这些更改。

第一步是对数据库架构进行一些更改。 我们要将用户表添加到该架构中。

-   在服务器资源管理器中右键单击**DatabaseFirst**数据库，然后选择 "**新建查询**"
-   将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

现在，架构已更新，现在可以用这些更改更新模型了。

-   在 EF 设计器中右键单击模型的空白点，然后选择 "从数据库更新模型 ..."，这将启动更新向导
-   在更新向导的 "添加" 选项卡上，选中 "表" 旁边的框，这表示要从架构中添加任何新表。
    *"刷新" 选项卡显示模型中的所有现有表，将在更新过程中检查这些表中的更改。删除选项卡显示已从架构中删除的所有表，并将作为更新的一部分从模型中删除。这两个选项卡上的信息是自动检测的，仅提供提供信息，无法更改任何设置。*

    ![刷新向导](~/ef6/media/refreshwizard.png)

-   在更新向导中单击 "完成"

 

模型现在已更新为包含新的用户实体，该实体映射到添加到数据库中的用户表。

![已更新模型](~/ef6/media/modelupdated.png)

## <a name="summary"></a>摘要

在本演练中，我们将讨论 Database First 开发，这允许我们基于现有数据库在 EF 设计器中创建模型。 然后，使用该模型从数据库中读取和写入一些数据。 最后，我们更新了模型以反映对数据库架构所做的更改。
