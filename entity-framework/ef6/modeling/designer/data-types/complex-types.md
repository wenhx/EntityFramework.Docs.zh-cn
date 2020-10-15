---
title: 复杂类型-EF 设计器-EF6
description: 复杂类型-实体框架6中的 EF 设计器
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: abacbdd375c2871e6c4205e0084764029b149a40
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065038"
---
# <a name="complex-types---ef-designer"></a>复杂类型-EF 设计器
本主题演示如何将复杂类型映射到 Entity Framework Designer (EF 设计器) 以及如何查询包含复杂类型属性的实体。

下图显示了在使用 EF 设计器时使用的主窗口。

![EF 设计器](~/ef6/media/efdesigner.png)

> [!NOTE]
> 在生成概念模型时，有关未映射的实体和关联的警告可能会显示在“错误列表”中。 您可以忽略这些警告，因为在您选择从模型生成数据库后，错误将消失。

## <a name="what-is-a-complex-type"></a>什么是复杂类型

复杂类型是实体类型的非标量属性，实体类型允许在实体内组织标量属性。 与实体相似，复杂类型由标量属性或者其他复杂类型属性组成。

使用表示复杂类型的对象时，请注意以下事项：

-   复杂类型没有键，因此不能独立存在。 复杂类型只能作为实体类型或其他复杂类型的属性而存在。
-   复杂类型不能参与关联且不能包含导航属性。
-   复杂类型属性不能为 **null**。 当**InvalidOperationException **调用**DbContext**   ，并且遇到 null 复杂对象时，会发生 InvalidOperationException。 复杂对象的标量属性可以为 **null**。
-   复杂类型不能从其他复杂类型继承。
-   必须将复杂类型定义为 **类**。 
-   调用 **DetectChanges** 时，EF 检测对复杂类型对象上的成员所做的更改。 调用以下成员时，实体框架自动调用**DetectChanges** ： **DbSet**、 **DbSet** **、** **DbSet** **、DbSet**、 **DbSet、DbContext** **、DbContext** **、GetValidationErrors**、DbContext、DbChangeTracker、 **DbChangeTracker.Entries**、。

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>将实体的属性重构为新的复杂类型

如果概念模型中已经有一个实体，可能想要将某些属性重构为复杂类型属性。

在设计器图面上，选择一个或多个属性 () 实体的导航属性，然后右键单击并选择 " **重构- &gt; 移动到新的复杂类型**"。

![重构为新的复杂类型](~/ef6/media/refactor.png)

具有选定属性的新复杂类型将添加到 **模型浏览器**。 此复杂类型会被赋予一个默认名称。

新创建类型的复杂属性将替换选定属性。 所有属性映射都将保留。

