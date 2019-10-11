---
title: 表值函数（Tvf）-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
ms.openlocfilehash: 35684196dcd7b708a8feeb1eca3096e8d4e555ec
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182525"
---
# <a name="table-valued-functions-tvfs"></a>表值函数（Tvf）
> [!NOTE]
> **EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。

视频和分步演练演示了如何使用 Entity Framework Designer 映射表值函数（Tvf）。 它还演示了如何从 LINQ 查询调用 TVF。

Tvf 当前仅在 Database First 工作流中受支持。

实体框架版本5中引入了 TVF 支持。 请注意，若要使用表值函数、枚举和空间类型等新功能，则必须以 .NET Framework 4.5 为目标。 默认情况下，Visual Studio 2012 面向 .NET 4.5。

Tvf 非常类似于具有一个关键区别的存储过程： TVF 的结果是可组合的。 这意味着，可以在 LINQ 查询中使用 TVF 的结果，而存储过程的结果不能。

## <a name="watch-the-video"></a>观看视频

**提供者**：Julia Kornich

[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)@NO__T[-1 .WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV （ZIP）](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)

## <a name="pre-requisites"></a>先决条件

若要完成本演练，你需要：

- 安装[School 数据库](~/ef6/resources/school-database.md)。

- 具有最新版本的 Visual Studio

## <a name="set-up-the-project"></a>设置项目

1.  打开 Visual Studio
2.  在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"
3.  在左窗格中，单击 " **Visual C @ no__t**"，然后选择**控制台**模板
4.  输入**TVF**作为项目名称，然后单击 **"确定"**

## <a name="add-a-tvf-to-the-database"></a>将 TVF 添加到数据库

-   选择 **&gt; SQL Server 对象资源管理器**
-   如果 LocalDB 不在服务器列表中：右键单击**SQL Server** ，然后选择 "**添加 SQL Server**使用默认**Windows 身份验证**连接到 LocalDB 服务器
-   展开 LocalDB 节点
-   在 "数据库" 节点下，右键单击 "School" 数据库节点，然后选择 " **新建查询 ...** "
-   在 T-sql 编辑器中粘贴以下 TVF 定义

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   在 T-sql 编辑器上单击鼠标右键按钮，然后选择 " **执行**"
-   GetStudentGradesForCourse 函数将添加到 School 数据库

 

## <a name="create-a-model"></a>创建模型

1.  右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"
2.  从左侧菜单中选择 "**数据**"，然后在 "**模板**" 窗格中选择 " **ADO.NET 实体数据模型**
3.  输入**TVFModel**作为文件名，然后单击 "**添加**"
4.  在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"
5.  单击 "服务器名称" 文本框中的 " **新建连接**输入 **（localdb） \\mssqllocaldb**输入**School** For 数据库名称单击 **" 确定 "**
6.  在 "选择数据库对象" 对话框中的 " **表**@no__t" 下，选择 " **人员**"、" **StudentGrade**" 和 " **课程**"  tables
7.  从 Visual Studio 2012 开始，选择 " **存储过程" 和 "函数**@no__t" 下的 **GetStudentGradesForCourse**函数，该函数从 Visual Studio 开始，Entity Designer 允许批处理导入存储过程和函数
8.  单击 " **完成**"
9.  此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。 您在 " **选择数据库对象** dialog" 框中选择的所有对象都将添加到模型中。
10. 默认情况下，每个导入的存储过程或函数的结果形状将自动成为实体模型中的新复杂类型。 但我们想要将 GetStudentGradesForCourse 函数的结果映射到 StudentGrade 实体：右键单击设计图面，然后在 "模型浏览器" 中选择 " **模型浏览器**"，选择 " **函数导**入"，然后在 "编辑函数导入" 对话框中双击 " **GetStudentGradesForCourse** " 函数，选择 " **实体**" and 选择 **StudentGrade**

## <a name="persist-and-retrieve-data"></a>保留和检索数据

打开定义 Main 方法的文件。 将以下代码添加到 Main 函数中。

下面的代码演示如何生成使用表值函数的查询。 该查询将结果投影到一个匿名类型中，该类型包含相关课程标题和一个等级大于或等于3.5 的相关学生。

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

编译并运行该应用程序。 该程序生成以下输出：

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a>总结

在本演练中，我们介绍了如何使用 Entity Framework Designer 映射表值函数（Tvf）。 它还演示了如何从 LINQ 查询调用 TVF。
