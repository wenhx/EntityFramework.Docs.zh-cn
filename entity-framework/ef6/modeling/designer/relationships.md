---
title: 关系-EF 设计器-EF6
description: 关系-实体框架6中的 EF 设计器
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: f7c801dd8f8ae81cfe44283b7575cfe869ce26d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620424"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="a23f6-103">关系-EF 设计器</span><span class="sxs-lookup"><span data-stu-id="a23f6-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="a23f6-104">本页提供有关使用 EF 设计器设置模型中的关系的信息。</span><span class="sxs-lookup"><span data-stu-id="a23f6-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="a23f6-105">有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅 [关系 & 导航属性](xref:ef6/fundamentals/relationships)。</span><span class="sxs-lookup"><span data-stu-id="a23f6-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="a23f6-106">关联定义模型中的实体类型之间的关系。</span><span class="sxs-lookup"><span data-stu-id="a23f6-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="a23f6-107">本主题说明如何将关联与 Entity Framework Designer (EF 设计器) 进行映射。</span><span class="sxs-lookup"><span data-stu-id="a23f6-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="a23f6-108">下图显示了在使用 EF 设计器时使用的主窗口。</span><span class="sxs-lookup"><span data-stu-id="a23f6-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF 设计器](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="a23f6-110">在生成概念模型时，有关未映射的实体和关联的警告可能会显示在“错误列表”中。</span><span class="sxs-lookup"><span data-stu-id="a23f6-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="a23f6-111">您可以忽略这些警告，因为在您选择从模型生成数据库后，错误将消失。</span><span class="sxs-lookup"><span data-stu-id="a23f6-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="a23f6-112">关联概述</span><span class="sxs-lookup"><span data-stu-id="a23f6-112">Associations Overview</span></span>

