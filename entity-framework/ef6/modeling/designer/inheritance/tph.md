---
title: 设计器 TPH 继承-EF6
description: 实体框架6中的设计器 TPH 继承
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073391"
---
# <a name="designer-tph-inheritance"></a>设计器 TPH 继承
此分步演练演示如何使用 Entity Framework Designer (EF 设计器) 实现概念模型中的每个层次结构一个表 (TPH) 继承。 TPH 继承使用一个数据库表来维护继承层次结构中所有实体类型的数据。

在本演练中，我们会将 Person 表映射到三个实体类型： Person (基类型) 、学生 (派生自人员) ，以及人员派生的指导员 (。 我们将从数据库 () Database First 创建概念模型，然后使用 EF 设计器更改模型以实现 TPH 继承。

可以使用 Model First 映射到 TPH 继承，但您必须编写自己的数据库生成工作流，该工作流非常复杂。 然后，将此工作流分配到 EF 设计器中的 " **数据库生成工作流** " 属性。 更简单的替代方法是使用 Code First。

## <a name="other-inheritance-options"></a>其他继承选项

 (TPT) 的每种类型一个表是另一种类型的继承，其中，数据库中的单独表映射到参与继承的实体。 有关如何将每种类型一个表继承映射到 EF 设计器的信息，请参阅 [Ef 设计器 TPT 继承](xref:ef6/modeling/designer/inheritance/tpt)。

实体框架运行时 (TPC) 和混合继承模型支持每个具体的表的类型继承，但 EF 设计器不支持。 如果要使用 TPC 或混合继承，则有两个选项：使用 Code First 或手动编辑 EDMX 文件。 如果选择处理 EDMX 文件，"映射详细信息" 窗口将进入 "安全模式"，您将无法使用设计器来更改映射。

## <a name="prerequisites"></a>必备条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>设置项目

-   打开 Visual Studio 2012。
-   选择 **文件- &gt; 新建- &gt; 项目**
-   在左窗格中，单击 " **Visual \# C**"，然后选择 "**控制台**" 模板。
-   输入 **TPHDBFirstSample**   作为名称。
-   选择“确定”。 ****

## <a name="create-a-model"></a>创建模型

-   在解决方案资源管理器中右键单击项目名称，然后选择 " **添加- &gt; 新建项**"。
-   从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。
-   输入 **TPHModel** 作为文件名，然后单击 " **添加**"。
-   在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。
-   单击 " **新建连接**"。
    在 "连接属性" 对话框中，输入服务器名称 (例如， ** (localdb) \\ mssqllocaldb**) ，选择身份验证方法，为数据库名称键入 **School**，然后   单击 **"确定"**。
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
-   在 "选择数据库对象" 对话框中的 "表" 节点下，选择 **Person** 表。
-   单击 " **完成**"。

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。 您在 "选择数据库对象" 对话框中选择的所有对象都将添加到模型中。

这就是 " **Person** " 表在数据库中的外观。

![Person 表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>实现每个层次结构一个表继承

 **Person**表具有**鉴别**器列，该列可以具有以下两个值之一： "Student" 和 "讲师"。 根据值， **Person** 表将映射到 " **学生** " 实体或 " **讲师** " 实体。  **Person**表还具有两列 " **雇佣**日期"   和 " **EnrollmentDate**"，这两列必须是可以**为 null**的，因为用户不能同时是学生和指导员 (此演练) 。

### <a name="add-new-entities"></a>添加新实体

-   添加新实体。
    为此，请右键单击 Entity Framework Designer 设计图面的空白区域，然后选择 " **添加 &gt; 实体**"。
-   为 **Instructor**   " **实体名称**" 键入 "指导员   "，然后 **Person**   从 " **基类型**" 下拉列表中选择 "人员"。
-   单击 **"确定"**。
-   添加另一个新实体。 键入 **Student**   "Student" 作为 " **实体名称**"   ，然后从 " **Person**    **基类型**" 下拉列表中选择 "人员"。

已将两个新的实体类型添加到设计图面。 箭头指向 " **person**" 实体类型的新实体类型   ; 这表示该 **用户**   是新实体类型的基类型。

-   右键单击 Person 实体的 " **雇佣**日期"   属性 **Person**   。 选择 " **剪切** (或使用 Ctrl-X 键) 。
-   右键单击 **讲师**   实体，然后选择 " **粘贴** (或使用 Ctrl + V 键) 。
-   右键单击 " **雇佣**日期"   属性，然后选择 " **属性**"。
-   在 " **属性**"   窗口中，将 **可为 null**的   属性设置为 **false**。
-   右键单击 Person 实体的 **EnrollmentDate**   属性 **Person**   。 选择 " **剪切** (或使用 Ctrl-X 键) 。
-   右键单击 " **学生** " 实体，然后选择 " **粘贴 (或使用 Ctrl + V 键) 。**
-   选择 **EnrollmentDate**   属性并将 **可为 null**的   属性设置为 **false**。
-   选择 " **人员**"   实体类型。 在 " **属性**"   窗口中，将其 " **抽象**" 属性设置   为 " **true**"。
-   删除**Person**的**鉴别**器属性。 下一节将对其删除原因进行说明。

### <a name="map-the-entities"></a>映射实体

-   右键单击 **讲师** ，然后选择 " **表映射"。**
    在 "映射详细信息" 窗口中选择 "指导员" 实体。
-   在 "映射详细信息" 窗口中单击 " ** &lt; 添加表或视图 &gt; **   " **Mapping Details**   。
    " ** &lt; 添加表" 或 " &gt; 视图**"   字段将成为所选实体可映射到的表或视图的下拉列表。
-    **Person**   从下拉列表中选择 "人员"。
-   " **映射详细信息**"   窗口将更新为默认的列映射，并提供一个用于添加条件的选项。
-   单击 " ** &lt; 添加条件 &gt; **"。
    " ** &lt; 添加条件 &gt; **"   字段将成为可以为其设置条件的列的下拉列表。
-    **Discriminator**   从下拉列表中选择 "鉴别器"。
-   在 **Operator**   " **映射详细信息**" 窗口的 "运算符" 列中   ，从下拉列表中选择 "="。
-   在 " **值/属性** " 列中，键入 " **讲师**"。 最终结果应该如下所示：

    ![映射详细信息](~/ef6/media/mappingdetails2.png)

-   对于 " **学生**"   实体类型，请重复这些步骤，但将条件设置为 "**学生**" 值。  
    *删除**鉴别**器属性的原因是，您不能多次映射一个表列。此列将用于条件映射，因此它也不能用于属性映射。如果条件使用 **Is null**   或 **is Not null**比较，则可以使用这两种方法   。*

“每个层次结构一个表”继承实现完毕。

![最终 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>使用模型

打开 **Program.cs** 文件，其中定义了 **Main** 方法。 将以下代码粘贴到 **Main** 函数中。 此代码执行三个查询。 第一个查询返回所有 **Person** 对象。 第二个查询使用 **OfType** 方法返回 **指导员** 对象。 第三个查询使用 **OfType** 方法返回 **学生** 对象。

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
