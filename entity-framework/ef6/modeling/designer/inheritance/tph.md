---
title: 设计器 TPH 继承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
ms.openlocfilehash: 43ba34a98c3960a7a3052a00e2ed2751c2f2b121
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415231"
---
# <a name="designer-tph-inheritance"></a>设计器 TPH 继承
此分步演练演示如何使用 Entity Framework Designer （EF 设计器）在概念模型中实现每个层次结构一个表（TPH）继承。 TPH 继承使用一个数据库表来维护继承层次结构中所有实体类型的数据。

在本演练中，我们会将 Person 表映射到三个实体类型： Person （基类型）、学生（派生自人员）和讲师（派生自人员）。 我们将从数据库（Database First）创建概念模型，然后使用 EF 设计器更改模型以实现 TPH 继承。

可以使用 Model First 映射到 TPH 继承，但您必须编写自己的数据库生成工作流，该工作流非常复杂。 然后，将此工作流分配到 EF 设计器中的 "**数据库生成工作流**" 属性。 更简单的替代方法是使用 Code First。

## <a name="other-inheritance-options"></a>其他继承选项

每种类型一个表（TPT）是另一种类型的继承，其中，数据库中的单独表映射到参与继承的实体。  有关如何将每种类型一个表继承映射到 EF 设计器的信息，请参阅[Ef 设计器 TPT 继承](~/ef6/modeling/designer/inheritance/tpt.md)。

实体框架运行时支持每个具体的表类型继承（TPC）和混合继承模型，但 EF 设计器不支持。 如果要使用 TPC 或混合继承，则有两个选项：使用 Code First 或手动编辑 EDMX 文件。 如果选择处理 EDMX 文件，"映射详细信息" 窗口将进入 "安全模式"，您将无法使用设计器来更改映射。

## <a name="prerequisites"></a>先决条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>设置项目

-   打开 Visual Studio 2012。
-   选择 "**文件-&gt;"&gt; 项目**
-   在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台**" 模板。
-   输入 " **TPHDBFirstSample** " 作为名称。
-   选择“确定”。 ****

## <a name="create-a-model"></a>创建模型

-   右键单击 "解决方案资源管理器中的项目名称，然后选择"**添加-&gt; 新项**"。
-   从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。
-   输入**TPHModel**作为文件名，然后单击 "**添加**"。
-   在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。
-   单击 " **新建连接**"。
    在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择身份验证方法，为数据库名称键入 **School** ，然后单击 **"确定"** 。
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
-   在 "选择数据库对象" 对话框中的 "表" 节点下，选择 **Person**表。
-   单击 " **完成**"。

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。 您在 "选择数据库对象" 对话框中选择的所有对象都将添加到模型中。

这就是 " **Person** " 表在数据库中的外观。

![Person 表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>实现每个层次结构一个表继承

 **Person**表具有**鉴别**器列，该列可以具有以下两个值之一： "Student" 和 "讲师"。 根据值， **Person**表将映射到 "**学生**" 实体或 "**讲师**" 实体。  **Person**表还具有两列： " **雇佣**日期" 和 " **EnrollmentDate**"，这些列必须可以为**null** ，因为用户不能同时是学生和指导员（至少在本演练中没有）。

### <a name="add-new-entities"></a>添加新实体

-   添加新实体。
    为此，请右键单击 Entity Framework Designer 设计图面的空白区域，然后选择 " **&gt;" 实体**。
-   为 " **实体名称**" 键入 " **指导员** " 然后从 " **基类型**" 下拉列表中选择 " **人员** "。
-   单击 **"确定"** 。
-   添加另一个新实体。 为 " **实体名称**" 键入 " **Student** " 然后从 " **基类型**" 下拉列表中选择 " **人员** "。

已将两个新的实体类型添加到设计图面。 箭头从新的实体类型指向 实体类型的 **人员**;这表示 **用户** 是新实体类型的基类型。

-   右键单击 **人员** 实体的 " **雇佣**日期" 属性。 选择 " **剪切**" （或使用 Ctrl + X 键）。
-   右键单击 **讲师** 实体，然后选择 " **粘贴**" （或使用 Ctrl + V 键）。
-   右键单击 " **雇佣**日期" 属性，然后选择 " **属性**"。
-   在 " **属性** " 窗口中，将 " **可以为 null**的 属性设置为 **false**。
-   右键单击 **Person** 实体的 **EnrollmentDate** 属性。 选择 " **剪切**" （或使用 Ctrl + X 键）。
-   右键单击 " **学生**" 实体，然后选择 " **粘贴" （或使用 Ctrl + V 键）。**
-   选择 **EnrollmentDate** 属性，并将 **可为 null**的 属性设置为 **false**。
-   选择 " **人员**" 实体类型。 在 " **属性** " 窗口中，将其 **Abstract** 属性设置为 **true**。
-   删除**Person**的**鉴别**器属性。 下一节将对其删除原因进行说明。

### <a name="map-the-entities"></a>映射实体

-   右键单击 **讲师**，然后选择 "**表映射"。**
    在 "映射详细信息" 窗口中选择 "指导员" 实体。
-   在 " **映射详细信息**" 窗口中，单击 " **&lt;添加表或视图&gt;**  "。
     **&lt;添加表或视图&gt;**  "字段将成为所选实体可映射到的表或视图的下拉列表。
-   从下拉列表中选择 **Person** 。
-   " **映射详细信息**" "窗口使用默认列映射和一个用于添加条件的选项进行更新。
-   单击 " **&lt;&gt;添加条件**。
     **&lt;"添加条件"&gt;**  "字段将成为可以设置条件的列的下拉列表。
-   从下拉列表中选择 " **鉴别**器 。
-   在 " **映射详细信息**" 窗口的 " **运算符** " 列中，从下拉列表中选择 "="。
-   在 " **值/属性**" 列中，键入 " **讲师**"。 最终结果应该如下所示：

    ![映射详细信息](~/ef6/media/mappingdetails2.png)

-   为 " **学生** " 实体类型重复上述步骤，但将条件设置为 "**学生**" 值。  
    *删除**鉴别**器属性的原因是，您不能多次映射一个表列。此列将用于条件映射，因此它也不能用于属性映射。对于这两种情况，唯一的方法是：如果条件使用 **Is null** 或者不 **是 null** 比较。*

“每个层次结构一个表”继承实现完毕。

![最终 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>使用模型

打开**Program.cs**文件，其中定义了**Main**方法。 将以下代码粘贴到**Main**函数中。 此代码执行三个查询。 第一个查询返回所有**Person**对象。 第二个查询使用**OfType**方法返回**指导员**对象。 第三个查询使用**OfType**方法返回**学生**对象。

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
