---
title: 枚举支持-EF 设计器-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
ms.openlocfilehash: 92a763b84a04d3ce7ec0853ef2a4852356cf7997
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182521"
---
# <a name="enum-support---ef-designer"></a>枚举支持-EF 设计器
> [!NOTE]
> **EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。

此视频和分步演练演示了如何将枚举类型与 Entity Framework Designer 一起使用。 它还演示了如何在 LINQ 查询中使用枚举。

本演练将使用 Model First 创建新的数据库，但 EF 设计器也可以与[Database First](~/ef6/modeling/designer/workflows/database-first.md)工作流一起用于映射到现有数据库。

实体框架5中引入了枚举支持。 若要使用枚举、空间数据类型和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。 默认情况下，Visual Studio 2012 面向 .NET 4.5。

在实体框架中，枚举可以具有以下基础类型： **Byte**、 **Int16**、 **Int32**、 **Int64**或**SByte**。

## <a name="watch-the-video"></a>观看视频
此视频演示如何将枚举类型与 Entity Framework Designer 一起使用。 它还演示了如何在 LINQ 查询中使用枚举。

**提供者**： Julia Kornich

**视频**：[WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>先决条件

你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。

## <a name="set-up-the-project"></a>设置项目

1.  打开 Visual Studio 2012
2.  在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"
3.  在左窗格中，单击 " **Visual C\#** "，然后选择**控制台**模板
4.  输入**EnumEFDesigner**作为项目名称，然后单击 **"确定"**

## <a name="create-a-new-model-using-the-ef-designer"></a>使用 EF 设计器创建新模型

1.  右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"
2.  从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**
3.  输入**EnumTestModel**作为文件名，然后单击 "**添加**"
4.  在 "实体数据模型向导" 页上，在 "选择模型内容" 对话框中选择 "**空模型**"
5.  单击 "**完成**"

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。

该向导执行下列操作：

-   生成 EnumTestModel 文件，该文件定义概念模型、存储模型和这些模型之间的映射。 设置要嵌入到输出程序集的 .edmx 文件的元数据项目处理属性，以便将生成的元数据文件嵌入到程序集中。
-   添加对以下程序集的引用： EntityFramework、System.componentmodel 和 DataAnnotations。
-   创建 EnumTestModel.tt 和 EnumTestModel.Context.tt 文件，并将它们添加到 .edmx 文件下。 这些 T4 模板文件生成代码，该代码定义 DbContext 派生类型和映射到 .edmx 模型中的实体的 POCO 类型。

## <a name="add-a-new-entity-type"></a>添加新的实体类型

1.  右键单击设计图面的空白区域，选择 "**外接&gt; 实体**"，将显示 "新建实体" 对话框。
2.  为类型名称指定**部门**并为键属性名称指定**DepartmentID** ，将类型保留为**Int32**
3.  单击“确定”
4.  右键单击该实体，然后选择 "**添加新的&gt; 标量属性**"
5.  将新属性重命名为**名称**
6.  将新属性的类型更改为**Int32** （默认情况下，新属性为字符串类型）若要更改类型，请打开属性窗口并将 type 属性更改为**Int32**
7.  添加另一个标量属性，并将其重命名为**预算**，将类型更改为**Decimal**

## <a name="add-an-enum-type"></a>添加枚举类型

1.  在 Entity Framework Designer 中，右键单击 "名称" 属性，选择 "**转换为枚举**"

    ![转换为枚举](~/ef6/media/converttoenum.png)

2.  在 "**添加枚举**" 对话框中，键入**DepartmentNames**作为枚举类型名称，将基础类型更改为**Int32**，然后将以下成员添加到类型：英语、数学和经济性

    ![添加枚举类型](~/ef6/media/addenumtype.png)

3.  按 **"确定"**
4.  保存模型并生成项目
    > [!NOTE]
    > 生成时，有关未映射实体和关联的警告可能出现在错误列表中。 你可以忽略这些警告，因为在我们选择从模型生成数据库后，错误将消失。

如果你查看属性窗口，你会注意到，Name 属性的类型已更改为**DepartmentNames** ，并且新添加的枚举类型已添加到类型列表中。

如果切换到 "模型浏览器" 窗口，您将看到该类型也已添加到 "枚举类型" 节点。

![模型浏览器](~/ef6/media/modelbrowser.png)

>[!NOTE]
> 还可以通过单击鼠标右键并选择 "**添加枚举类型**"，从此窗口添加新枚举类型。 一旦创建类型，该类型就会出现在类型列表中，并且你可以与属性关联

## <a name="generate-database-from-model"></a>从模型生成数据库

现在，我们可以生成一个基于该模型的数据库。

1.  右键单击 Entity Designer 图面上的空白区域，然后选择 "**从模型生成数据库**"
2.  "生成数据库" 向导的 "选择您的数据连接" 对话框随即出现，单击 "**新建连接**" 按钮 "指定 **（localdb）\\mssqllocaldb** **作为服务器名称，然后**单击 **" 确定 "**
3.  询问是否要创建新数据库的对话框将弹出，单击 **"是"** 。
4.  单击 "**下一步**"，"创建数据库向导" 生成用于创建数据库的数据定义语言（ddl）。在 "摘要和设置" 对话框中显示该 ddl 不包含映射到枚举类型的表的定义
5.  单击 "**完成**"，单击 "完成" 不执行 DDL 脚本。
6.  "创建数据库向导" 执行以下操作：在 T-sql 编辑器中打开**EnumTest** ，并生成 edmx 文件的存储架构和映射部分，并将连接字符串信息添加到 app.config 文件
7.  在 T-sql 编辑器中单击鼠标右键，然后选择 "**执行**连接到服务器" 对话框，输入步骤2中的连接信息，然后单击 "**连接**"
8.  若要查看生成的架构，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"

## <a name="persist-and-retrieve-data"></a>保留和检索数据

打开 Program.cs 文件，其中定义了 Main 方法。 将以下代码添加到 Main 函数中。 该代码将新的部门对象添加到上下文中。 然后，它会保存数据。 此代码还执行 LINQ 查询，该查询返回名称为 DepartmentNames 的部门。

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

编译并运行该应用程序。 该程序生成以下输出：

```console
DepartmentID: 1 Name: English
```

若要查看数据库中的数据，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"。 然后，单击表上的鼠标右键，然后选择 "**查看数据**"。

## <a name="summary"></a>摘要

在本演练中，我们介绍了如何使用 Entity Framework Designer 映射枚举类型以及如何在代码中使用枚举。 
