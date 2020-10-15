---
title: 设计器 TPH 继承-EF6
description: 实体框架6中的设计器 TPH 继承
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: bf689e02a76e46e5eee5a64cb9825e667c8cd65c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064843"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="fcc00-103">设计器 TPH 继承</span><span class="sxs-lookup"><span data-stu-id="fcc00-103">Designer TPH Inheritance</span></span>
<span data-ttu-id="fcc00-104">此分步演练演示如何使用 Entity Framework Designer (EF 设计器) 实现概念模型中的每个层次结构一个表 (TPH) 继承。</span><span class="sxs-lookup"><span data-stu-id="fcc00-104">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="fcc00-105">TPH 继承使用一个数据库表来维护继承层次结构中所有实体类型的数据。</span><span class="sxs-lookup"><span data-stu-id="fcc00-105">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="fcc00-106">在本演练中，我们会将 Person 表映射到三个实体类型： Person (基类型) 、学生 (派生自人员) ，以及人员派生的指导员 (。</span><span class="sxs-lookup"><span data-stu-id="fcc00-106">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="fcc00-107">我们将从数据库 () Database First 创建概念模型，然后使用 EF 设计器更改模型以实现 TPH 继承。</span><span class="sxs-lookup"><span data-stu-id="fcc00-107">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="fcc00-108">可以使用 Model First 映射到 TPH 继承，但您必须编写自己的数据库生成工作流，该工作流非常复杂。</span><span class="sxs-lookup"><span data-stu-id="fcc00-108">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="fcc00-109">然后，将此工作流分配到 EF 设计器中的 " **数据库生成工作流** " 属性。</span><span class="sxs-lookup"><span data-stu-id="fcc00-109">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="fcc00-110">更简单的替代方法是使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="fcc00-110">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="fcc00-111">其他继承选项</span><span class="sxs-lookup"><span data-stu-id="fcc00-111">Other Inheritance Options</span></span>

<span data-ttu-id="fcc00-112"> (TPT) 的每种类型一个表是另一种类型的继承，其中，数据库中的单独表映射到参与继承的实体。</span><span class="sxs-lookup"><span data-stu-id="fcc00-112">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="fcc00-113">有关如何将每种类型一个表继承映射到 EF 设计器的信息，请参阅 [Ef 设计器 TPT 继承](xref:ef6/modeling/designer/inheritance/tpt)。</span><span class="sxs-lookup"><span data-stu-id="fcc00-113"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](xref:ef6/modeling/designer/inheritance/tpt).</span></span>

<span data-ttu-id="fcc00-114">实体框架运行时 (TPC) 和混合继承模型支持每个具体的表的类型继承，但 EF 设计器不支持。</span><span class="sxs-lookup"><span data-stu-id="fcc00-114">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="fcc00-115">如果要使用 TPC 或混合继承，则有两个选项：使用 Code First 或手动编辑 EDMX 文件。</span><span class="sxs-lookup"><span data-stu-id="fcc00-115">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="fcc00-116">如果选择处理 EDMX 文件，"映射详细信息" 窗口将进入 "安全模式"，您将无法使用设计器来更改映射。</span><span class="sxs-lookup"><span data-stu-id="fcc00-116">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fcc00-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="fcc00-117">Prerequisites</span></span>

