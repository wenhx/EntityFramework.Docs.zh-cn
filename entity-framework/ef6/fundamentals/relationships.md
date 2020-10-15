---
title: 关系、导航属性和外键-EF6
description: 实体框架6中的关系、导航属性和外键
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 956a24051c89d410e052ab02f073e693e1934a74
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062966"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="89329-103">关系、导航属性和外键</span><span class="sxs-lookup"><span data-stu-id="89329-103">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="89329-104">本文概述了实体框架如何管理实体之间的关系。</span><span class="sxs-lookup"><span data-stu-id="89329-104">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="89329-105">它还提供了一些有关如何映射和操作关系的指导。</span><span class="sxs-lookup"><span data-stu-id="89329-105">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="89329-106">EF 中的关系</span><span class="sxs-lookup"><span data-stu-id="89329-106">Relationships in EF</span></span>

<span data-ttu-id="89329-107">在关系数据库中，关系 (也称为关联) 在表之间通过外键定义。</span><span class="sxs-lookup"><span data-stu-id="89329-107">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="89329-108">外键 (FK) 是用于建立和加强两个表数据之间的链接的一列或多列。</span><span class="sxs-lookup"><span data-stu-id="89329-108">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="89329-109">通常有三种类型的关系：一对一、一对多和多对多的关系。</span><span class="sxs-lookup"><span data-stu-id="89329-109">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="89329-110">在一对多关系中，外键在表示关系的多个端的表上定义。</span><span class="sxs-lookup"><span data-stu-id="89329-110">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="89329-111">多对多关系涉及到定义第三个表 (称为联接表或联接表) ，其主键由两个相关表中的外键构成。</span><span class="sxs-lookup"><span data-stu-id="89329-111">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="89329-112">在一对一关系中，主键除了作为外键以外，对于任何一个表都没有单独的外键列。</span><span class="sxs-lookup"><span data-stu-id="89329-112">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="89329-113">下图显示了参与一对多关系的两个表。</span><span class="sxs-lookup"><span data-stu-id="89329-113">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="89329-114">**课程**表是依赖表，因为它包含将其链接到**部门**表的**DepartmentID**列。</span><span class="sxs-lookup"><span data-stu-id="89329-114">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![部门和课程表](~/ef6/media/database2.png)

