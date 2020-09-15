---
title: 复杂类型-EF 设计器-EF6
description: 复杂类型-实体框架6中的 EF 设计器
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 6ebba9ea36ddad1a25b21bf98a6ac3090c327144
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073508"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="edac4-103">复杂类型-EF 设计器</span><span class="sxs-lookup"><span data-stu-id="edac4-103">Complex Types - EF Designer</span></span>
<span data-ttu-id="edac4-104">本主题演示如何将复杂类型映射到 Entity Framework Designer (EF 设计器) 以及如何查询包含复杂类型属性的实体。</span><span class="sxs-lookup"><span data-stu-id="edac4-104">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="edac4-105">下图显示了在使用 EF 设计器时使用的主窗口。</span><span class="sxs-lookup"><span data-stu-id="edac4-105">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF 设计器](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="edac4-107">在生成概念模型时，有关未映射的实体和关联的警告可能会显示在“错误列表”中。</span><span class="sxs-lookup"><span data-stu-id="edac4-107">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="edac4-108">您可以忽略这些警告，因为在您选择从模型生成数据库后，错误将消失。</span><span class="sxs-lookup"><span data-stu-id="edac4-108">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="edac4-109">什么是复杂类型</span><span class="sxs-lookup"><span data-stu-id="edac4-109">What is a Complex Type</span></span>

<span data-ttu-id="edac4-110">复杂类型是实体类型的非标量属性，实体类型允许在实体内组织标量属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-110">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="edac4-111">与实体相似，复杂类型由标量属性或者其他复杂类型属性组成。</span><span class="sxs-lookup"><span data-stu-id="edac4-111">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="edac4-112">使用表示复杂类型的对象时，请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="edac4-112">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="edac4-113">复杂类型没有键，因此不能独立存在。</span><span class="sxs-lookup"><span data-stu-id="edac4-113">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="edac4-114">复杂类型只能作为实体类型或其他复杂类型的属性而存在。</span><span class="sxs-lookup"><span data-stu-id="edac4-114">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="edac4-115">复杂类型不能参与关联且不能包含导航属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-115">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="edac4-116">复杂类型属性不能为 **null**。</span><span class="sxs-lookup"><span data-stu-id="edac4-116">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="edac4-117">当**InvalidOperationException **调用**DbContext**   ，并且遇到 null 复杂对象时，会发生 InvalidOperationException。</span><span class="sxs-lookup"><span data-stu-id="edac4-117">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="edac4-118">复杂对象的标量属性可以为 **null**。</span><span class="sxs-lookup"><span data-stu-id="edac4-118">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="edac4-119">复杂类型不能从其他复杂类型继承。</span><span class="sxs-lookup"><span data-stu-id="edac4-119">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="edac4-120">必须将复杂类型定义为 **类**。</span><span class="sxs-lookup"><span data-stu-id="edac4-120">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="edac4-121">调用 **DetectChanges** 时，EF 检测对复杂类型对象上的成员所做的更改。</span><span class="sxs-lookup"><span data-stu-id="edac4-121">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="edac4-122">调用以下成员时，实体框架自动调用**DetectChanges** ： **DbSet**、 **DbSet** **、** **DbSet** **、DbSet**、 **DbSet、DbContext** **、DbContext** **、GetValidationErrors**、DbContext、DbChangeTracker、 **DbChangeTracker.Entries**、。</span><span class="sxs-lookup"><span data-stu-id="edac4-122">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="edac4-123">将实体的属性重构为新的复杂类型</span><span class="sxs-lookup"><span data-stu-id="edac4-123">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="edac4-124">如果概念模型中已经有一个实体，可能想要将某些属性重构为复杂类型属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-124">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="edac4-125">在设计器图面上，选择一个或多个属性 () 实体的导航属性，然后右键单击并选择 " **重构- &gt; 移动到新的复杂类型**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-125">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![重构为新的复杂类型](~/ef6/media/refactor.png)

<span data-ttu-id="edac4-127">具有选定属性的新复杂类型将添加到 **模型浏览器**。</span><span class="sxs-lookup"><span data-stu-id="edac4-127">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="edac4-128">此复杂类型会被赋予一个默认名称。</span><span class="sxs-lookup"><span data-stu-id="edac4-128">The complex type is given a default name.</span></span>

<span data-ttu-id="edac4-129">新创建类型的复杂属性将替换选定属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-129">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="edac4-130">所有属性映射都将保留。</span><span class="sxs-lookup"><span data-stu-id="edac4-130">All property mappings are preserved.</span></span>

![重构为新的复杂类型2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="edac4-132">创建新的复杂类型</span><span class="sxs-lookup"><span data-stu-id="edac4-132">Create a New Complex Type</span></span>

<span data-ttu-id="edac4-133">你还可以创建不包含现有实体的属性的新复杂类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-133">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="edac4-134">右键单击 "模型浏览器" 中的 " **复杂类型** " 文件夹，指向 " **AddNew 复杂类型 ...**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-134">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="edac4-135">或者，您可以选择 "**复杂类型**" 文件夹，然后 **Insert**   在键盘上按 "Insert" 键。</span><span class="sxs-lookup"><span data-stu-id="edac4-135">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![添加新的复杂类型](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="edac4-137">新复杂类型将添加到具有默认名称的文件夹。</span><span class="sxs-lookup"><span data-stu-id="edac4-137">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="edac4-138">你现在可以将属性添加到该类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-138">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="edac4-139">向复杂类型添加属性</span><span class="sxs-lookup"><span data-stu-id="edac4-139">Add Properties to a Complex Type</span></span>

<span data-ttu-id="edac4-140">复杂类型的属性可以是标量类型或现有的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-140">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="edac4-141">但是，复杂类型属性无法具有循环引用。</span><span class="sxs-lookup"><span data-stu-id="edac4-141">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="edac4-142">例如，复杂类型 **OnsiteCourseDetails**   不能具有复杂类型 **OnsiteCourseDetails**的属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-142">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="edac4-143">可以通过下面列出的任何方式向复杂类型添加属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-143">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="edac4-144">在模型浏览器中右键单击复杂类型，指向 " **添加**"，再指向 " **标量属性**   " 或 " **复杂属性**"，然后选择所需的属性类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-144">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="edac4-145">或者，可以选择复杂类型，然后按键盘上的 " **Insert**"   键。</span><span class="sxs-lookup"><span data-stu-id="edac4-145">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![向复杂类型添加属性](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="edac4-147">新属性将添加到具有默认名称的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-147">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="edac4-148">或 -</span><span class="sxs-lookup"><span data-stu-id="edac4-148">OR -</span></span>

-   <span data-ttu-id="edac4-149">右键单击 **EF 设计器** 图面上的实体属性，选择 " **复制**"，然后在 " **模型浏览器** " 中右键单击复杂类型，然后选择 " **粘贴**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-149">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="edac4-150">重命名复杂类型</span><span class="sxs-lookup"><span data-stu-id="edac4-150">Rename a Complex Type</span></span>

<span data-ttu-id="edac4-151">重命名复杂类型时，将通过项目更新对类型的所有引用。</span><span class="sxs-lookup"><span data-stu-id="edac4-151">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="edac4-152">在 **模型浏览器**中，慢慢地双击复杂类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-152">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="edac4-153">名称将选定并处于编辑模式。</span><span class="sxs-lookup"><span data-stu-id="edac4-153">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="edac4-154">或 -</span><span class="sxs-lookup"><span data-stu-id="edac4-154">OR -</span></span>

-   <span data-ttu-id="edac4-155">在 **模型浏览器** 中右键单击复杂类型，然后选择 " **重命名**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-155">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="edac4-156">或 -</span><span class="sxs-lookup"><span data-stu-id="edac4-156">OR -</span></span>

-   <span data-ttu-id="edac4-157">在模型浏览器中选择复杂类型，按 F2 键。</span><span class="sxs-lookup"><span data-stu-id="edac4-157">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="edac4-158">或 -</span><span class="sxs-lookup"><span data-stu-id="edac4-158">OR -</span></span>

-   <span data-ttu-id="edac4-159">在 **模型浏览器** 中右键单击复杂类型，然后选择 " **属性**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-159">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="edac4-160">在 " **属性**" 窗口中编辑该名称   。</span><span class="sxs-lookup"><span data-stu-id="edac4-160">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="edac4-161">将现有复杂类型添加到实体，并将其属性映射到表列</span><span class="sxs-lookup"><span data-stu-id="edac4-161">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="edac4-162">右键单击某个实体，指向 " **添加新**项"，然后选择 " **复杂属性**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-162">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="edac4-163">具有默认名称的复杂类型属性将添加到该实体。</span><span class="sxs-lookup"><span data-stu-id="edac4-163">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="edac4-164">默认类型（从现有复杂类型中选择）将指派给该属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-164">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="edac4-165">将所需的类型分配给 " **属性**" 窗口中的属性   。</span><span class="sxs-lookup"><span data-stu-id="edac4-165">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="edac4-166">在将复杂类型属性添加到实体后，必须将其属性映射到表列。</span><span class="sxs-lookup"><span data-stu-id="edac4-166">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="edac4-167">右键单击设计图面或 **模型浏览器**中的某个实体类型   ，然后选择 " **表映射**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-167">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="edac4-168">表映射显示在 " **映射详细信息**"   窗口中。</span><span class="sxs-lookup"><span data-stu-id="edac4-168">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="edac4-169">展开 " \*\*映射到 &lt; 表名称 &gt; \*\*"   节点。</span><span class="sxs-lookup"><span data-stu-id="edac4-169">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="edac4-170">此时会显示 " **列映射**"   节点。</span><span class="sxs-lookup"><span data-stu-id="edac4-170">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="edac4-171">展开 " **列映射**"   节点。</span><span class="sxs-lookup"><span data-stu-id="edac4-171">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="edac4-172">出现一个包含表中所有列的列表。</span><span class="sxs-lookup"><span data-stu-id="edac4-172">A list of all the columns in the table appears.</span></span> <span data-ttu-id="edac4-173">如果列映射列在 " **值/属性**" 标题下列出的任何) ，则默认属性 (  。</span><span class="sxs-lookup"><span data-stu-id="edac4-173">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="edac4-174">选择要映射的列，然后右键单击相应的 " **值/属性**"   字段。</span><span class="sxs-lookup"><span data-stu-id="edac4-174">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="edac4-175">出现一个包含所有标量属性的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="edac4-175">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="edac4-176">选择适当的属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-176">Select the appropriate property.</span></span>

    ![映射复杂类型](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="edac4-178">对每一个表列重复步骤 6 和 7。</span><span class="sxs-lookup"><span data-stu-id="edac4-178">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="edac4-179">若要删除列映射，请选择要映射的列，然后单击 " **值/属性**"   字段。</span><span class="sxs-lookup"><span data-stu-id="edac4-179">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="edac4-180">然后，从下拉列表中选择 " **删除** "。</span><span class="sxs-lookup"><span data-stu-id="edac4-180">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="edac4-181">将函数导入映射到复杂类型</span><span class="sxs-lookup"><span data-stu-id="edac4-181">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="edac4-182">函数导入基于存储过程。</span><span class="sxs-lookup"><span data-stu-id="edac4-182">Function imports are based on stored procedures.</span></span> <span data-ttu-id="edac4-183">若要将函数导入映射到复杂类型，相应存储过程返回的列必须在数量上与复杂类型的属性匹配并且必须具有与属性类型兼容的存储类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-183">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="edac4-184">双击要映射到复杂类型的已导入函数。</span><span class="sxs-lookup"><span data-stu-id="edac4-184">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![函数导入](~/ef6/media/functionimports.png)

-   <span data-ttu-id="edac4-186">填入新函数导入的设置，如下所示：</span><span class="sxs-lookup"><span data-stu-id="edac4-186">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="edac4-187">在 " **存储过程名称**" 字段中指定要为其创建函数导入的存储过程   。</span><span class="sxs-lookup"><span data-stu-id="edac4-187">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="edac4-188">此字段是一个下拉列表，其中显示存储模型中的所有存储过程。</span><span class="sxs-lookup"><span data-stu-id="edac4-188">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="edac4-189">在 " **函数导入名称**" 字段中指定函数导入的名称   。</span><span class="sxs-lookup"><span data-stu-id="edac4-189">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="edac4-190">选择 " **复杂**"   作为返回类型，然后通过从下拉列表中选择适当的类型来指定特定的复杂返回类型。</span><span class="sxs-lookup"><span data-stu-id="edac4-190">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![编辑函数导入](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="edac4-192">单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="edac4-192">Click **OK**.</span></span>
    <span data-ttu-id="edac4-193">此时将在概念模型中创建函数导入项。</span><span class="sxs-lookup"><span data-stu-id="edac4-193">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="edac4-194">自定义函数导入的列映射</span><span class="sxs-lookup"><span data-stu-id="edac4-194">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="edac4-195">右键单击模型浏览器中的函数导入，然后选择 " **函数导入映射**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-195">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="edac4-196">此时将出现 " **映射详细信息**"   窗口，并显示函数导入的默认映射。</span><span class="sxs-lookup"><span data-stu-id="edac4-196">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="edac4-197">箭头指示列值和属性值之间的映射。</span><span class="sxs-lookup"><span data-stu-id="edac4-197">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="edac4-198">默认情况，假设列名称与复杂类型的属性名称相同。</span><span class="sxs-lookup"><span data-stu-id="edac4-198">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="edac4-199">默认的列名称以灰色文本显示。</span><span class="sxs-lookup"><span data-stu-id="edac4-199">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="edac4-200">如有必要，请更改列名称以匹配由对应于函数导入的存储过程返回的列名称。</span><span class="sxs-lookup"><span data-stu-id="edac4-200">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="edac4-201">删除复杂类型</span><span class="sxs-lookup"><span data-stu-id="edac4-201">Delete a Complex Type</span></span>

<span data-ttu-id="edac4-202">删除复杂类型时，从概念模型删除类型，并且将删除该类型所有实例的映射。</span><span class="sxs-lookup"><span data-stu-id="edac4-202">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="edac4-203">但是，不更新对类型的引用。</span><span class="sxs-lookup"><span data-stu-id="edac4-203">However, references to the type are not updated.</span></span> <span data-ttu-id="edac4-204">例如，如果实体具有类型为 ComplexType1 的复杂类型属性，并且在 **模型浏览器**中删除了 ComplexType1，则不会更新相应的实体属性。</span><span class="sxs-lookup"><span data-stu-id="edac4-204">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="edac4-205">模型将不会验证，因为它包含引用已删除复杂类型的实体。</span><span class="sxs-lookup"><span data-stu-id="edac4-205">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="edac4-206">可以使用实体设计器更新或删除对已删除复杂类型的引用。</span><span class="sxs-lookup"><span data-stu-id="edac4-206">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="edac4-207">在模型浏览器中右键单击复杂类型，然后选择 " **删除**"。</span><span class="sxs-lookup"><span data-stu-id="edac4-207">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="edac4-208">或 -</span><span class="sxs-lookup"><span data-stu-id="edac4-208">OR -</span></span>

-   <span data-ttu-id="edac4-209">在模型浏览器中选择复杂类型，然后按键盘上的“Delete”键。</span><span class="sxs-lookup"><span data-stu-id="edac4-209">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="edac4-210">查询包含复杂类型属性的实体</span><span class="sxs-lookup"><span data-stu-id="edac4-210">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="edac4-211">下面的代码演示如何执行一个查询，该查询返回包含复杂类型属性的实体类型对象的集合。</span><span class="sxs-lookup"><span data-stu-id="edac4-211">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

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
