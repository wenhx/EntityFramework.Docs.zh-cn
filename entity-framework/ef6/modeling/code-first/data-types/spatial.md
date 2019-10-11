---
title: 空间 Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: 018f480c1f0f1e74fc9f7a8950a6880e96f1facc
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182654"
---
# <a name="spatial---code-first"></a>空间 Code First
> [!NOTE]
> **EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。

视频和分步演练演示了如何使用实体框架 Code First 映射空间类型。 它还演示了如何使用 LINQ 查询查找两个位置之间的距离。

本演练将使用 Code First 创建新数据库，但也可以使用[Code First 到现有数据库](~/ef6/modeling/code-first/workflows/existing-database.md)。

实体框架5中引入了空间类型支持。 请注意，若要使用空间类型、枚举和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。 默认情况下，Visual Studio 2012 面向 .NET 4.5。

若要使用空间数据类型，还必须使用具有空间支持的实体框架提供程序。 有关详细信息，请参阅[提供程序对空间类型的支持](~/ef6/fundamentals/providers/spatial-support.md)。

主要的空间数据类型有两种：地理和几何。 Geography 数据类型存储椭圆体的数据（例如 GPS 纬度和经度坐标）。 Geometry 数据类型表示欧氏（平面）坐标系。

## <a name="watch-the-video"></a>观看视频
此视频演示如何实体框架 Code First 映射空间类型。 它还演示了如何使用 LINQ 查询查找两个位置之间的距离。

**提供者**：Julia Kornich

**视频**：[WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)@NO__T[-1 .WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>先决条件

你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。

## <a name="set-up-the-project"></a>设置项目

1.  打开 Visual Studio 2012
2.  在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"
3.  在左窗格中，单击 " **Visual C @ no__t**"，然后选择**控制台**模板
4.  输入**SpatialCodeFirst**作为项目名称，然后单击 **"确定"**

## <a name="define-a-new-model-using-code-first"></a>使用 Code First 定义新模型

使用 Code First 开发时，通常首先编写定义概念（域）模型 .NET Framework 类。 下面的代码定义了大学类。

该大学具有 DbGeography 类型的 Location 属性。 若要使用 DbGeography 类型，必须添加对 System.web 程序集的引用，并使用语句添加 node.js。

打开 Program.cs 文件，并将以下 using 语句粘贴到文件顶部：

``` csharp
using System.Data.Spatial;
```

将以下大学类定义添加到 Program.cs 文件。

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>定义 DbContext 派生类型

除了定义实体外，还需要定义派生自 DbContext 的类，并公开 DbSet @ no__t-0TEntity @ no__t 属性。 DbSet @ no__t-0TEntity @ no__t 属性使上下文知道要包括在模型中的类型。

DbContext 派生类型的实例在运行时管理实体对象，这包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。

DbContext 和 DbSet 类型是在 EntityFramework 程序集中定义的。 我们将使用 EntityFramework NuGet 包添加对此 DLL 的引用。

1.  在解决方案资源管理器中，右键单击项目名称。
2.  选择 "**管理 NuGet 包 ...** "
3.  在 "管理 NuGet 包" 对话框中，选择 "**联机**" 选项卡，然后选择 " **EntityFramework** " 包。
4.  单击 "**安装**"

请注意，除 EntityFramework 程序集之外，还添加了对 System.componentmodel. DataAnnotations 程序集的引用。

在 Program.cs 文件的顶部，添加以下 using 语句：

``` csharp
using System.Data.Entity;
```

在 Program.cs 中，添加上下文定义。 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>保留和检索数据

打开 Program.cs 文件，其中定义了 Main 方法。 将以下代码添加到 Main 函数中。

该代码将两个新的大学对象添加到上下文中。 空间属性使用 DbGeography. FromText 方法进行初始化。 将 WellKnownText 表示的地理点传递给方法。 然后，该代码将保存数据。 然后，将构造并执行 LINQ 查询，该查询返回其位置与指定位置最接近的大学对象。

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

## <a name="view-the-generated-database"></a>查看生成的数据库

首次运行应用程序时，实体框架会为您创建一个数据库。 由于我们安装了 Visual Studio 2012，因此将在 LocalDB 实例上创建数据库。 默认情况下，实体框架在派生上下文的完全限定名称（在此示例中为**SpatialCodeFirst. UniversityContext**）之后对数据库进行命名。 随后将使用现有数据库的时间。  

请注意，如果在创建数据库后对模型进行了任何更改，则应使用 Code First 迁移来更新数据库架构。 有关使用迁移的示例，请参阅[Code First 到新的数据库](~/ef6/modeling/code-first/workflows/new-database.md)。

若要查看数据库和数据，请执行以下操作：

1.  在 Visual Studio 2012 主菜单中，选择**View** - @ no__t-2 **SQL Server 对象资源管理器**。
2.  如果 LocalDB 不在服务器列表中，请在**SQL Server**上单击鼠标右键按钮，然后选择 "**添加 SQL Server**使用默认**Windows 身份验证**连接到 LocalDB 实例
3.  展开 LocalDB 节点
4.  展开 "**数据库**" 文件夹，以查看新数据库并浏览到**大学**表
5.  若要查看数据，请右键单击该表，然后选择 "**查看数据**"

## <a name="summary"></a>总结

在本演练中，我们介绍了如何在实体框架 Code First 中使用空间类型。 
