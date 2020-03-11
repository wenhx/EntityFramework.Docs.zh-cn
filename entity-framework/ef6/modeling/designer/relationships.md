---
title: 关系-EF 设计器-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: d429c39dafbf183caabdc85748c188deb8dd6f66
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415075"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="f5de2-102">关系-EF 设计器</span><span class="sxs-lookup"><span data-stu-id="f5de2-102">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="f5de2-103">本页提供有关使用 EF 设计器设置模型中的关系的信息。</span><span class="sxs-lookup"><span data-stu-id="f5de2-103">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="f5de2-104">有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅[关系 & 导航属性](~/ef6/fundamentals/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="f5de2-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>

<span data-ttu-id="f5de2-105">关联定义模型中的实体类型之间的关系。</span><span class="sxs-lookup"><span data-stu-id="f5de2-105">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="f5de2-106">本主题演示如何映射与 Entity Framework Designer （EF 设计器）的关联。</span><span class="sxs-lookup"><span data-stu-id="f5de2-106">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="f5de2-107">下图显示了在使用 EF 设计器时使用的主窗口。</span><span class="sxs-lookup"><span data-stu-id="f5de2-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF 设计器](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="f5de2-109">在生成概念模型时，有关未映射的实体和关联的警告可能会显示在“错误列表”中。</span><span class="sxs-lookup"><span data-stu-id="f5de2-109">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="f5de2-110">您可以忽略这些警告，因为在您选择从模型生成数据库后，错误将消失。</span><span class="sxs-lookup"><span data-stu-id="f5de2-110">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="f5de2-111">关联概述</span><span class="sxs-lookup"><span data-stu-id="f5de2-111">Associations Overview</span></span>

