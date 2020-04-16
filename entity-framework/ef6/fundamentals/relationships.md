---
title: 关系、导航属性和外键 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434321"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="b86c5-102">关系、导航属性和外键</span><span class="sxs-lookup"><span data-stu-id="b86c5-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="b86c5-103">本文概述了实体框架如何管理实体之间的关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="b86c5-104">它还就如何映射和操作关系提供了一些指导。</span><span class="sxs-lookup"><span data-stu-id="b86c5-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="b86c5-105">EF 中的关系</span><span class="sxs-lookup"><span data-stu-id="b86c5-105">Relationships in EF</span></span>

<span data-ttu-id="b86c5-106">在关系数据库中，表之间的关系（也称为关联）通过外键定义。</span><span class="sxs-lookup"><span data-stu-id="b86c5-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="b86c5-107">外键 (FK) 是用于建立和加强两个表数据之间的链接的一列或多列。</span><span class="sxs-lookup"><span data-stu-id="b86c5-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="b86c5-108">通常有三种类型的关系：一对一、一对多和多对多。</span><span class="sxs-lookup"><span data-stu-id="b86c5-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="b86c5-109">在一对多关系中，外键在表上定义，该表表示关系的多端。</span><span class="sxs-lookup"><span data-stu-id="b86c5-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="b86c5-110">多对多关系涉及定义第三个表（称为连接表或联接表），其主键由两个相关表中的外键组成。</span><span class="sxs-lookup"><span data-stu-id="b86c5-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="b86c5-111">在一对一关系中，主键另外充当外键，并且两个表没有单独的外键列。</span><span class="sxs-lookup"><span data-stu-id="b86c5-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="b86c5-112">下图显示了两个参与一对多关系的表。</span><span class="sxs-lookup"><span data-stu-id="b86c5-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="b86c5-113">**课程**表是从属表，因为它包含将其链接到 **"部门"** 表的 **"部门 ID"** 列。</span><span class="sxs-lookup"><span data-stu-id="b86c5-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![部门和课程表](~/ef6/media/database2.png)