![重构为新的复杂类型2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>创建新的复杂类型

你还可以创建不包含现有实体的属性的新复杂类型。

右键单击 "模型浏览器" 中的 " **复杂类型** " 文件夹，指向 " **AddNew 复杂类型 ...**"。 或者，您可以选择 "**复杂类型**" 文件夹，然后 **Insert**   在键盘上按 "Insert" 键。

![添加新的复杂类型](~/ef6/media/addnewcomplextype.png)

新复杂类型将添加到具有默认名称的文件夹。 你现在可以将属性添加到该类型。

## <a name="add-properties-to-a-complex-type"></a>向复杂类型添加属性

复杂类型的属性可以是标量类型或现有的复杂类型。 但是，复杂类型属性无法具有循环引用。 例如，复杂类型 **OnsiteCourseDetails**   不能具有复杂类型 **OnsiteCourseDetails**的属性。

可以通过下面列出的任何方式向复杂类型添加属性。

-   在模型浏览器中右键单击复杂类型，指向 " **添加**"，再指向 " **标量属性**   " 或 " **复杂属性**"，然后选择所需的属性类型。 或者，可以选择复杂类型，然后按键盘上的 " **Insert**"   键。  

    ![向复杂类型添加属性](~/ef6/media/addpropertiestocomplextype.png)

    新属性将添加到具有默认名称的复杂类型。

- 或 -

-   右键单击 **EF 设计器** 图面上的实体属性，选择 " **复制**"，然后在 " **模型浏览器** " 中右键单击复杂类型，然后选择 " **粘贴**"。

## <a name="rename-a-complex-type"></a>重命名复杂类型

重命名复杂类型时，将通过项目更新对类型的所有引用。

-   在 **模型浏览器**中，慢慢地双击复杂类型。
    名称将选定并处于编辑模式。

- 或 -

-   在 **模型浏览器** 中右键单击复杂类型，然后选择 " **重命名**"。

- 或 -

-   在模型浏览器中选择复杂类型，按 F2 键。

- 或 -

-   在 **模型浏览器** 中右键单击复杂类型，然后选择 " **属性**"。 在 " **属性**" 窗口中编辑该名称   。

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>将现有复杂类型添加到实体，并将其属性映射到表列

1.  右键单击某个实体，指向 " **添加新**项"，然后选择 " **复杂属性**"。
    具有默认名称的复杂类型属性将添加到该实体。 默认类型（从现有复杂类型中选择）将指派给该属性。
2.  将所需的类型分配给 " **属性**" 窗口中的属性   。
    在将复杂类型属性添加到实体后，必须将其属性映射到表列。
3.  右键单击设计图面或 **模型浏览器**中的某个实体类型   ，然后选择 " **表映射**"。
    表映射显示在 " **映射详细信息**"   窗口中。
4.  展开 " **映射到 &lt; 表名称 &gt; **"   节点。
    此时会显示 " **列映射**"   节点。
5.  展开 " **列映射**"   节点。
    出现一个包含表中所有列的列表。 如果列映射列在 " **值/属性**" 标题下列出的任何) ，则默认属性 (  。
6.  选择要映射的列，然后右键单击相应的 " **值/属性**"   字段。
    出现一个包含所有标量属性的下拉列表。
7.  选择适当的属性。

    ![映射复杂类型](~/ef6/media/mapcomplextype.png)

8.  对每一个表列重复步骤 6 和 7。

>[!NOTE]
> 若要删除列映射，请选择要映射的列，然后单击 " **值/属性**"   字段。 然后，从下拉列表中选择 " **删除** "。

## <a name="map-a-function-import-to-a-complex-type"></a>将函数导入映射到复杂类型

函数导入基于存储过程。 若要将函数导入映射到复杂类型，相应存储过程返回的列必须在数量上与复杂类型的属性匹配并且必须具有与属性类型兼容的存储类型。

-   双击要映射到复杂类型的已导入函数。

    ![函数导入](~/ef6/media/functionimports.png)

-   填入新函数导入的设置，如下所示：
    -   在 " **存储过程名称**" 字段中指定要为其创建函数导入的存储过程   。 此字段是一个下拉列表，其中显示存储模型中的所有存储过程。
    -   在 " **函数导入名称**" 字段中指定函数导入的名称   。
    -   选择 " **复杂**"   作为返回类型，然后通过从下拉列表中选择适当的类型来指定特定的复杂返回类型。

        ![编辑函数导入](~/ef6/media/editfunctionimport.png)

-   单击 **"确定"**。
    此时将在概念模型中创建函数导入项。

### <a name="customize-column-mapping-for-function-import"></a>自定义函数导入的列映射

-   右键单击模型浏览器中的函数导入，然后选择 " **函数导入映射**"。
    此时将出现 " **映射详细信息**"   窗口，并显示函数导入的默认映射。 箭头指示列值和属性值之间的映射。 默认情况，假设列名称与复杂类型的属性名称相同。 默认的列名称以灰色文本显示。
-   如有必要，请更改列名称以匹配由对应于函数导入的存储过程返回的列名称。

## <a name="delete-a-complex-type"></a>删除复杂类型

删除复杂类型时，从概念模型删除类型，并且将删除该类型所有实例的映射。 但是，不更新对类型的引用。 例如，如果实体具有类型为 ComplexType1 的复杂类型属性，并且在 **模型浏览器**中删除了 ComplexType1，则不会更新相应的实体属性。 模型将不会验证，因为它包含引用已删除复杂类型的实体。 可以使用实体设计器更新或删除对已删除复杂类型的引用。

-   在模型浏览器中右键单击复杂类型，然后选择 " **删除**"。

- 或 -

-   在模型浏览器中选择复杂类型，然后按键盘上的“Delete”键。

## <a name="query-for-entities-containing-properties-of-complex-type"></a>查询包含复杂类型属性的实体

下面的代码演示如何执行一个查询，该查询返回包含复杂类型属性的实体类型对象的集合。

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
