---
title: 设计器表拆分-EF6
description: 实体框架6中的设计器表拆分
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 8f6db22913fb3ac8a4254c29b5f986eb6e14f18e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066039"
---
# <a name="designer-table-splitting"></a>设计器表拆分
本演练演示如何通过使用 Entity Framework Designer (EF 设计器) 修改模型，将多个实体类型映射到单个表。

当使用延迟加载加载对象时，可能需要使用表拆分延迟加载某些属性。您可以将可能包含大量数据的属性分成单独的实体，并且仅在需要时加载。

下图显示了在使用 EF 设计器时使用的主窗口。

![EF 设计器](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>先决条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>设置项目

本演练使用 Visual Studio 2012。

-   打开 Visual Studio 2012。
-   在 **“文件”** 菜单上，指向 **“新建”** ，再单击 **“项目”** 。
-   在左窗格中，单击 "Visual C \# "，然后选择 "控制台应用程序" 模板。
-   输入 **TableSplittingSample** 作为项目名称，然后单击 **"确定"**。

## <a name="create-a-model-based-on-the-school-database"></a>基于 School 数据库创建模型

-   右键单击 "解决方案资源管理器中的项目名称，指向" **添加**"，然后单击" **新建项**"。
-   从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。
-   输入 **TableSplittingModel** 作为文件名，然后单击 " **添加**"。
-   在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步"。**
-   单击 "新建连接"。 在 "连接属性" 对话框中，输入服务器名称 (例如， ** (localdb) \\ mssqllocaldb**) ，选择身份验证方法，为数据库名称键入 **School**，然后   单击 **"确定"**。
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
-   在 "选择数据库对象" 对话框中，展开 "**表**"   节点并检查**Person**表。 这会将指定的表添加到 **School** 模型。
-   单击 " **完成**"。

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。 您在 " **选择数据库对象**" 对话框中选择的所有对象   都将添加到模型中。

## <a name="map-two-entities-to-a-single-table"></a>将两个实体映射到单个表

在本节中，您将把 **Person** 实体拆分为两个实体，然后将它们映射到一个表。

> [!NOTE]
> **Person**实体不包含任何可能包含大量数据的属性;它仅用作示例。

-   右键单击设计图面的空白区域，指向 " **添加新**项"，然后单击 " **实体**"。
    此时将显示 " **新建实体**"   对话框。
-   为 **HireInfo**   " **实体名称**" 键入 HireInfo，为**键属性**名称键入 " **PersonID** "。
-   单击 **"确定"**。
-   新的实体类型创建完毕，并且显示在设计图面上。
-   选择 Person 实体类型的 " **雇佣**日期"   属性 **Person**   ，并按**Ctrl + X**键。
-   选择**HireInfo**   实体，并按**Ctrl + V**键。
-   创建 **Person** 和 **HireInfo**之间的关联。 为此，请右键单击设计图面的空白区域，指向 " **添加新**项"，然后单击 " **关联**"。
-   此时将显示 " **添加关联**"   对话框。 默认情况下，指定 **PersonHireInfo** 名称。
-   指定多重性 **1 (** 关系两端的一个) 。
-   按“确定”。

下一步需要 " **映射详细信息**"   窗口。 如果看不到此窗口，请右键单击设计图面，然后选择 " **映射详细信息**"。

-   选择 **HireInfo**   实体类型，然后在 "映射详细信息" 窗口中单击 " ** &lt; 添加表或视图 &gt; **   " **Mapping Details**   。
-   从 " ** &lt; 添加表或视图 &gt; **字段" 下拉列表中选择 "**人员**"   。 此列表包含所选实体可以映射到的表或视图。
    默认情况下，应映射相应的属性。

    ![映射属性](~/ef6/media/mapping.png)

-   在设计图面上选择 " **PersonHireInfo** " 关联。
-   右键单击设计图面上的关联，然后选择 " **属性**"。
-   在 " **属性** " 窗口中，选择 " **引用约束** " 属性，然后单击省略号按钮。
-   从 "**主体**" 下拉列表中选择 "**人员**"。
-   按“确定”。

 

## <a name="use-the-model"></a>使用模型

-   将以下代码粘贴到 Main 方法中。

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   编译并运行该应用程序。

由于运行此应用程序，以下 T-sql 语句针对 **School** 数据库执行。 

-   执行上下文时，执行了以下 **插入** 操作。SaveChanges ( # A1，并合并 **Person** 和 **HireInfo** 实体的数据

    ![Insert 合并人员和 HireInfo 数据](~/ef6/media/insert.png)

-   执行上下文时，执行了以下**SELECT** 。FirstOrDefault ( # A1 并仅选择映射到**人员**的列

    ![选择“1”](~/ef6/media/select1.png)

-   在访问导航属性 existingPerson 的结果中执行了下面的**SELECT**语句，并只选择了映射到**HireInfo**的列

    ![选择2](~/ef6/media/select2.png)
