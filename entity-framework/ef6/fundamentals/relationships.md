---
title: 关系、 导航属性和外键的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 8292ae7af8d760240715854611d92ab340bf1ca7
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319187"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="7cd94-102">关系、 导航属性和外键</span><span class="sxs-lookup"><span data-stu-id="7cd94-102">Relationships, navigation properties and foreign keys</span></span>
<span data-ttu-id="7cd94-103">本主题概述了的实体框架如何管理实体之间的关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-103">This topic gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="7cd94-104">它还提供有关如何将映射和操作关系的一些指导。</span><span class="sxs-lookup"><span data-stu-id="7cd94-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="7cd94-105">在 EF 中的关系</span><span class="sxs-lookup"><span data-stu-id="7cd94-105">Relationships in EF</span></span>

<span data-ttu-id="7cd94-106">在关系数据库中，通过外键来定义表之间关系 （也称为关联）。</span><span class="sxs-lookup"><span data-stu-id="7cd94-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="7cd94-107">外键 (FK) 是列的组合，用于建立和加强两个表中的数据之间的链接。</span><span class="sxs-lookup"><span data-stu-id="7cd94-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="7cd94-108">通常有三种类型的关系： 一对一、 一个一对多和多对多。</span><span class="sxs-lookup"><span data-stu-id="7cd94-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="7cd94-109">在一个对多关系中，表示此关系的多端表上定义外键。</span><span class="sxs-lookup"><span data-stu-id="7cd94-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="7cd94-110">多对多关系包括定义其主键由两个相关表中的外键的第三个表 （称为联接或联接表）。</span><span class="sxs-lookup"><span data-stu-id="7cd94-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="7cd94-111">在一对一关系中，此外作为外键的主键的作用，并且没有任一表没有单独的外键列。</span><span class="sxs-lookup"><span data-stu-id="7cd94-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="7cd94-112">下图显示一个对多关系中两个参与的表。</span><span class="sxs-lookup"><span data-stu-id="7cd94-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="7cd94-113">**课程**表是相关的表，因为它包含**DepartmentID**其链接到的列**部门**表。</span><span class="sxs-lookup"><span data-stu-id="7cd94-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![部门和 Course 表](~/ef6/media/database2.png)

<span data-ttu-id="7cd94-115">在实体框架中，实体可以与其他实体通过关联或关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="7cd94-116">每个关系包含两个端点，用于描述实体类型和在该关系中的两个实体类型 （一个、 零或一个或许多） 的重数。</span><span class="sxs-lookup"><span data-stu-id="7cd94-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="7cd94-117">此关系可能会受引用约束，其中描述了关系中的哪一端为 principal role 以及哪一个是从属角色。</span><span class="sxs-lookup"><span data-stu-id="7cd94-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="7cd94-118">导航属性提供了一种导航两个实体类型之间的关联方法。</span><span class="sxs-lookup"><span data-stu-id="7cd94-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="7cd94-119">针对对象参与到其中的每个关系，各对象均可以具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="7cd94-120">导航属性，您可以导航和管理两个方向，返回引用对象中的关系 (在重数为两者中任何一个或零或一) 或集合 （如果重数为多个）。</span><span class="sxs-lookup"><span data-stu-id="7cd94-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="7cd94-121">您可能还选择单向导航，这种情况下定义的导航属性，在只有一个参与关系的类型和不在两者上。</span><span class="sxs-lookup"><span data-stu-id="7cd94-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="7cd94-122">建议将映射到数据库中的外键在模型中包含属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="7cd94-123">加入了外键属性，您就可以通过修改依赖对象的外键值来创建或更改关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="7cd94-124">此类关联称为外键关联。</span><span class="sxs-lookup"><span data-stu-id="7cd94-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="7cd94-125">使用断开连接的实体时，使用外键是甚至更加重要。</span><span class="sxs-lookup"><span data-stu-id="7cd94-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="7cd94-126">请注意，当使用 1 到 1 或 1 到 0...1 关系，没有任何单独的外键列，主键属性可用作外键，并始终包括在模型中。</span><span class="sxs-lookup"><span data-stu-id="7cd94-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="7cd94-127">在外键列未包括在模型中，作为独立对象进行管理的关联信息。</span><span class="sxs-lookup"><span data-stu-id="7cd94-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="7cd94-128">通过而不是外键属性的对象引用跟踪的关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="7cd94-129">此类型的关联称为*独立关联*。</span><span class="sxs-lookup"><span data-stu-id="7cd94-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="7cd94-130">若要修改的最常见方式*独立关联*是修改为参与到关联中每个实体生成导航属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="7cd94-131">可以在您的模型中选择使用一种或两种类型的关联。</span><span class="sxs-lookup"><span data-stu-id="7cd94-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="7cd94-132">但是，如果必须仅包含外键的联接表已连接的纯多对多关系，EF 将使用独立关联管理这种多对多关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="7cd94-133">下图显示了使用实体框架设计器创建的概念模型。</span><span class="sxs-lookup"><span data-stu-id="7cd94-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="7cd94-134">该模型包含两个参与对多关系的实体。</span><span class="sxs-lookup"><span data-stu-id="7cd94-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="7cd94-135">这两个实体都具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-135">Both entities have navigation properties.</span></span> <span data-ttu-id="7cd94-136">**课程**，且是 depend 实体**DepartmentID**定义的外键属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-136">**Course** is the depend entity and has the **DepartmentID** foreign key property defined.</span></span>

