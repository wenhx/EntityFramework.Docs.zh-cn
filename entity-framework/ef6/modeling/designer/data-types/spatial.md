---
title: 空间-EF 设计器-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
ms.openlocfilehash: a9c54fbc14dd02ce5d4d91449a0d5f9e72f7f0f7
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182505"
---
# <a name="spatial---ef-designer"></a>空间-EF 设计器
> [!NOTE]
> **EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。

视频和分步演练演示了如何使用 Entity Framework Designer 映射空间类型。 它还演示了如何使用 LINQ 查询查找两个位置之间的距离。

本演练将使用 Model First 创建新的数据库，但 EF 设计器也可以与[Database First](~/ef6/modeling/designer/workflows/database-first.md)工作流一起用于映射到现有数据库。

实体框架5中引入了空间类型支持。 请注意，若要使用空间类型、枚举和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。 默认情况下，Visual Studio 2012 面向 .NET 4.5。

若要使用空间数据类型，还必须使用具有空间支持的实体框架提供程序。 有关详细信息，请参阅[提供程序对空间类型的支持](~/ef6/fundamentals/providers/spatial-support.md)。

主要的空间数据类型有两种：地理和几何。 Geography 数据类型存储椭圆体的数据（例如 GPS 纬度和经度坐标）。 Geometry 数据类型表示欧氏（平面）坐标系。

## <a name="watch-the-video"></a>观看视频
此视频演示如何用 Entity Framework Designer 映射空间类型。 它还演示了如何使用 LINQ 查询查找两个位置之间的距离。

**提供者**：Julia Kornich

**视频**：[WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)@NO__T[-1 .WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>先决条件

你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。

## <a name="set-up-the-project"></a>设置项目

1.  打开 Visual Studio 2012
2.  在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"
3.  在左窗格中，单击 " **Visual C @ no__t**"，然后选择**控制台**模板
4.  输入**SpatialEFDesigner**作为项目名称，然后单击 **"确定"**

## <a name="create-a-new-model-using-the-ef-designer"></a>使用 EF 设计器创建新模型

1.  右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"
2.  从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**
3.  输入**UniversityModel**作为文件名，然后单击 "**添加**"
4.  在 "实体数据模型向导" 页上，在 "选择模型内容" 对话框中选择 "**空模型**"
5.  单击 "**完成**"

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。

该向导执行下列操作：

-   生成 EnumTestModel 文件，该文件定义概念模型、存储模型和这些模型之间的映射。 设置要嵌入到输出程序集的 .edmx 文件的元数据项目处理属性，以便将生成的元数据文件嵌入到程序集中。
-   添加对以下程序集的引用：EntityFramework、System.componentmodel、DataAnnotations 和 System.object。
-   创建 UniversityModel.tt 和 UniversityModel.Context.tt 文件，并将它们添加到 .edmx 文件下。 这些 T4 模板文件生成代码，该代码定义 DbContext 派生类型和映射到 .edmx 模型中的实体的 POCO 类型

## <a name="add-a-new-entity-type"></a>添加新的实体类型

1.  右键单击设计图面的空白区域，选择 "**外 &gt;" 实体**，此时将显示 "新建实体" 对话框。
2.  指定类型名称的**大学**，并为键属性名称指定**UniversityID** ，将类型保留为**Int32**
3.  单击“确定”
4.  右键单击该实体，然后选择 "**添加 &gt; 标量属性**"
5.  将新属性重命名为**名称**
6.  添加另一个标量属性，并将其重命名为**Location**打开属性窗口并将新属性的类型更改为**Geography**
7.  保存模型并生成项目
    > [!NOTE]
    > 生成时，有关未映射实体和关联的警告可能出现在错误列表中。 你可以忽略这些警告，因为在我们选择从模型生成数据库后，错误将消失。

## <a name="generate-database-from-model"></a>从模型生成数据库

现在，我们可以生成一个基于该模型的数据库。

1.  右键单击 Entity Designer 图面上的空白区域，然后选择 "**从模型生成数据库**"
2.  随即显示 "生成数据库" 向导的 "选择数据连接" 对话框，单击 "**新建连接**" 按钮 "指定 **（localdb） \\mssqllocaldb** " 作为数据库的服务器名称和**大学**，并单击 **"确定"**
3.  询问是否要创建新数据库的对话框将弹出，单击 **"是"** 。
4.  单击 "**下一步**"，"创建数据库向导" 生成用于创建数据库的数据定义语言（ddl）。在 "摘要和设置" 对话框中显示该 ddl 不包含映射到的表的定义枚举类型
5.  单击 "**完成**"，单击 "完成" 不执行 DDL 脚本。
6.  创建数据库向导执行以下操作：在 T-sql 编辑器中打开**UniversityModel**生成存储架构并将连接字符串信息添加到 app.config 文件中的映射部分。
7.  在 T-sql 编辑器中单击鼠标右键，然后选择 "**执行**连接到服务器" 对话框，输入步骤2中的连接信息，然后单击 "**连接**"
8.  若要查看生成的架构，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"

## <a name="persist-and-retrieve-data"></a>保留和检索数据

打开 Program.cs 文件，其中定义了 Main 方法。 将以下代码添加到 Main 函数中。

该代码将两个新的大学对象添加到上下文中。 空间属性使用 DbGeography. FromText 方法进行初始化。 将 WellKnownText 表示的地理点传递给方法。 然后，该代码将保存数据。 然后，将构造并执行 LINQ 查询，该查询返回其位置与指定位置最接近的大学对象。

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

编译并运行该应用程序。 该程序生成以下输出：

```console
The closest University to you is: School of Fine Art.
```

若要查看数据库中的数据，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"。 然后，单击表上的鼠标右键，然后选择 "**查看数据**"。

## <a name="summary"></a>总结

在本演练中，我们介绍了如何使用 Entity Framework Designer 映射空间类型，以及如何在代码中使用空间类型。 