<span data-ttu-id="a23f6-113">使用 EF 设计器设计模型时，.edmx 文件表示您的模型。</span><span class="sxs-lookup"><span data-stu-id="a23f6-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="a23f6-114">在 .edmx 文件中， **Association** 元素定义了两个实体类型之间的关系。</span><span class="sxs-lookup"><span data-stu-id="a23f6-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="a23f6-115">关联必须指定关系中涉及的实体类型和关系的每一端可能的实体类型数量（也称为重数）。</span><span class="sxs-lookup"><span data-stu-id="a23f6-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="a23f6-116">关联端的多重性的 (值可以是 1) 、零或一个 (0 .. 1) 或多个 (\*) 。</span><span class="sxs-lookup"><span data-stu-id="a23f6-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="a23f6-117">此信息是在两个子 **结束** 元素中指定的。</span><span class="sxs-lookup"><span data-stu-id="a23f6-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="a23f6-118">在运行时，如果选择在实体) 中公开外键，则可以通过导航属性或外键访问关联一端的实体类型实例 (。</span><span class="sxs-lookup"><span data-stu-id="a23f6-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="a23f6-119">公开外键后，实体之间的关系将使用 **ReferentialConstraint** 元素进行管理， (**关联** 元素) 的子元素。</span><span class="sxs-lookup"><span data-stu-id="a23f6-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="a23f6-120">建议你始终公开实体中关系的外键。</span><span class="sxs-lookup"><span data-stu-id="a23f6-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="a23f6-121">在多对多 (\* ： \*) 不能将外键添加到实体。</span><span class="sxs-lookup"><span data-stu-id="a23f6-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="a23f6-122">在 \* ： \* 关系中，使用独立的对象管理关联信息。</span><span class="sxs-lookup"><span data-stu-id="a23f6-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="a23f6-123">有关 CSDL 元素 (**ReferentialConstraint**、 **Association**等 ) 的信息，请参阅 [csdl 规范](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。</span><span class="sxs-lookup"><span data-stu-id="a23f6-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="a23f6-124">创建和删除关联</span><span class="sxs-lookup"><span data-stu-id="a23f6-124">Create and Delete Associations</span></span>

<span data-ttu-id="a23f6-125">使用 EF 设计器创建关联会更新 .edmx 文件的模型内容。</span><span class="sxs-lookup"><span data-stu-id="a23f6-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="a23f6-126">创建关联后，您必须为本主题后面讨论的关联 (创建映射) 。</span><span class="sxs-lookup"><span data-stu-id="a23f6-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="a23f6-127">本部分假定已添加要在模型之间创建关联的实体。</span><span class="sxs-lookup"><span data-stu-id="a23f6-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="a23f6-128">创建关联</span><span class="sxs-lookup"><span data-stu-id="a23f6-128">To create an association</span></span>

1.  <span data-ttu-id="a23f6-129">右键单击设计图面的空白区域，指向 " **添加新**项"，然后选择 " **关联 ...**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="a23f6-130">在 " **添加关联** " 对话框中填写关联的设置。</span><span class="sxs-lookup"><span data-stu-id="a23f6-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![添加关联](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="a23f6-132">您可以通过清除 **导航属性 **并 **将外键属性添加到 " &lt; 实体类型名称 &gt; 实体" **复选框，选择不向关联端的实体添加导航属性或外键属性。</span><span class="sxs-lookup"><span data-stu-id="a23f6-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="a23f6-133">如果只添加一个导航属性，则将只能在一个方向遍历关联。</span><span class="sxs-lookup"><span data-stu-id="a23f6-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="a23f6-134">如果不添加导航属性，则必须选择添加外键属性才能访问位于关联各端的实体。</span><span class="sxs-lookup"><span data-stu-id="a23f6-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="a23f6-135">单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="a23f6-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="a23f6-136">删除关联</span><span class="sxs-lookup"><span data-stu-id="a23f6-136">To delete an association</span></span>

<span data-ttu-id="a23f6-137">若要删除关联，请执行以下操作之一：</span><span class="sxs-lookup"><span data-stu-id="a23f6-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="a23f6-138">右键单击 EF 设计器图面上的关联，然后选择 " **删除**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="a23f6-139">或 -</span><span class="sxs-lookup"><span data-stu-id="a23f6-139">OR -</span></span>

-   <span data-ttu-id="a23f6-140">选择一个或多个关联并按 Delete 键。</span><span class="sxs-lookup"><span data-stu-id="a23f6-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="a23f6-141">将外键属性包含在实体中 (引用约束) </span><span class="sxs-lookup"><span data-stu-id="a23f6-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="a23f6-142">建议你始终公开实体中关系的外键。</span><span class="sxs-lookup"><span data-stu-id="a23f6-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="a23f6-143">实体框架使用引用约束来标识某个属性充当关系的外键。</span><span class="sxs-lookup"><span data-stu-id="a23f6-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="a23f6-144">如果在创建关系时选中了 " ***将外键属性添加到 &lt; 实体类型名称 &gt; 实体*** " 复选框，则会为你添加此引用约束。</span><span class="sxs-lookup"><span data-stu-id="a23f6-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="a23f6-145">使用 EF 设计器添加或编辑引用约束时，EF 设计器会**ReferentialConstraint**   在 .EDMX 文件的 CSDL 内容中添加或修改 ReferentialConstraint 元素。</span><span class="sxs-lookup"><span data-stu-id="a23f6-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="a23f6-146">双击要编辑的关联。</span><span class="sxs-lookup"><span data-stu-id="a23f6-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="a23f6-147">此时将显示 " **引用约束**"   对话框。</span><span class="sxs-lookup"><span data-stu-id="a23f6-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="a23f6-148">从 " **主体**"   下拉列表中，选择引用约束中的主体实体。</span><span class="sxs-lookup"><span data-stu-id="a23f6-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="a23f6-149">实体的键属性将添加到对话框中的 **主体键**   列表。</span><span class="sxs-lookup"><span data-stu-id="a23f6-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="a23f6-150">从 " **依赖项**"   下拉列表中，选择引用约束中的依赖实体。</span><span class="sxs-lookup"><span data-stu-id="a23f6-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="a23f6-151">对于具有依赖键的每个主体键，从 " **依赖键**" 列中的下拉列表中选择相应的依赖项   。</span><span class="sxs-lookup"><span data-stu-id="a23f6-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 约束](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="a23f6-153">单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="a23f6-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="a23f6-154">创建和编辑关联映射</span><span class="sxs-lookup"><span data-stu-id="a23f6-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="a23f6-155">您可以在 EF 设计器的 "**映射详细信息**" 窗口中指定关联映射到数据库的方式   。</span><span class="sxs-lookup"><span data-stu-id="a23f6-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="a23f6-156">只能映射未指定引用约束的关联的详细信息。</span><span class="sxs-lookup"><span data-stu-id="a23f6-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="a23f6-157">如果指定了引用约束，则会在实体中包含一个外键属性，您可以使用该实体的映射详细信息来控制外键映射到的列。</span><span class="sxs-lookup"><span data-stu-id="a23f6-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="a23f6-158">创建关联映射</span><span class="sxs-lookup"><span data-stu-id="a23f6-158">Create an association mapping</span></span>

-   <span data-ttu-id="a23f6-159">右键单击设计图面中的关联，然后选择 " **表映射**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="a23f6-160">这会在 " **映射详细信息**" 窗口中显示关联映射   。</span><span class="sxs-lookup"><span data-stu-id="a23f6-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="a23f6-161">单击 " **添加表或视图**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="a23f6-162">此时将显示一个下拉列表，其中包含存储模型中的所有表。</span><span class="sxs-lookup"><span data-stu-id="a23f6-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="a23f6-163">选择关联要映射到的表。</span><span class="sxs-lookup"><span data-stu-id="a23f6-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="a23f6-164">" **映射详细信息**"   窗口显示关联的两端以及每**一端**的实体类型的键属性。</span><span class="sxs-lookup"><span data-stu-id="a23f6-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="a23f6-165">对于每个键属性，请单击 " **列**"   字段，然后选择属性将映射到的列。</span><span class="sxs-lookup"><span data-stu-id="a23f6-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![映射详细信息4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="a23f6-167">编辑关联映射</span><span class="sxs-lookup"><span data-stu-id="a23f6-167">Edit an association mapping</span></span>

-   <span data-ttu-id="a23f6-168">右键单击设计图面中的关联，然后选择 " **表映射**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="a23f6-169">这会在 " **映射详细信息**" 窗口中显示关联映射   。</span><span class="sxs-lookup"><span data-stu-id="a23f6-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="a23f6-170">单击 " **映射到 &lt; 表 &gt; 名称**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="a23f6-171">此时将显示一个下拉列表，其中包含存储模型中的所有表。</span><span class="sxs-lookup"><span data-stu-id="a23f6-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="a23f6-172">选择关联要映射到的表。</span><span class="sxs-lookup"><span data-stu-id="a23f6-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="a23f6-173">" **映射详细信息**"   窗口显示关联的两端以及每一端的实体类型的键属性。</span><span class="sxs-lookup"><span data-stu-id="a23f6-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="a23f6-174">对于每个键属性，请单击 " **列**"   字段，然后选择属性将映射到的列。</span><span class="sxs-lookup"><span data-stu-id="a23f6-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="a23f6-175">编辑和删除导航属性</span><span class="sxs-lookup"><span data-stu-id="a23f6-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="a23f6-176">导航属性是快捷方式属性，用于在模型中关联的两端查找实体。</span><span class="sxs-lookup"><span data-stu-id="a23f6-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="a23f6-177">在创建两个实体类型之间的关联时可以创建导航属性。</span><span class="sxs-lookup"><span data-stu-id="a23f6-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="a23f6-178">编辑导航属性</span><span class="sxs-lookup"><span data-stu-id="a23f6-178">To edit navigation properties</span></span>

-   <span data-ttu-id="a23f6-179">选择 EF 设计器图面上的导航属性。</span><span class="sxs-lookup"><span data-stu-id="a23f6-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="a23f6-180">有关导航属性的信息将显示在 Visual Studio 的 " **属性**"   窗口中。</span><span class="sxs-lookup"><span data-stu-id="a23f6-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="a23f6-181">在 " **属性**" 窗口中更改属性设置   。</span><span class="sxs-lookup"><span data-stu-id="a23f6-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="a23f6-182">删除导航属性</span><span class="sxs-lookup"><span data-stu-id="a23f6-182">To delete navigation properties</span></span>

-   <span data-ttu-id="a23f6-183">如果概念模型中的实体类型上没有公开外键，则删除导航属性可能导致仅在一个方向遍历相应的关联，或者根本不遍历关联。</span><span class="sxs-lookup"><span data-stu-id="a23f6-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="a23f6-184">右键单击 EF 设计器图面上的导航属性，然后选择 " **删除**"。</span><span class="sxs-lookup"><span data-stu-id="a23f6-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