<span data-ttu-id="f5de2-112">使用 EF 设计器设计模型时，.edmx 文件表示您的模型。</span><span class="sxs-lookup"><span data-stu-id="f5de2-112">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="f5de2-113">在 .edmx 文件中， **Association**元素定义了两个实体类型之间的关系。</span><span class="sxs-lookup"><span data-stu-id="f5de2-113">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="f5de2-114">关联必须指定关系中涉及的实体类型和关系的每一端可能的实体类型数量（也称为重数）。</span><span class="sxs-lookup"><span data-stu-id="f5de2-114">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="f5de2-115">关联端的多重性值可以为一（1）、零或一（0 ..1）或多（\*）。</span><span class="sxs-lookup"><span data-stu-id="f5de2-115">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="f5de2-116">此信息是在两个子**结束**元素中指定的。</span><span class="sxs-lookup"><span data-stu-id="f5de2-116">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="f5de2-117">在运行时，可以通过导航属性或外键（如果在实体中选择公开外键）来访问关联一端的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="f5de2-117">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="f5de2-118">公开外键后，将使用**ReferentialConstraint**元素（ **Association**元素的子元素）管理实体之间的关系。</span><span class="sxs-lookup"><span data-stu-id="f5de2-118">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="f5de2-119">建议你始终公开实体中关系的外键。</span><span class="sxs-lookup"><span data-stu-id="f5de2-119">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="f5de2-120">在多对多（\*：\*）中，不能向实体中添加外键。</span><span class="sxs-lookup"><span data-stu-id="f5de2-120">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="f5de2-121">在 \*：\* 关系中，使用独立对象管理关联信息。</span><span class="sxs-lookup"><span data-stu-id="f5de2-121">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="f5de2-122">有关 CSDL 元素（**ReferentialConstraint**、**关联**等）的信息，请参阅[csdl 规范](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="f5de2-122">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="f5de2-123">创建和删除关联</span><span class="sxs-lookup"><span data-stu-id="f5de2-123">Create and Delete Associations</span></span>

<span data-ttu-id="f5de2-124">使用 EF 设计器创建关联会更新 .edmx 文件的模型内容。</span><span class="sxs-lookup"><span data-stu-id="f5de2-124">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="f5de2-125">创建关联后，必须创建关联的映射（本主题后面将对此进行讨论）。</span><span class="sxs-lookup"><span data-stu-id="f5de2-125">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="f5de2-126">本部分假定已添加要在模型之间创建关联的实体。</span><span class="sxs-lookup"><span data-stu-id="f5de2-126">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="f5de2-127">创建关联</span><span class="sxs-lookup"><span data-stu-id="f5de2-127">To create an association</span></span>

1.  <span data-ttu-id="f5de2-128">右键单击设计图面的空白区域，指向 " **添加新**项"，然后选择 " **关联 ...** "。</span><span class="sxs-lookup"><span data-stu-id="f5de2-128">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="f5de2-129">在 "**添加关联**" 对话框中填写关联的设置。</span><span class="sxs-lookup"><span data-stu-id="f5de2-129">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![添加关联](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="f5de2-131">您可以通过清除 **导航属性 **并 **将外键属性添加到 &lt;实体类型名称&gt; 实体 **"复选框，选择不向关联端的实体添加导航属性或外键属性。</span><span class="sxs-lookup"><span data-stu-id="f5de2-131">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="f5de2-132">如果只添加一个导航属性，则将只能在一个方向遍历关联。</span><span class="sxs-lookup"><span data-stu-id="f5de2-132">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="f5de2-133">如果不添加导航属性，则必须选择添加外键属性才能访问位于关联各端的实体。</span><span class="sxs-lookup"><span data-stu-id="f5de2-133">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="f5de2-134">单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="f5de2-134">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="f5de2-135">删除关联</span><span class="sxs-lookup"><span data-stu-id="f5de2-135">To delete an association</span></span>

<span data-ttu-id="f5de2-136">若要删除关联，请执行以下操作之一：</span><span class="sxs-lookup"><span data-stu-id="f5de2-136">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="f5de2-137">右键单击 EF 设计器图面上的关联，然后选择 " **删除**"。</span><span class="sxs-lookup"><span data-stu-id="f5de2-137">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="f5de2-138">或 -</span><span class="sxs-lookup"><span data-stu-id="f5de2-138">OR -</span></span>

-   <span data-ttu-id="f5de2-139">选择一个或多个关联并按 Delete 键。</span><span class="sxs-lookup"><span data-stu-id="f5de2-139">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="f5de2-140">在实体中包括外键属性（引用约束）</span><span class="sxs-lookup"><span data-stu-id="f5de2-140">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="f5de2-141">建议你始终公开实体中关系的外键。</span><span class="sxs-lookup"><span data-stu-id="f5de2-141">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="f5de2-142">实体框架使用引用约束来标识某个属性充当关系的外键。</span><span class="sxs-lookup"><span data-stu-id="f5de2-142">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="f5de2-143">如果在创建关系时选中了 "***将外键属性添加到 &lt;实体类型名称&gt; 实体***" 复选框，则会为你添加此引用约束。</span><span class="sxs-lookup"><span data-stu-id="f5de2-143">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="f5de2-144">使用 EF 设计器添加或编辑引用约束时，EF 设计器会在 .edmx 文件的 CSDL 内容中添加或修改**ReferentialConstraint** 元素。</span><span class="sxs-lookup"><span data-stu-id="f5de2-144">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="f5de2-145">双击要编辑的关联。</span><span class="sxs-lookup"><span data-stu-id="f5de2-145">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="f5de2-146">此时将显示 " **引用约束** " 对话框。</span><span class="sxs-lookup"><span data-stu-id="f5de2-146">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="f5de2-147">从 " **主体** " 下拉列表中，选择引用约束中的主体实体。</span><span class="sxs-lookup"><span data-stu-id="f5de2-147">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="f5de2-148">实体的键属性将添加到对话框中的 " **主体键** " 列表中。</span><span class="sxs-lookup"><span data-stu-id="f5de2-148">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="f5de2-149">从 " **依赖项** " 下拉列表中，选择引用约束中的依赖实体。</span><span class="sxs-lookup"><span data-stu-id="f5de2-149">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="f5de2-150">对于具有依赖键的每个主体键，从 " **依赖键** " 列中的下拉列表中选择相应的依赖项。</span><span class="sxs-lookup"><span data-stu-id="f5de2-150">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 约束](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="f5de2-152">单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="f5de2-152">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="f5de2-153">创建和编辑关联映射</span><span class="sxs-lookup"><span data-stu-id="f5de2-153">Create and Edit Association Mappings</span></span>

<span data-ttu-id="f5de2-154">可以在 EF 设计器的 "**映射详细信息**" 窗口中指定关联映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="f5de2-154">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="f5de2-155">只能映射未指定引用约束的关联的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f5de2-155">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="f5de2-156">如果指定了引用约束，则会在实体中包含一个外键属性，您可以使用该实体的映射详细信息来控制外键映射到的列。</span><span class="sxs-lookup"><span data-stu-id="f5de2-156">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="f5de2-157">创建关联映射</span><span class="sxs-lookup"><span data-stu-id="f5de2-157">Create an association mapping</span></span>

-   <span data-ttu-id="f5de2-158">右键单击设计图面中的关联，然后选择 " **表映射**"。</span><span class="sxs-lookup"><span data-stu-id="f5de2-158">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="f5de2-159">这会在 " **映射详细信息**" 窗口中显示关联映射。</span><span class="sxs-lookup"><span data-stu-id="f5de2-159">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="f5de2-160">单击 " **添加表或视图**"。</span><span class="sxs-lookup"><span data-stu-id="f5de2-160">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="f5de2-161">此时将显示一个下拉列表，其中包含存储模型中的所有表。</span><span class="sxs-lookup"><span data-stu-id="f5de2-161">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="f5de2-162">选择关联要映射到的表。</span><span class="sxs-lookup"><span data-stu-id="f5de2-162">Select the table to which the association will map.</span></span>
    <span data-ttu-id="f5de2-163">" **映射详细信息**" "窗口将显示关联的两端以及每**一端**的实体类型的键属性。</span><span class="sxs-lookup"><span data-stu-id="f5de2-163">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="f5de2-164">对于每个键属性，请单击 " **列** " 字段，然后选择属性将映射到的列。</span><span class="sxs-lookup"><span data-stu-id="f5de2-164">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![映射详细信息4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="f5de2-166">编辑关联映射</span><span class="sxs-lookup"><span data-stu-id="f5de2-166">Edit an association mapping</span></span>

-   <span data-ttu-id="f5de2-167">右键单击设计图面中的关联，然后选择 " **表映射**"。</span><span class="sxs-lookup"><span data-stu-id="f5de2-167">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="f5de2-168">这会在 " **映射详细信息**" 窗口中显示关联映射。</span><span class="sxs-lookup"><span data-stu-id="f5de2-168">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="f5de2-169">单击 " **映射" &lt;表名称&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="f5de2-169">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="f5de2-170">此时将显示一个下拉列表，其中包含存储模型中的所有表。</span><span class="sxs-lookup"><span data-stu-id="f5de2-170">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="f5de2-171">选择关联要映射到的表。</span><span class="sxs-lookup"><span data-stu-id="f5de2-171">Select the table to which the association will map.</span></span>
    <span data-ttu-id="f5de2-172">" **映射详细信息**" "窗口将显示关联的两端以及每一端的实体类型的键属性。</span><span class="sxs-lookup"><span data-stu-id="f5de2-172">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="f5de2-173">对于每个键属性，请单击 " **列** " 字段，然后选择属性将映射到的列。</span><span class="sxs-lookup"><span data-stu-id="f5de2-173">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="f5de2-174">编辑和删除导航属性</span><span class="sxs-lookup"><span data-stu-id="f5de2-174">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="f5de2-175">导航属性是快捷方式属性，用于在模型中关联的两端查找实体。</span><span class="sxs-lookup"><span data-stu-id="f5de2-175">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="f5de2-176">在创建两个实体类型之间的关联时可以创建导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5de2-176">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="f5de2-177">编辑导航属性</span><span class="sxs-lookup"><span data-stu-id="f5de2-177">To edit navigation properties</span></span>

-   <span data-ttu-id="f5de2-178">选择 EF 设计器图面上的导航属性。</span><span class="sxs-lookup"><span data-stu-id="f5de2-178">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="f5de2-179">有关导航属性的信息将显示在 Visual Studio 的 " **属性**" "窗口中。</span><span class="sxs-lookup"><span data-stu-id="f5de2-179">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="f5de2-180">在 " **属性**" 窗口中更改属性设置。</span><span class="sxs-lookup"><span data-stu-id="f5de2-180">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="f5de2-181">删除导航属性</span><span class="sxs-lookup"><span data-stu-id="f5de2-181">To delete navigation properties</span></span>

-   <span data-ttu-id="f5de2-182">如果概念模型中的实体类型上没有公开外键，则删除导航属性可能导致仅在一个方向遍历相应的关联，或者根本不遍历关联。</span><span class="sxs-lookup"><span data-stu-id="f5de2-182">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="f5de2-183">右键单击 EF 设计器图面上的导航属性，然后选择 " **删除**"。</span><span class="sxs-lookup"><span data-stu-id="f5de2-183">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