<span data-ttu-id="89329-116">在实体框架中，可以通过关联或关系将实体与其他实体相关。</span><span class="sxs-lookup"><span data-stu-id="89329-116">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="89329-117">每个关系都包含两个端，分别描述了该关系中的两个实体 (一个、零或一或多个) 类型的实体类型和重数。</span><span class="sxs-lookup"><span data-stu-id="89329-117">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="89329-118">关系可由引用约束控制，该引用约束描述了关系中的哪端为 Principal Role 以及哪端为 Dependent Role。</span><span class="sxs-lookup"><span data-stu-id="89329-118">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="89329-119">导航属性提供了一种在两个实体类型之间导航关联的方法。</span><span class="sxs-lookup"><span data-stu-id="89329-119">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="89329-120">针对对象参与到其中的每个关系，各对象均可以具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="89329-120">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="89329-121">使用导航属性，您可以在两个方向上导航和管理关系，如果重数为1或零或一个) ，则返回引用对象 (; 如果重数为多) ，则返回 (集合。</span><span class="sxs-lookup"><span data-stu-id="89329-121">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="89329-122">您还可以选择使用单向导航，在这种情况下，只需在参与关系的一种类型上定义导航属性，而不是在两者上定义。</span><span class="sxs-lookup"><span data-stu-id="89329-122">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="89329-123">建议在模型中包含映射到数据库中的外键的属性。</span><span class="sxs-lookup"><span data-stu-id="89329-123">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="89329-124">加入了外键属性，您就可以通过修改依赖对象的外键值来创建或更改关系。</span><span class="sxs-lookup"><span data-stu-id="89329-124">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="89329-125">此类关联称为外键关联。</span><span class="sxs-lookup"><span data-stu-id="89329-125">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="89329-126">在处理断开连接的实体时，使用外键更为重要。</span><span class="sxs-lookup"><span data-stu-id="89329-126">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="89329-127">请注意，当使用1对1或1到0时，请注意。1关系，没有单独的外键列，主键属性充当外键，并且始终包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="89329-127">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="89329-128">如果模型中不包含外键列，则会将关联信息作为独立对象进行管理。</span><span class="sxs-lookup"><span data-stu-id="89329-128">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="89329-129">关系通过对象引用而不是外键属性进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="89329-129">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="89329-130">这种类型的关联称为 *独立关联*。</span><span class="sxs-lookup"><span data-stu-id="89329-130">This type of association is called an *independent association*.</span></span> <span data-ttu-id="89329-131">修改 *独立关联* 的最常见方法是修改为参与关联的每个实体生成的导航属性。</span><span class="sxs-lookup"><span data-stu-id="89329-131">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="89329-132">可以在您的模型中选择使用一种或两种类型的关联。</span><span class="sxs-lookup"><span data-stu-id="89329-132">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="89329-133">但是，如果您具有通过仅包含外键的联接表连接的一种纯多对多关系，则 EF 将使用独立的关联来管理这类多对多关系。</span><span class="sxs-lookup"><span data-stu-id="89329-133">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="89329-134">下图显示了使用 Entity Framework Designer 创建的概念模型。</span><span class="sxs-lookup"><span data-stu-id="89329-134">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="89329-135">该模型包含两个参与一对多关系的实体。</span><span class="sxs-lookup"><span data-stu-id="89329-135">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="89329-136">这两个实体都具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="89329-136">Both entities have navigation properties.</span></span> <span data-ttu-id="89329-137">**当然** 是依赖实体，并且定义了 **DepartmentID** 外键属性。</span><span class="sxs-lookup"><span data-stu-id="89329-137">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![具有导航属性的部门和课程表](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="89329-139">下面的代码片段显示了用 Code First 创建的同一模型。</span><span class="sxs-lookup"><span data-stu-id="89329-139">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="89329-140">配置或映射关系</span><span class="sxs-lookup"><span data-stu-id="89329-140">Configuring or mapping relationships</span></span>

<span data-ttu-id="89329-141">本页的其余部分介绍如何使用关系访问和操作数据。</span><span class="sxs-lookup"><span data-stu-id="89329-141">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="89329-142">有关设置模型中的关系的信息，请参阅以下页面。</span><span class="sxs-lookup"><span data-stu-id="89329-142">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="89329-143">若要在 Code First 中配置关系，请参阅 [数据批注](xref:ef6/modeling/code-first/data-annotations) 和 [熟知的 API-关系](xref:ef6/modeling/code-first/fluent/relationships)。</span><span class="sxs-lookup"><span data-stu-id="89329-143">To configure relationships in Code First, see [Data Annotations](xref:ef6/modeling/code-first/data-annotations) and [Fluent API – Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>
-   <span data-ttu-id="89329-144">若要使用 Entity Framework Designer 配置关系，请参阅 [与 EF 设计器](xref:ef6/modeling/designer/relationships)之间的关系。</span><span class="sxs-lookup"><span data-stu-id="89329-144">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](xref:ef6/modeling/designer/relationships).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="89329-145">创建和修改关系</span><span class="sxs-lookup"><span data-stu-id="89329-145">Creating and modifying relationships</span></span>

<span data-ttu-id="89329-146">在 *外键关联*中，当你更改关系时，状态为的依赖对象的状态将 `EntityState.Unchanged` 更改为 `EntityState.Modified` 。</span><span class="sxs-lookup"><span data-stu-id="89329-146">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="89329-147">在独立关系中，更改关系不会更新依赖对象的状态。</span><span class="sxs-lookup"><span data-stu-id="89329-147">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="89329-148">下面的示例演示如何使用外键属性和导航属性将相关对象关联起来。</span><span class="sxs-lookup"><span data-stu-id="89329-148">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="89329-149">使用外键关联，可以使用这两种方法更改、创建或修改关系。</span><span class="sxs-lookup"><span data-stu-id="89329-149">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="89329-150">使用独立关联，则不能使用外键属性。</span><span class="sxs-lookup"><span data-stu-id="89329-150">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="89329-151">将新值分配给外键属性，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="89329-151">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="89329-152">下面的代码通过将外键设置为 **null**来移除关系。</span><span class="sxs-lookup"><span data-stu-id="89329-152">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="89329-153">请注意，外键属性必须可以为 null。</span><span class="sxs-lookup"><span data-stu-id="89329-153">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="89329-154">如果引用处于 "已添加" 状态 (在此示例中，课程对象) ，则在调用 SaveChanges 之前，引用导航属性将不会与新对象的键值同步。</span><span class="sxs-lookup"><span data-stu-id="89329-154">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="89329-155">由于对象上下文在键值保存前不包含已添加对象的永久键，因此不发生同步。</span><span class="sxs-lookup"><span data-stu-id="89329-155">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="89329-156">如果在设置关系后，必须完全同步新对象，请使用以下方法之一。</span><span class="sxs-lookup"><span data-stu-id="89329-156">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="89329-157">通过将一个新对象分配给导航属性。</span><span class="sxs-lookup"><span data-stu-id="89329-157">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="89329-158">下面的代码创建一个课程与之间的关系 `department` 。</span><span class="sxs-lookup"><span data-stu-id="89329-158">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="89329-159">如果将对象附加到上下文，则 `course` 也会将添加到 `department.Courses` 集合中，对象上的相应外键属性 `course` 将设置为该部门的键属性值。</span><span class="sxs-lookup"><span data-stu-id="89329-159">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="89329-160">若要删除关系，请将导航属性设置为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="89329-160">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="89329-161">如果使用的是基于 .NET 4.0 的实体框架，则需要加载相关端，然后将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89329-161">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="89329-162">例如：</span><span class="sxs-lookup"><span data-stu-id="89329-162">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="89329-163">从基于 .NET 4.5 实体框架5.0 开始，可以在不加载相关端的情况下将关系设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89329-163">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="89329-164">还可以使用以下方法将当前值设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89329-164">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="89329-165">通过在实体集合中删除或添加对象。</span><span class="sxs-lookup"><span data-stu-id="89329-165">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="89329-166">例如，可以将类型的对象添加 `Course` 到 `department.Courses` 集合中。</span><span class="sxs-lookup"><span data-stu-id="89329-166">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="89329-167">此操作在特定的 **课程** 与特定的之间建立关系 `department` 。</span><span class="sxs-lookup"><span data-stu-id="89329-167">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="89329-168">如果将对象附加到上下文，则会将 **课程** 对象上的部门引用和外键属性设置为相应的 `department` 。</span><span class="sxs-lookup"><span data-stu-id="89329-168">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="89329-169">通过使用 `ChangeRelationshipState` 方法更改两个实体对象之间的指定关系的状态。</span><span class="sxs-lookup"><span data-stu-id="89329-169">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="89329-170">此方法最常用于使用 N 层应用程序和 *独立关联* ， (不能将其与外键关联) 一起使用。</span><span class="sxs-lookup"><span data-stu-id="89329-170">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="89329-171">此外，若要使用此方法，必须将下拉到 `ObjectContext` ，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="89329-171">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="89329-172">在下面的示例中，讲师和课程之间存在多对多的关系。</span><span class="sxs-lookup"><span data-stu-id="89329-172">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="89329-173">调用 `ChangeRelationshipState` 方法并传递 `EntityState.Added` 参数，可以 `SchoolContext` 了解在两个对象之间添加了关系：</span><span class="sxs-lookup"><span data-stu-id="89329-173">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="89329-174">请注意，如果您正在更新 (不只是添加) 关系，则必须在添加新关系之后删除旧关系：</span><span class="sxs-lookup"><span data-stu-id="89329-174">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="89329-175">同步外键和导航属性之间的更改</span><span class="sxs-lookup"><span data-stu-id="89329-175">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="89329-176">使用上述方法之一更改附加到上下文的对象的关系时，实体框架需要使外键、引用和集合保持同步。实体框架会自动管理此同步 (也称为具有代理的 POCO 实体的关系修补) 。</span><span class="sxs-lookup"><span data-stu-id="89329-176">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="89329-177">有关详细信息，请参阅使用 [代理](xref:ef6/fundamentals/proxies)。</span><span class="sxs-lookup"><span data-stu-id="89329-177">For more information, see [Working with Proxies](xref:ef6/fundamentals/proxies).</span></span>

<span data-ttu-id="89329-178">如果使用不带代理的 POCO 实体，则必须确保调用 **DetectChanges** 方法来同步上下文中的相关对象。</span><span class="sxs-lookup"><span data-stu-id="89329-178">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="89329-179">请注意，以下 Api 会自动触发 **DetectChanges** 调用。</span><span class="sxs-lookup"><span data-stu-id="89329-179">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="89329-180">对执行 LINQ 查询 `DbSet`</span><span class="sxs-lookup"><span data-stu-id="89329-180">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="89329-181">正在加载相关对象</span><span class="sxs-lookup"><span data-stu-id="89329-181">Loading related objects</span></span>

<span data-ttu-id="89329-182">在实体框架通常使用导航属性来加载通过定义的关联与返回的实体相关的实体。</span><span class="sxs-lookup"><span data-stu-id="89329-182">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="89329-183">有关详细信息，请参阅 [加载相关对象](xref:ef6/querying/related-data)。</span><span class="sxs-lookup"><span data-stu-id="89329-183">For more information, see [Loading Related Objects](xref:ef6/querying/related-data).</span></span>

> [!NOTE]
> <span data-ttu-id="89329-184">在外键关联中，加载依赖对象的相关端时，将会基于当前位于内存中的依赖对象的外键值加载相关对象：</span><span class="sxs-lookup"><span data-stu-id="89329-184">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="89329-185">在独立关联中，基于当前数据库中的外键值查询依赖对象的相关端。</span><span class="sxs-lookup"><span data-stu-id="89329-185">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="89329-186">但是，如果关系已修改，且依赖对象的引用属性指向对象上下文中加载的其他主体对象，实体框架将尝试创建关系，因为它是在客户端上定义的。</span><span class="sxs-lookup"><span data-stu-id="89329-186">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="89329-187">管理并发</span><span class="sxs-lookup"><span data-stu-id="89329-187">Managing concurrency</span></span>

<span data-ttu-id="89329-188">在外键和独立关联中，并发检查基于在模型中定义的实体键和其他实体属性。</span><span class="sxs-lookup"><span data-stu-id="89329-188">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="89329-189">使用 EF 设计器创建模型时，请将属性设置 `ConcurrencyMode` 为 " **固定** "，以指定应检查属性的并发性。</span><span class="sxs-lookup"><span data-stu-id="89329-189">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="89329-190">使用 Code First 定义模型时，请 `ConcurrencyCheck` 在要检查其并发性的属性上使用批注。</span><span class="sxs-lookup"><span data-stu-id="89329-190">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="89329-191">使用 Code First 时，还可以使用 `TimeStamp` 批注来指定应检查属性的并发性。</span><span class="sxs-lookup"><span data-stu-id="89329-191">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="89329-192">给定类中只能有一个时间戳属性。</span><span class="sxs-lookup"><span data-stu-id="89329-192">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="89329-193">Code First 将此属性映射到数据库中不可以为 null 的字段。</span><span class="sxs-lookup"><span data-stu-id="89329-193">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="89329-194">建议你始终在使用参与并发检查和解析的实体时使用外键关联。</span><span class="sxs-lookup"><span data-stu-id="89329-194">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="89329-195">有关详细信息，请参阅 [处理并发冲突](xref:ef6/saving/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="89329-195">For more information, see [Handling Concurrency Conflicts](xref:ef6/saving/concurrency).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="89329-196">使用重叠键</span><span class="sxs-lookup"><span data-stu-id="89329-196">Working with overlapping Keys</span></span>

<span data-ttu-id="89329-197">重叠键是指键中某些属性亦是实体中其他键的一部分的那些组合键。</span><span class="sxs-lookup"><span data-stu-id="89329-197">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="89329-198">在独立关联中不能包含重叠键。</span><span class="sxs-lookup"><span data-stu-id="89329-198">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="89329-199">若要更改包含重叠键的外键关联，我们建议您修改外键值而不要使用对象引用。</span><span class="sxs-lookup"><span data-stu-id="89329-199">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