<span data-ttu-id="b86c5-115">在实体框架中，实体可以通过关联或关系与其他实体关联。</span><span class="sxs-lookup"><span data-stu-id="b86c5-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="b86c5-116">每个关系包含两个端，用于描述该关系中两个实体的实体类型和类型的多重性（一个、零或一个或多个）。</span><span class="sxs-lookup"><span data-stu-id="b86c5-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="b86c5-117">关系可由引用约束控制，该引用约束描述了关系中的哪端为 Principal Role 以及哪端为 Dependent Role。</span><span class="sxs-lookup"><span data-stu-id="b86c5-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="b86c5-118">导航属性提供了一种导航两种实体类型之间的关联的方法。</span><span class="sxs-lookup"><span data-stu-id="b86c5-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="b86c5-119">针对对象参与到其中的每个关系，各对象均可以具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="b86c5-120">导航属性允许您在两个方向上导航和管理关系，返回引用对象（如果多重性为 1 或为零或 1）或集合（如果多重性多）。</span><span class="sxs-lookup"><span data-stu-id="b86c5-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="b86c5-121">您还可以选择单向导航，在这种情况下，您只对参与关系的一种类型定义导航属性，而不是同时在两种类型上定义导航属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="b86c5-122">建议在模型中包括映射到数据库中的外键的属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="b86c5-123">加入了外键属性，您就可以通过修改依赖对象的外键值来创建或更改关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="b86c5-124">此类关联称为外键关联。</span><span class="sxs-lookup"><span data-stu-id="b86c5-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="b86c5-125">使用断开连接的实体时，使用外键更为重要。</span><span class="sxs-lookup"><span data-stu-id="b86c5-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="b86c5-126">请注意，使用 1 比 1 或 1 比 0 时。1 个关系，没有单独的外键列，主键属性充当外键，始终包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="b86c5-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="b86c5-127">当模型中不包括外键列时，关联信息将作为独立对象进行管理。</span><span class="sxs-lookup"><span data-stu-id="b86c5-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="b86c5-128">关系通过对象引用而不是外键属性进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b86c5-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="b86c5-129">这种类型的关联称为*独立关联*。</span><span class="sxs-lookup"><span data-stu-id="b86c5-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="b86c5-130">修改*独立关联的*最常见方法是修改为参与关联的每个实体生成的导航属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="b86c5-131">可以在您的模型中选择使用一种或两种类型的关联。</span><span class="sxs-lookup"><span data-stu-id="b86c5-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="b86c5-132">但是，如果您有一个纯多对多关系，该关系由仅包含外键的联接表连接，则 EF 将使用独立关联来管理此类多对多关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="b86c5-133">下图显示了使用实体框架设计器创建的概念模型。</span><span class="sxs-lookup"><span data-stu-id="b86c5-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="b86c5-134">该模型包含两个参与一对多关系的实体。</span><span class="sxs-lookup"><span data-stu-id="b86c5-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="b86c5-135">两个实体都具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-135">Both entities have navigation properties.</span></span> <span data-ttu-id="b86c5-136">**课程**是从属实体，并且定义了**DepartmentID**外键属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![具有导航属性的部门和课程表](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="b86c5-138">以下代码段显示使用代码优先创建的模型。</span><span class="sxs-lookup"><span data-stu-id="b86c5-138">The following code snippet shows the same model that was created with Code First.</span></span>

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="b86c5-139">配置或映射关系</span><span class="sxs-lookup"><span data-stu-id="b86c5-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="b86c5-140">本页的其余部分介绍如何使用关系访问和操作数据。</span><span class="sxs-lookup"><span data-stu-id="b86c5-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="b86c5-141">有关在模型中设置关系的信息，请参阅以下页面。</span><span class="sxs-lookup"><span data-stu-id="b86c5-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="b86c5-142">要在代码优先中配置关系，请参阅[数据注释](~/ef6/modeling/code-first/data-annotations.md)和[Fluent API = 关系](~/ef6/modeling/code-first/fluent/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="b86c5-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="b86c5-143">要使用实体框架设计器配置关系，请参阅[与 EF 设计器的关系](~/ef6/modeling/designer/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="b86c5-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="b86c5-144">创建和修改关系</span><span class="sxs-lookup"><span data-stu-id="b86c5-144">Creating and modifying relationships</span></span>

<span data-ttu-id="b86c5-145">在*外键关联*中，当您更改关系时，具有`EntityState.Unchanged`状态的从属对象的状态将更改为 。 `EntityState.Modified`</span><span class="sxs-lookup"><span data-stu-id="b86c5-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="b86c5-146">在独立关系中，更改关系不会更新从属对象的状态。</span><span class="sxs-lookup"><span data-stu-id="b86c5-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="b86c5-147">以下示例演示如何使用外键属性和导航属性来关联相关对象。</span><span class="sxs-lookup"><span data-stu-id="b86c5-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="b86c5-148">使用外键关联，可以使用任一方法更改、创建或修改关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="b86c5-149">使用独立关联，则不能使用外键属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="b86c5-150">通过将新值分配给外键属性，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="b86c5-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="b86c5-151">以下代码通过将外键设置为**null**来删除关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="b86c5-152">请注意，外键属性必须为空。</span><span class="sxs-lookup"><span data-stu-id="b86c5-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="b86c5-153">如果引用处于添加状态（在此示例中为课程对象），则在调用 SaveChanges 之前，引用导航属性不会与新对象的键值同步。</span><span class="sxs-lookup"><span data-stu-id="b86c5-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="b86c5-154">由于对象上下文在键值保存前不包含已添加对象的永久键，因此不发生同步。</span><span class="sxs-lookup"><span data-stu-id="b86c5-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="b86c5-155">如果在设置关系后立即必须完全同步新对象，请使用以下方法之一。</span><span class="sxs-lookup"><span data-stu-id="b86c5-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="b86c5-156">通过将一个新对象分配给导航属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="b86c5-157">以下代码在 课程和 之间`department`创建关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="b86c5-158">如果对象附加到上下文，`course`则 还会将 添加到`department.Courses`集合中，并且`course`对象上的相应外键属性将设置为部门的关键属性值。</span><span class="sxs-lookup"><span data-stu-id="b86c5-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="b86c5-159">要删除关系，请将导航属性设置为`null`。</span><span class="sxs-lookup"><span data-stu-id="b86c5-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="b86c5-160">如果使用基于 .NET 4.0 的实体框架，则需要在将其设置为 null 之前加载相关端。</span><span class="sxs-lookup"><span data-stu-id="b86c5-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="b86c5-161">例如：</span><span class="sxs-lookup"><span data-stu-id="b86c5-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="b86c5-162">从基于 .NET 4.5 的实体框架 5.0 开始，可以将关系设置为 null，而无需加载相关端。</span><span class="sxs-lookup"><span data-stu-id="b86c5-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="b86c5-163">您还可以使用以下方法将当前值设置为 null。</span><span class="sxs-lookup"><span data-stu-id="b86c5-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="b86c5-164">通过在实体集合中删除或添加对象。</span><span class="sxs-lookup"><span data-stu-id="b86c5-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="b86c5-165">例如，可以将类型`Course`对象添加到集合中。 `department.Courses`</span><span class="sxs-lookup"><span data-stu-id="b86c5-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="b86c5-166">此操作在特定**课程**和特定`department`之间创建关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="b86c5-167">如果对象附加到上下文，**则课程**对象的部门引用和外键属性将设置为相应的`department`。</span><span class="sxs-lookup"><span data-stu-id="b86c5-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="b86c5-168">通过使用 方法`ChangeRelationshipState`更改两个实体对象之间指定关系的状态。</span><span class="sxs-lookup"><span data-stu-id="b86c5-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="b86c5-169">此方法最常用于使用 N-Tier 应用程序和独立*关联*（不能与外键关联一起使用）。</span><span class="sxs-lookup"><span data-stu-id="b86c5-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="b86c5-170">此外，要使用此方法，必须下拉至`ObjectContext`，如下例所示。</span><span class="sxs-lookup"><span data-stu-id="b86c5-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="b86c5-171">在下面的示例中，教师和课程之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="b86c5-172">调用`ChangeRelationshipState`方法并传递`EntityState.Added`参数，`SchoolContext`可以知道两个对象之间已添加了关系：</span><span class="sxs-lookup"><span data-stu-id="b86c5-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="b86c5-173">请注意，如果要更新（而不仅仅是添加）关系，则必须在添加新关系后删除旧关系：</span><span class="sxs-lookup"><span data-stu-id="b86c5-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="b86c5-174">同步外键和导航属性之间的更改</span><span class="sxs-lookup"><span data-stu-id="b86c5-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="b86c5-175">使用上述方法之一更改附加到上下文的对象之间的关系时，实体框架需要保持外键、引用和集合同步。实体框架自动管理具有代理的 POCO 实体的此同步（也称为关系修复）。</span><span class="sxs-lookup"><span data-stu-id="b86c5-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="b86c5-176">有关详细信息，请参阅[使用代理](~/ef6/fundamentals/proxies.md)。</span><span class="sxs-lookup"><span data-stu-id="b86c5-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="b86c5-177">如果使用没有代理的 POCO 实体，则必须确保调用**检测更改**方法以同步上下文中的相关对象。</span><span class="sxs-lookup"><span data-stu-id="b86c5-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="b86c5-178">请注意，以下 API 会自动触发**检测更改**调用。</span><span class="sxs-lookup"><span data-stu-id="b86c5-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   <span data-ttu-id="b86c5-179">针对 执行 LINQ 查询`DbSet`</span><span class="sxs-lookup"><span data-stu-id="b86c5-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="b86c5-180">加载相关对象</span><span class="sxs-lookup"><span data-stu-id="b86c5-180">Loading related objects</span></span>

<span data-ttu-id="b86c5-181">在实体框架中，通常使用导航属性加载由定义的关联与返回的实体相关的实体。</span><span class="sxs-lookup"><span data-stu-id="b86c5-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="b86c5-182">有关详细信息，请参阅[加载相关对象](~/ef6/querying/related-data.md)。</span><span class="sxs-lookup"><span data-stu-id="b86c5-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="b86c5-183">在外键关联中，加载依赖对象的相关端时，将会基于当前位于内存中的依赖对象的外键值加载相关对象：</span><span class="sxs-lookup"><span data-stu-id="b86c5-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="b86c5-184">在独立关联中，基于当前数据库中的外键值查询依赖对象的相关端。</span><span class="sxs-lookup"><span data-stu-id="b86c5-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="b86c5-185">但是，如果修改了关系，并且从属对象的引用属性指向在对象上下文中加载的不同主体对象，实体框架将尝试创建在客户端上定义的关系。</span><span class="sxs-lookup"><span data-stu-id="b86c5-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="b86c5-186">管理并发</span><span class="sxs-lookup"><span data-stu-id="b86c5-186">Managing concurrency</span></span>

<span data-ttu-id="b86c5-187">在外键和独立关联中，并发检查基于模型中定义的实体密钥和其他实体属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="b86c5-188">使用 EF 设计器创建模型时，将`ConcurrencyMode`属性设置为**固定**以指定应检查属性为并发性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="b86c5-189">使用 Code First 定义模型时，对`ConcurrencyCheck`要检查为并发的属性使用注释。</span><span class="sxs-lookup"><span data-stu-id="b86c5-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="b86c5-190">使用 Code First 时，`TimeStamp`还可以使用注释指定应检查属性为并发性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="b86c5-191">给定类中只能有一个时间戳属性。</span><span class="sxs-lookup"><span data-stu-id="b86c5-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="b86c5-192">代码 First 将此属性映射到数据库中的非空字段。</span><span class="sxs-lookup"><span data-stu-id="b86c5-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="b86c5-193">我们建议您在使用参与并发检查和解析的实体时始终使用外键关联。</span><span class="sxs-lookup"><span data-stu-id="b86c5-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="b86c5-194">有关详细信息，请参阅[处理并发冲突](~/ef6/saving/concurrency.md)。</span><span class="sxs-lookup"><span data-stu-id="b86c5-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="b86c5-195">使用重叠键</span><span class="sxs-lookup"><span data-stu-id="b86c5-195">Working with overlapping Keys</span></span>

<span data-ttu-id="b86c5-196">重叠键是指键中某些属性亦是实体中其他键的一部分的那些组合键。</span><span class="sxs-lookup"><span data-stu-id="b86c5-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="b86c5-197">在独立关联中不能包含重叠键。</span><span class="sxs-lookup"><span data-stu-id="b86c5-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="b86c5-198">若要更改包含重叠键的外键关联，我们建议您修改外键值而不要使用对象引用。</span><span class="sxs-lookup"><span data-stu-id="b86c5-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
