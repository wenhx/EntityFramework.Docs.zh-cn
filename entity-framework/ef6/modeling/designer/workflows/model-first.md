---
title: Model First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
ms.openlocfilehash: 1b37805beb3d33f0b6dad2577a8abb3ea8f7b1e4
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414955"
---
# <a name="model-first"></a>Model First
此视频和分步演练提供使用实体框架 Model First 开发的简介。 Model First 允许使用 Entity Framework Designer 创建新模型，然后从该模型生成数据库架构。 模型存储在 EDMX 文件（.edmx 扩展名）中，可在 Entity Framework Designer 中查看和编辑。 在应用程序中与之交互的类将自动从 EDMX 文件生成。

## <a name="watch-the-video"></a>观看视频
此视频和分步演练提供使用实体框架 Model First 开发的简介。 Model First 允许使用 Entity Framework Designer 创建新模型，然后从该模型生成数据库架构。 模型存储在 EDMX 文件（.edmx 扩展名）中，可在 Entity Framework Designer 中查看和编辑。 在应用程序中与之交互的类将自动从 EDMX 文件生成。

**主讲人**：[Rowan Miller](https://romiller.com/)

**视频**：WMV | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>先决条件

你将需要安装 Visual Studio 2010 或 Visual Studio 2012 才能完成此演练。

如果你使用的是 Visual Studio 2010，你还需要安装[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) 。

## <a name="1-create-the-application"></a>1.创建应用程序

为了简单起见，我们将构建一个使用 Model First 执行数据访问的基本控制台应用程序：

-   打开 Visual Studio
-   **文件-&gt;&gt; 项目 。**
-   从左侧菜单和**控制台应用程序**选择**Windows**
-   输入**ModelFirstSample**作为名称
-   选择“确定”

## <a name="2-create-model"></a>2.创建模型

我们将使用在 Visual Studio 中包含的 Entity Framework Designer 来创建模型。

-   **项目-&gt; "添加新项 ..."**
-   从左侧菜单中选择 "**数据**"，然后**ADO.NET 实体数据模型**
-   输入**BloggingModel**作为名称，然后单击 **"确定"** ，这将启动实体数据模型向导
-   选择 "**空模型**"，然后单击 "**完成**"

    ![创建空模型](~/ef6/media/createemptymodel.png)

将使用空白模型打开 Entity Framework Designer。 现在，我们可以开始向模型添加实体、属性和关联。

-   右键单击设计图面，然后选择 "**属性**"
-   在属性窗口将**实体容器名称**更改为 **"bloggingcontext"** 
    *这是将为你生成的派生上下文的名称，上下文表示与数据库的会话，从而使我们能够查询和保存数据*
-   右键单击设计图面，然后选择 "**添加新的-&gt; 实体 ...** "
-   以 "实体名称" 和 " **BlogId** " 作为键名称输入**博客**，然后单击 **"确定"**

    ![添加博客实体](~/ef6/media/addblogentity.png)

-   右键单击设计图面上的新实体，然后选择 "**添加新的&gt; 标量属性**"，输入**name**作为属性的名称。
-   重复此过程以添加**Url**属性。
-   右键单击设计图面上的 " **Url** " 属性，然后选择 "**属性**"，在 "属性窗口将**可为 Null**的设置更改为**True**
    *这样就可以将博客保存到数据库，而无需向其分配 Url*
-   使用刚才了解到的技术，添加具有**PostId**键属性的**Post**实体
-   向**Post**实体添加**Title**和**Content**标量属性

现在我们有了几个实体，可以在它们之间添加关联（或关系）。

-   右键单击设计图面，然后选择 "**添加新&gt; 关联 ...** "
-   将关系点的一端作为**博客**，使其重数为**1** ，另一个终结**点使用** **多个**
    的重数，*这意味着，博客有多篇文章，张贴内容属于一个博客*
-   确保选中 "**将外键属性添加到 ' Post ' 实体**" 框，然后单击 **"确定**"

    ![添加 Association MF](~/ef6/media/addassociationmf.png)

现在，我们有了一个简单的模型，我们可以从它生成数据库并使用它来读取和写入数据。

![模型初始](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Visual Studio 2010 中的其他步骤

如果使用的是 Visual Studio 2010，升级到最新版本的实体框架需要遵循一些额外步骤。 升级很重要，因为它使你能够访问改进的 API 图面，更易于使用，并提供最新的 bug 修复。

首先，我们需要从 NuGet 获取最新版本的实体框架。

-   **项目–&gt; 管理 NuGet 程序包 ...** 
    *如果你没有 "**管理 nuget 包 ...** " 选项，则应安装[最新版本的 nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) *
-   选择 "**联机**" 选项卡
-   选择**EntityFramework**包
-   单击“安装”

接下来，我们需要交换模型，以生成使用 DbContext API 的代码，这些代码是在实体框架的更高版本中引入的。

-   在 EF 设计器中右键单击模型的空位置，然后选择 "**添加代码生成项 ...** "
-   从左侧菜单中选择 "**联机模板**"，然后搜索**DbContext**
-   选择**用于 C\#的 EF DbContext 生成器**，输入**BloggingModel**作为名称，然后单击 "**添加**"

    ![DbContext 模板](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a>3.正在生成数据库

考虑到我们的模型，实体框架可以计算一个数据库架构，该架构将允许我们使用模型存储和检索数据。

随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：

-   如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。
-   如果使用的是 Visual Studio 2012，则将创建一个[LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx)数据库。

接下来，生成数据库。

-   右键单击设计图面，然后选择 "**从模型生成数据库 ...** "
-   单击 "**新建连接 ...** " 并指定 LocalDB 或 SQL Express，具体取决于所使用的 Visual Studio 的版本，请输入**ModelFirst**作为数据库名称。

    ![LocalDB 连接 MF](~/ef6/media/localdbconnectionmf.png)

    ![Sql Express 连接 MF](~/ef6/media/sqlexpressconnectionmf.png)

-   选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**
-   选择 "**下一步**"，Entity Framework Designer 将计算用于创建数据库架构的脚本
-   显示脚本后，单击 "**完成**"，脚本将添加到项目中并打开
-   右键单击该脚本，然后选择 "**执行**"，系统将提示您指定要连接到的数据库，指定 LocalDB 或 SQL Server Express，具体取决于您使用的 Visual Studio 的版本

## <a name="4-reading--writing-data"></a>4.读取 & 写入数据

现在，我们有了一个模型，可以使用它来访问某些数据了。 要用于访问数据的类将根据 EDMX 文件自动生成。

*此屏幕快照来自 Visual Studio 2012，如果你使用的是 Visual Studio 2010，则 BloggingModel.tt 和 BloggingModel.Context.tt 文件将直接位于你的项目下，而不是在 EDMX 文件下嵌套。*

![生成的类](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5.处理模型更改

现在，可以对模型进行一些更改，当我们进行这些更改时，我们还需要更新数据库架构。

首先向模型添加新的用户实体。

-   添加新的**用户**实体名称，其中**用户名**为密钥名称，**字符串**用作密钥的属性类型

    ![添加用户实体](~/ef6/media/adduserentity.png)

-   右键单击设计图面上的 "**用户名**" 属性，然后选择 "**属性**"，在 "属性窗口将" **MaxLength** "设置更改为**50**
    *这会将用户名中存储的数据限制为50个字符*
-   向**用户**实体添加**DisplayName**标量属性

现在，我们有了一个更新的模型，我们可以更新数据库以适应新的用户实体类型。

-   右键单击设计图面，然后选择 "**从模型生成数据库 ...** "，实体框架将计算一个脚本以根据更新后的模型重新创建架构。
-   单击“完成”
-   你可能会收到有关覆盖模型的现有 DDL 脚本以及映射和存储部分的警告，请单击 **"是"** 以显示这两个警告
-   将为您打开用于创建数据库的更新的 SQL 脚本  
    *生成的脚本将删除所有现有的表，然后从头开始重新创建该架构。这可能适用于本地开发，但并不适合将更改推送到已部署的数据库。如果需要将更改发布到已部署的数据库，则需要编辑该脚本或使用架构比较工具来计算迁移脚本。*
-   右键单击该脚本，然后选择 "**执行**"，系统将提示您指定要连接到的数据库，指定 LocalDB 或 SQL Server Express，具体取决于您使用的 Visual Studio 的版本

## <a name="summary"></a>Summary

在本演练中，我们介绍 Model First 开发，这允许我们在 EF 设计器中创建模型，然后从该模型生成数据库。 然后，使用该模型从数据库中读取和写入一些数据。 最后，我们更新了模型，然后重新创建了数据库架构以匹配模型。
