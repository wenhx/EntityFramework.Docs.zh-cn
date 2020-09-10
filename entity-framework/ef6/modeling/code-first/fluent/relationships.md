---
title: 熟知的 API 关系-EF6
description: 熟知 API-实体框架6中的关系
author: divega
ms.date: 10/23/2016
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 8cc56f7341df6da7f60f649308ea7042ef23b537
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617002"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="e4651-103">熟知 API-关系</span><span class="sxs-lookup"><span data-stu-id="e4651-103">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="e4651-104">本页提供有关使用 Fluent API 设置 Code First 模型中的关系的信息。</span><span class="sxs-lookup"><span data-stu-id="e4651-104">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="e4651-105">有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅 [关系 & 导航属性](xref:ef6/fundamentals/relationships)。</span><span class="sxs-lookup"><span data-stu-id="e4651-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>  

<span data-ttu-id="e4651-106">使用 Code First 时，可通过定义域 CLR 类定义模型。</span><span class="sxs-lookup"><span data-stu-id="e4651-106">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="e4651-107">默认情况下，实体框架使用 Code First 约定将类映射到数据库架构。</span><span class="sxs-lookup"><span data-stu-id="e4651-107">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="e4651-108">如果你使用 Code First 命名约定，则在大多数情况下，你可以依赖于 Code First 根据你在类上定义的外键和导航属性来设置表之间的关系。</span><span class="sxs-lookup"><span data-stu-id="e4651-108">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="e4651-109">如果在定义类时不遵循约定，或者若要更改约定的工作方式，可以使用 Fluent API 或数据批注来配置类，以便 Code First 可以映射表之间的关系。</span><span class="sxs-lookup"><span data-stu-id="e4651-109">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="e4651-110">简介</span><span class="sxs-lookup"><span data-stu-id="e4651-110">Introduction</span></span>  

<span data-ttu-id="e4651-111">在配置与 Fluent API 的关系时，请从 EntityTypeConfiguration 实例开始，然后使用 HasRequired、HasOptional 或 HasMany 方法来指定此实体参与的关系的类型。</span><span class="sxs-lookup"><span data-stu-id="e4651-111">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="e4651-112">HasRequired 和 HasOptional 方法采用表示引用导航属性的 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="e4651-112">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="e4651-113">HasMany 方法采用表示集合导航属性的 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="e4651-113">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="e4651-114">然后，可以通过使用 WithRequired、WithOptional 和 WithMany 方法配置反向导航属性。</span><span class="sxs-lookup"><span data-stu-id="e4651-114">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="e4651-115">这些方法具有不带参数的重载，可用于通过单向导航指定基数。</span><span class="sxs-lookup"><span data-stu-id="e4651-115">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="e4651-116">然后，可以使用 HasForeignKey 方法配置外键属性。</span><span class="sxs-lookup"><span data-stu-id="e4651-116">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="e4651-117">此方法采用一个表示要用作外键的属性的 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="e4651-117">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="e4651-118">将必需的可选关系配置 (一对零或一) </span><span class="sxs-lookup"><span data-stu-id="e4651-118">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="e4651-119">下面的示例将配置一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="e4651-119">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="e4651-120">OfficeAssignment 具有 InstructorID 属性，该属性是主键和外键，因为属性的名称不遵循该约定。 HasKey 方法用于配置主键。</span><span class="sxs-lookup"><span data-stu-id="e4651-120">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="e4651-121">配置一种关系，在这种情况下，这两个端都需要 (一对一) </span><span class="sxs-lookup"><span data-stu-id="e4651-121">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="e4651-122">在大多数情况下实体框架可以推断哪个类型是依赖项并且是关系中的主体。</span><span class="sxs-lookup"><span data-stu-id="e4651-122">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="e4651-123">但是，如果需要关系的两端，或者两个两侧都是可选的，则实体框架无法识别依赖项和主体。</span><span class="sxs-lookup"><span data-stu-id="e4651-123">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="e4651-124">如果关系的两端都是必需的，请在 HasRequired 方法后面使用 WithRequiredPrincipal 或 WithRequiredDependent。</span><span class="sxs-lookup"><span data-stu-id="e4651-124">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="e4651-125">如果关系两端都是可选的，请在 HasOptional 方法后面使用 WithOptionalPrincipal 或 WithOptionalDependent。</span><span class="sxs-lookup"><span data-stu-id="e4651-125">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="e4651-126">配置多对多关系</span><span class="sxs-lookup"><span data-stu-id="e4651-126">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="e4651-127">下面的代码配置课程类型和指导员类型之间的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="e4651-127">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="e4651-128">在下面的示例中，使用了默认 Code First 约定来创建联接表。</span><span class="sxs-lookup"><span data-stu-id="e4651-128">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="e4651-129">因此，Courseinstructor.courseid 表是使用 Course_CourseID 和 Instructor_InstructorID 列创建的。</span><span class="sxs-lookup"><span data-stu-id="e4651-129">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="e4651-130">如果要使用 Map 方法指定联接表名称和表中列的名称，则需要执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="e4651-130">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="e4651-131">下面的代码生成包含 CourseID 和 InstructorID 列的 Courseinstructor.courseid 表。</span><span class="sxs-lookup"><span data-stu-id="e4651-131">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="e4651-132">使用一个导航属性配置关系</span><span class="sxs-lookup"><span data-stu-id="e4651-132">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="e4651-133">一种单向 (也称为单向) 关系，只是在一个关系端上定义导航属性，而不是在两者上定义。</span><span class="sxs-lookup"><span data-stu-id="e4651-133">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="e4651-134">按照约定，Code First 始终将单向关系解释为一对多。</span><span class="sxs-lookup"><span data-stu-id="e4651-134">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="e4651-135">例如，如果你想要在讲师与 OfficeAssignment 之间进行一对一关系，其中仅有指导员类型的导航属性，则需要使用 Fluent API 来配置此关系。</span><span class="sxs-lookup"><span data-stu-id="e4651-135">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="e4651-136">启用级联删除</span><span class="sxs-lookup"><span data-stu-id="e4651-136">Enabling Cascade Delete</span></span>  