![具有导航属性的部门和 Course 表](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="7cd94-138">以下代码片段显示使用 Code First 创建的相同模型。</span><span class="sxs-lookup"><span data-stu-id="7cd94-138">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="7cd94-139">配置或映射关系</span><span class="sxs-lookup"><span data-stu-id="7cd94-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="7cd94-140">此页的其余部分介绍了如何访问和处理使用关系数据。</span><span class="sxs-lookup"><span data-stu-id="7cd94-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="7cd94-141">设置您的模型中的关系的信息，请参阅以下页面。</span><span class="sxs-lookup"><span data-stu-id="7cd94-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="7cd94-142">若要配置关系中第一个代码，请参阅[数据批注](~/ef6/modeling/code-first/data-annotations.md)并[Fluent API-关系](~/ef6/modeling/code-first/fluent/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="7cd94-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="7cd94-143">若要配置使用实体框架设计器的关系，请参阅[与 EF 设计器之间的关系](~/ef6/modeling/designer/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="7cd94-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="7cd94-144">创建和修改关系</span><span class="sxs-lookup"><span data-stu-id="7cd94-144">Creating and modifying relationships</span></span>

<span data-ttu-id="7cd94-145">在中*外键关联*，当更改关系后，具有的依赖对象的状态`EntityState.Unchanged`的状态更改为`EntityState.Modified`。</span><span class="sxs-lookup"><span data-stu-id="7cd94-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="7cd94-146">在独立关系中，更改该关系不会更新依赖对象的状态。</span><span class="sxs-lookup"><span data-stu-id="7cd94-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="7cd94-147">以下示例演示如何使用外键属性和导航属性关联相关的对象。</span><span class="sxs-lookup"><span data-stu-id="7cd94-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="7cd94-148">通过外键关联，可以使用任一方法来更改、 创建或修改关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="7cd94-149">使用独立关联，则不能使用外键属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="7cd94-150">通过将新值分配给外键属性，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="7cd94-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="7cd94-151">下面的代码中删除通过外键设置为一种关系**null**。</span><span class="sxs-lookup"><span data-stu-id="7cd94-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="7cd94-152">请注意外, 键属性必须是可以为 null。</span><span class="sxs-lookup"><span data-stu-id="7cd94-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="7cd94-153">如果引用处于已添加状态 （在此示例中，课程对象），引用导航属性将不同步新的对象的键值，直到调用 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="7cd94-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="7cd94-154">由于对象上下文在键值保存前不包含已添加对象的永久键，因此不发生同步。</span><span class="sxs-lookup"><span data-stu-id="7cd94-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="7cd94-155">如果你必须拥有新对象进行完全同步设置关系一样，使用以下 methods.\* 之一</span><span class="sxs-lookup"><span data-stu-id="7cd94-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="7cd94-156">通过将一个新对象分配给导航属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="7cd94-157">以下代码将创建一门课程之间的关系和`department`。</span><span class="sxs-lookup"><span data-stu-id="7cd94-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="7cd94-158">如果这些对象附加到上下文中，`course`还将添加到`department.Courses`集合，并且对应的外键属性上`course`对象设置为部门的键属性值。</span><span class="sxs-lookup"><span data-stu-id="7cd94-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="7cd94-159">若要删除此关系，请将导航属性设置为`null`。</span><span class="sxs-lookup"><span data-stu-id="7cd94-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="7cd94-160">如果您正在使用实体框架，基于.NET 4.0，相关的端需要在加载之前将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="7cd94-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="7cd94-161">例如：</span><span class="sxs-lookup"><span data-stu-id="7cd94-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="7cd94-162">从实体框架 5.0，基于.NET 4.5，可以设置为 null 关系而不加载相关的端。</span><span class="sxs-lookup"><span data-stu-id="7cd94-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="7cd94-163">此外可以设置的当前值为 null，使用以下方法。</span><span class="sxs-lookup"><span data-stu-id="7cd94-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="7cd94-164">通过在实体集合中删除或添加对象。</span><span class="sxs-lookup"><span data-stu-id="7cd94-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="7cd94-165">例如，可以添加类型的对象`Course`到`department.Courses`集合。</span><span class="sxs-lookup"><span data-stu-id="7cd94-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="7cd94-166">此操作将创建特定之间的关系**课程**和特定`department`。</span><span class="sxs-lookup"><span data-stu-id="7cd94-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="7cd94-167">如果对象附加到上下文、 部门引用和外键属性上**课程**对象将被设置为相应`department`。</span><span class="sxs-lookup"><span data-stu-id="7cd94-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="7cd94-168">通过使用`ChangeRelationshipState`方法以更改指定两个实体对象之间关系的状态。</span><span class="sxs-lookup"><span data-stu-id="7cd94-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="7cd94-169">使用 N 层应用程序时最常使用此方法和一个*独立关联*（与外键关联不能使用）。</span><span class="sxs-lookup"><span data-stu-id="7cd94-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="7cd94-170">此外，若要使用此方法必须删除直至`ObjectContext`，下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="7cd94-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="7cd94-171">以下示例中，在没有讲师和课程之间的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="7cd94-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="7cd94-172">调用`ChangeRelationshipState`方法并传入`EntityState.Added`参数，允许`SchoolContext`知道是否两个对象之间添加关系：</span><span class="sxs-lookup"><span data-stu-id="7cd94-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="7cd94-173">请注意，如果你要更新 （而不仅仅添加） 关系，您必须添加新后删除旧的关系：</span><span class="sxs-lookup"><span data-stu-id="7cd94-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="7cd94-174">同步之间的外键和导航属性的更改</span><span class="sxs-lookup"><span data-stu-id="7cd94-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="7cd94-175">使用上面所述的方法之一附加到上下文的对象的关系更改时，实体框架需要使外键、 引用以及集合保持同步。实体框架会自动管理此同步 （也称为关系修复-最多） 与代理的 POCO 实体。</span><span class="sxs-lookup"><span data-stu-id="7cd94-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="7cd94-176">有关详细信息，请参阅[使用代理](~/ef6/fundamentals/proxies.md)。</span><span class="sxs-lookup"><span data-stu-id="7cd94-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="7cd94-177">如果要使用不含代理的 POCO 实体，必须确保**DetectChanges**调用方法来同步上下文中的相关的对象。</span><span class="sxs-lookup"><span data-stu-id="7cd94-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="7cd94-178">请注意，以下 Api 会自动触发**DetectChanges**调用。</span><span class="sxs-lookup"><span data-stu-id="7cd94-178">Note, that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="7cd94-179">对执行 LINQ 查询 `DbSet`</span><span class="sxs-lookup"><span data-stu-id="7cd94-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="7cd94-180">加载相关对象</span><span class="sxs-lookup"><span data-stu-id="7cd94-180">Loading related objects</span></span>

<span data-ttu-id="7cd94-181">实体框架中您通常使用导航属性加载通过定义的关联返回的实体相关的实体。</span><span class="sxs-lookup"><span data-stu-id="7cd94-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="7cd94-182">有关详细信息，请参阅[加载相关对象](~/ef6/querying/related-data.md)。</span><span class="sxs-lookup"><span data-stu-id="7cd94-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="7cd94-183">在外键关联中，加载依赖对象的相关端时，将会基于当前位于内存中的依赖对象的外键值加载相关对象：</span><span class="sxs-lookup"><span data-stu-id="7cd94-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c=>c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="7cd94-184">在独立关联中，基于当前数据库中的外键值查询依赖对象的相关端。</span><span class="sxs-lookup"><span data-stu-id="7cd94-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="7cd94-185">但是，如果关系已修改，并对它加载到对象上下文，实体框架中的不同主体对象的依赖对象点上的引用属性将尝试创建的关系为它定义在客户端上。</span><span class="sxs-lookup"><span data-stu-id="7cd94-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="7cd94-186">管理并发</span><span class="sxs-lookup"><span data-stu-id="7cd94-186">Managing concurrency</span></span>

<span data-ttu-id="7cd94-187">在外键和独立关联中，并发检查基于实体键和其他模型中定义的实体属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="7cd94-188">当使用 EF 设计器创建模型时，设置`ConcurrencyMode`属性为**修复**以指定应针对并发检查属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="7cd94-189">在使用 Code First 定义的模型，使用`ConcurrencyCheck`上想要检查并发的属性批注。</span><span class="sxs-lookup"><span data-stu-id="7cd94-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="7cd94-190">使用 Code First 时还可以使用`TimeStamp`批注来指定应针对并发检查属性。</span><span class="sxs-lookup"><span data-stu-id="7cd94-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="7cd94-191">您可以只有一个时间戳属性中给定的类。</span><span class="sxs-lookup"><span data-stu-id="7cd94-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="7cd94-192">代码首先将此属性映射到数据库中的不可以为 null 的字段。</span><span class="sxs-lookup"><span data-stu-id="7cd94-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="7cd94-193">我们建议使用参与并发检查和解析实体时始终使用外键关联。</span><span class="sxs-lookup"><span data-stu-id="7cd94-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="7cd94-194">有关详细信息，请参阅[处理并发冲突](~/ef6/saving/concurrency.md)。</span><span class="sxs-lookup"><span data-stu-id="7cd94-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="7cd94-195">使用重叠键</span><span class="sxs-lookup"><span data-stu-id="7cd94-195">Working with overlapping Keys</span></span>

<span data-ttu-id="7cd94-196">重叠键是指键中某些属性亦是实体中其他键的一部分的那些组合键。</span><span class="sxs-lookup"><span data-stu-id="7cd94-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="7cd94-197">在独立关联中不能包含重叠键。</span><span class="sxs-lookup"><span data-stu-id="7cd94-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="7cd94-198">若要更改包含重叠键的外键关联，我们建议您修改外键值而不要使用对象引用。</span><span class="sxs-lookup"><span data-stu-id="7cd94-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