<span data-ttu-id="fcc00-118">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="fcc00-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="fcc00-119">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="fcc00-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="fcc00-120">[School 示例数据库](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="fcc00-120">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="fcc00-121">设置项目</span><span class="sxs-lookup"><span data-stu-id="fcc00-121">Set up the Project</span></span>

-   <span data-ttu-id="fcc00-122">打开 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="fcc00-122">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="fcc00-123">选择 **文件- &gt; 新建- &gt; 项目**</span><span class="sxs-lookup"><span data-stu-id="fcc00-123">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="fcc00-124">在左窗格中，单击 " **Visual \# C**"，然后选择 "**控制台**" 模板。</span><span class="sxs-lookup"><span data-stu-id="fcc00-124">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="fcc00-125">输入 **TPHDBFirstSample**   作为名称。</span><span class="sxs-lookup"><span data-stu-id="fcc00-125">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="fcc00-126">选择“确定”。 \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="fcc00-126">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="fcc00-127">创建模型</span><span class="sxs-lookup"><span data-stu-id="fcc00-127">Create a Model</span></span>

-   <span data-ttu-id="fcc00-128">在解决方案资源管理器中右键单击项目名称，然后选择 " **添加- &gt; 新建项**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-128">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="fcc00-129">从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。</span><span class="sxs-lookup"><span data-stu-id="fcc00-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="fcc00-130">输入 **TPHModel** 作为文件名，然后单击 " **添加**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-130">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="fcc00-131">在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="fcc00-132">单击 " **新建连接**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-132">Click **New Connection**.</span></span>
    <span data-ttu-id="fcc00-133">在 "连接属性" 对话框中，输入服务器名称 (例如， \*\* (localdb) \\ mssqllocaldb**) ，选择身份验证方法，为数据库名称键入 **School**，然后   单击 **"确定"\*\*。</span><span class="sxs-lookup"><span data-stu-id="fcc00-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="fcc00-134">"选择您的数据连接" 对话框将通过数据库连接设置进行更新。</span><span class="sxs-lookup"><span data-stu-id="fcc00-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="fcc00-135">在 "选择数据库对象" 对话框中的 "表" 节点下，选择 **Person** 表。</span><span class="sxs-lookup"><span data-stu-id="fcc00-135">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="fcc00-136">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-136">Click **Finish**.</span></span>

<span data-ttu-id="fcc00-137">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="fcc00-137">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="fcc00-138">您在 "选择数据库对象" 对话框中选择的所有对象都将添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="fcc00-138">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="fcc00-139">这就是 " **Person** " 表在数据库中的外观。</span><span class="sxs-lookup"><span data-stu-id="fcc00-139">That is how the **Person** table looks in the database.</span></span>

![Person 表](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="fcc00-141">实现每个层次结构一个表继承</span><span class="sxs-lookup"><span data-stu-id="fcc00-141">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="fcc00-142"> **Person**表具有**鉴别**器列，该列可以具有以下两个值之一： "Student" 和 "讲师"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-142">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="fcc00-143">根据值， **Person** 表将映射到 " **学生** " 实体或 " **讲师** " 实体。</span><span class="sxs-lookup"><span data-stu-id="fcc00-143">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="fcc00-144"> **Person**表还具有两列 " **雇佣**日期"   和 " **EnrollmentDate**"，这两列必须是可以**为 null**的，因为用户不能同时是学生和指导员 (此演练) 。</span><span class="sxs-lookup"><span data-stu-id="fcc00-144">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="fcc00-145">添加新实体</span><span class="sxs-lookup"><span data-stu-id="fcc00-145">Add new Entities</span></span>

-   <span data-ttu-id="fcc00-146">添加新实体。</span><span class="sxs-lookup"><span data-stu-id="fcc00-146">Add a new entity.</span></span>
    <span data-ttu-id="fcc00-147">为此，请右键单击 Entity Framework Designer 设计图面的空白区域，然后选择 " **添加 &gt; 实体**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-147">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="fcc00-148">为 **Instructor**   " **实体名称**" 键入 "指导员   "，然后 **Person**   从 " **基类型**" 下拉列表中选择 "人员"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-148">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="fcc00-149">单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="fcc00-149">Click **OK**.</span></span>
-   <span data-ttu-id="fcc00-150">添加另一个新实体。</span><span class="sxs-lookup"><span data-stu-id="fcc00-150">Add another new entity.</span></span> <span data-ttu-id="fcc00-151">键入 **Student**   "Student" 作为 " **实体名称**"   ，然后从 " **Person**    **基类型**" 下拉列表中选择 "人员"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-151">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="fcc00-152">已将两个新的实体类型添加到设计图面。</span><span class="sxs-lookup"><span data-stu-id="fcc00-152">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="fcc00-153">箭头指向 " **person**" 实体类型的新实体类型   ; 这表示该 **用户**   是新实体类型的基类型。</span><span class="sxs-lookup"><span data-stu-id="fcc00-153">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="fcc00-154">右键单击 Person 实体的 " **雇佣**日期"   属性 **Person**   。</span><span class="sxs-lookup"><span data-stu-id="fcc00-154">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="fcc00-155">选择 " **剪切** (或使用 Ctrl-X 键) 。</span><span class="sxs-lookup"><span data-stu-id="fcc00-155">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="fcc00-156">右键单击 **讲师**   实体，然后选择 " **粘贴** (或使用 Ctrl + V 键) 。</span><span class="sxs-lookup"><span data-stu-id="fcc00-156">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="fcc00-157">右键单击 " **雇佣**日期"   属性，然后选择 " **属性**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-157">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="fcc00-158">在 " **属性**"   窗口中，将 **可为 null**的   属性设置为 **false**。</span><span class="sxs-lookup"><span data-stu-id="fcc00-158">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="fcc00-159">右键单击 Person 实体的 **EnrollmentDate**   属性 **Person**   。</span><span class="sxs-lookup"><span data-stu-id="fcc00-159">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="fcc00-160">选择 " **剪切** (或使用 Ctrl-X 键) 。</span><span class="sxs-lookup"><span data-stu-id="fcc00-160">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="fcc00-161">右键单击 " **学生** " 实体，然后选择 " **粘贴 (或使用 Ctrl + V 键) 。**</span><span class="sxs-lookup"><span data-stu-id="fcc00-161">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="fcc00-162">选择 **EnrollmentDate**   属性并将 **可为 null**的   属性设置为 **false**。</span><span class="sxs-lookup"><span data-stu-id="fcc00-162">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="fcc00-163">选择 " **人员**"   实体类型。</span><span class="sxs-lookup"><span data-stu-id="fcc00-163">Select the **Person** entity type.</span></span> <span data-ttu-id="fcc00-164">在 " **属性**"   窗口中，将其 " **抽象**" 属性设置   为 " **true**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-164">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="fcc00-165">删除**Person**的**鉴别**器属性。</span><span class="sxs-lookup"><span data-stu-id="fcc00-165">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="fcc00-166">下一节将对其删除原因进行说明。</span><span class="sxs-lookup"><span data-stu-id="fcc00-166">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="fcc00-167">映射实体</span><span class="sxs-lookup"><span data-stu-id="fcc00-167">Map the entities</span></span>

-   <span data-ttu-id="fcc00-168">右键单击 **讲师** ，然后选择 " **表映射"。**</span><span class="sxs-lookup"><span data-stu-id="fcc00-168">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="fcc00-169">在 "映射详细信息" 窗口中选择 "指导员" 实体。</span><span class="sxs-lookup"><span data-stu-id="fcc00-169">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="fcc00-170">在 "映射详细信息" 窗口中单击 " \*\* &lt; 添加表或视图 &gt; \*\*   " **Mapping Details**   。</span><span class="sxs-lookup"><span data-stu-id="fcc00-170">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="fcc00-171">" \*\* &lt; 添加表" 或 " &gt; 视图\*\*"   字段将成为所选实体可映射到的表或视图的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="fcc00-171">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="fcc00-172"> **Person**   从下拉列表中选择 "人员"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-172">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="fcc00-173">" **映射详细信息**"   窗口将更新为默认的列映射，并提供一个用于添加条件的选项。</span><span class="sxs-lookup"><span data-stu-id="fcc00-173">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="fcc00-174">单击 " \*\* &lt; 添加条件 &gt; \*\*"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-174">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="fcc00-175">" \*\* &lt; 添加条件 &gt; \*\*"   字段将成为可以为其设置条件的列的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="fcc00-175">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="fcc00-176"> **Discriminator**   从下拉列表中选择 "鉴别器"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-176">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="fcc00-177">在 **Operator**   " **映射详细信息**" 窗口的 "运算符" 列中   ，从下拉列表中选择 "="。</span><span class="sxs-lookup"><span data-stu-id="fcc00-177">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="fcc00-178">在 " **值/属性** " 列中，键入 " **讲师**"。</span><span class="sxs-lookup"><span data-stu-id="fcc00-178">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="fcc00-179">最终结果应该如下所示：</span><span class="sxs-lookup"><span data-stu-id="fcc00-179">The end result should look like this:</span></span>

    ![映射详细信息](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="fcc00-181">对于 " **学生**"   实体类型，请重复这些步骤，但将条件设置为 "**学生**" 值。</span><span class="sxs-lookup"><span data-stu-id="fcc00-181">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="fcc00-182">*删除**鉴别**器属性的原因是，您不能多次映射一个表列。此列将用于条件映射，因此它也不能用于属性映射。如果条件使用 \*\*Is null*\*   或 **is Not null**比较，则可以使用这两种方法   。\*</span><span class="sxs-lookup"><span data-stu-id="fcc00-182">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="fcc00-183">“每个层次结构一个表”继承实现完毕。</span><span class="sxs-lookup"><span data-stu-id="fcc00-183">Table-per-hierarchy inheritance is now implemented.</span></span>

![最终 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="fcc00-185">使用模型</span><span class="sxs-lookup"><span data-stu-id="fcc00-185">Use the Model</span></span>

<span data-ttu-id="fcc00-186">打开 **Program.cs** 文件，其中定义了 **Main** 方法。</span><span class="sxs-lookup"><span data-stu-id="fcc00-186">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="fcc00-187">将以下代码粘贴到 **Main** 函数中。</span><span class="sxs-lookup"><span data-stu-id="fcc00-187">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="fcc00-188">此代码执行三个查询。</span><span class="sxs-lookup"><span data-stu-id="fcc00-188">The code executes three queries.</span></span> <span data-ttu-id="fcc00-189">第一个查询返回所有 **Person** 对象。</span><span class="sxs-lookup"><span data-stu-id="fcc00-189">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="fcc00-190">第二个查询使用 **OfType** 方法返回 **指导员** 对象。</span><span class="sxs-lookup"><span data-stu-id="fcc00-190">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="fcc00-191">第三个查询使用 **OfType** 方法返回 **学生** 对象。</span><span class="sxs-lookup"><span data-stu-id="fcc00-191">The third query uses the **OfType** method to return **Student** objects.</span></span>

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
