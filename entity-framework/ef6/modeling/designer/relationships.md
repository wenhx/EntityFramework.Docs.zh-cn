---
title: 关系-EF 设计器-EF6
description: 关系-实体框架6中的 EF 设计器
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 0211eb1916a7372874f0b7812c512e6eeba0974f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066130"
---
# <a name="relationships---ef-designer"></a>关系-EF 设计器
> [!NOTE]
> 本页提供有关使用 EF 设计器设置模型中的关系的信息。 有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅 [关系 & 导航属性](xref:ef6/fundamentals/relationships)。

关联定义模型中的实体类型之间的关系。 本主题说明如何将关联与 Entity Framework Designer (EF 设计器) 进行映射。 下图显示了在使用 EF 设计器时使用的主窗口。

![EF 设计器](~/ef6/media/efdesigner.png)

> [!NOTE]
> 在生成概念模型时，有关未映射的实体和关联的警告可能会显示在“错误列表”中。 您可以忽略这些警告，因为在您选择从模型生成数据库后，错误将消失。

## <a name="associations-overview"></a>关联概述

使用 EF 设计器设计模型时，.edmx 文件表示您的模型。 在 .edmx 文件中， **Association** 元素定义了两个实体类型之间的关系。 关联必须指定关系中涉及的实体类型和关系的每一端可能的实体类型数量（也称为重数）。 关联端的多重性的 (值可以是 1) 、零或一个 (0 .. 1) 或多个 (\*) 。 此信息是在两个子 **结束** 元素中指定的。

在运行时，如果选择在实体) 中公开外键，则可以通过导航属性或外键访问关联一端的实体类型实例 (。 公开外键后，实体之间的关系将使用 **ReferentialConstraint** 元素进行管理， (**关联** 元素) 的子元素。 建议你始终公开实体中关系的外键。

> [!NOTE]
> 在多对多 (\* ： \*) 不能将外键添加到实体。 在 \* ： \* 关系中，使用独立的对象管理关联信息。

有关 CSDL 元素 (**ReferentialConstraint**、 **Association**等 ) 的信息，请参阅 [csdl 规范](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。

## <a name="create-and-delete-associations"></a>创建和删除关联

使用 EF 设计器创建关联会更新 .edmx 文件的模型内容。 创建关联后，您必须为本主题后面讨论的关联 (创建映射) 。

> [!NOTE]
> 本部分假定已添加要在模型之间创建关联的实体。

### <a name="to-create-an-association"></a>创建关联

1.  右键单击设计图面的空白区域，指向 " **添加新**项"，然后选择 " **关联 ...**"。
2.  在 " **添加关联** " 对话框中填写关联的设置。

    ![添加关联](~/ef6/media/addassociation.png)

    > [!NOTE]
    > 您可以通过清除 **导航属性 **并 **将外键属性添加到 " &lt; 实体类型名称 &gt; 实体" **复选框，选择不向关联端的实体添加导航属性或外键属性。 如果只添加一个导航属性，则将只能在一个方向遍历关联。 如果不添加导航属性，则必须选择添加外键属性才能访问位于关联各端的实体。
    
3.  单击 **"确定"**。

### <a name="to-delete-an-association"></a>删除关联

若要删除关联，请执行以下操作之一：

-   右键单击 EF 设计器图面上的关联，然后选择 " **删除**"。

- 或 -

-   选择一个或多个关联并按 Delete 键。

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>将外键属性包含在实体中 (引用约束) 

建议你始终公开实体中关系的外键。 实体框架使用引用约束来标识某个属性充当关系的外键。

如果在创建关系时选中了 " ***将外键属性添加到 &lt; 实体类型名称 &gt; 实体*** " 复选框，则会为你添加此引用约束。

使用 EF 设计器添加或编辑引用约束时，EF 设计器会**ReferentialConstraint**   在 .EDMX 文件的 CSDL 内容中添加或修改 ReferentialConstraint 元素。

-   双击要编辑的关联。
    此时将显示 " **引用约束**"   对话框。
-   从 " **主体**"   下拉列表中，选择引用约束中的主体实体。
    实体的键属性将添加到对话框中的 **主体键**   列表。
-   从 " **依赖项**"   下拉列表中，选择引用约束中的依赖实体。
-   对于具有依赖键的每个主体键，从 " **依赖键**" 列中的下拉列表中选择相应的依赖项   。

    ![Ref 约束](~/ef6/media/refconstraint.png)

-   单击 **"确定"**。

## <a name="create-and-edit-association-mappings"></a>创建和编辑关联映射

您可以在 EF 设计器的 "**映射详细信息**" 窗口中指定关联映射到数据库的方式   。

> [!NOTE]
> 只能映射未指定引用约束的关联的详细信息。 如果指定了引用约束，则会在实体中包含一个外键属性，您可以使用该实体的映射详细信息来控制外键映射到的列。

### <a name="create-an-association-mapping"></a>创建关联映射

-   右键单击设计图面中的关联，然后选择 " **表映射**"。
    这会在 " **映射详细信息**" 窗口中显示关联映射   。
-   单击 " **添加表或视图**"。
    此时将显示一个下拉列表，其中包含存储模型中的所有表。
-   选择关联要映射到的表。
    " **映射详细信息**"   窗口显示关联的两端以及每**一端**的实体类型的键属性。
-   对于每个键属性，请单击 " **列**"   字段，然后选择属性将映射到的列。

    ![映射详细信息4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>编辑关联映射

-   右键单击设计图面中的关联，然后选择 " **表映射**"。
    这会在 " **映射详细信息**" 窗口中显示关联映射   。
-   单击 " **映射到 &lt; 表 &gt; 名称**"。
    此时将显示一个下拉列表，其中包含存储模型中的所有表。
-   选择关联要映射到的表。
    " **映射详细信息**"   窗口显示关联的两端以及每一端的实体类型的键属性。
-   对于每个键属性，请单击 " **列**"   字段，然后选择属性将映射到的列。

## <a name="edit-and-delete-navigation-properties"></a>编辑和删除导航属性

导航属性是快捷方式属性，用于在模型中关联的两端查找实体。 在创建两个实体类型之间的关联时可以创建导航属性。

#### <a name="to-edit-navigation-properties"></a>编辑导航属性

-   选择 EF 设计器图面上的导航属性。
    有关导航属性的信息将显示在 Visual Studio 的 " **属性**"   窗口中。
-   在 " **属性**" 窗口中更改属性设置   。

#### <a name="to-delete-navigation-properties"></a>删除导航属性

-   如果概念模型中的实体类型上没有公开外键，则删除导航属性可能导致仅在一个方向遍历相应的关联，或者根本不遍历关联。
-   右键单击 EF 设计器图面上的导航属性，然后选择 " **删除**"。