<span data-ttu-id="e4651-137">您可以通过使用 WillCascadeOnDelete 方法为关系配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e4651-137">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="e4651-138">如果从属实体上的外键不可为 null，则 Code First 在关系上设置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e4651-138">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="e4651-139">如果从属实体上的外键可为 null，则 Code First 不会对关系设置级联删除，并且在删除主体时，外键将设置为 null。</span><span class="sxs-lookup"><span data-stu-id="e4651-139">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="e4651-140">您可以使用以下方法删除这些级联删除约定：</span><span class="sxs-lookup"><span data-stu-id="e4651-140">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="e4651-141">modelBuilder \<OneToManyCascadeDeleteConvention\> ( # A1</span><span class="sxs-lookup"><span data-stu-id="e4651-141">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="e4651-142">modelBuilder \<ManyToManyCascadeDeleteConvention\> ( # A1</span><span class="sxs-lookup"><span data-stu-id="e4651-142">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="e4651-143">下面的代码将关系配置为 "必需"，然后禁用级联删除。</span><span class="sxs-lookup"><span data-stu-id="e4651-143">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="e4651-144">配置复合外键</span><span class="sxs-lookup"><span data-stu-id="e4651-144">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="e4651-145">如果部门类型上的主键由 DepartmentID 和 Name 属性组成，则可以为该部门配置主键，并为课程类型配置外键，如下所示：</span><span class="sxs-lookup"><span data-stu-id="e4651-145">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="e4651-146">重命名未在模型中定义的外键</span><span class="sxs-lookup"><span data-stu-id="e4651-146">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="e4651-147">如果选择不在 CLR 类型上定义外键，但要指定它应在数据库中具有的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e4651-147">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="e4651-148">配置不遵循 Code First 约定的外键名称</span><span class="sxs-lookup"><span data-stu-id="e4651-148">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="e4651-149">如果课程类的外键属性称为 SomeDepartmentID 而不是 DepartmentID，则需要执行以下操作，以指定要 SomeDepartmentID 为外键：</span><span class="sxs-lookup"><span data-stu-id="e4651-149">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="e4651-150">示例中使用的模型</span><span class="sxs-lookup"><span data-stu-id="e4651-150">Model Used in Samples</span></span>  

<span data-ttu-id="e4651-151">以下 Code First 模型用于此页上的示例。</span><span class="sxs-lookup"><span data-stu-id="e4651-151">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
