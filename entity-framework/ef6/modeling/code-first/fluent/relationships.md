---
title: 熟知的 API 关系-EF6
description: 熟知 API-实体框架6中的关系
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 7c62dfd788afda41f4c940836152b9114aa545e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065155"
---
# <a name="fluent-api---relationships"></a>熟知 API-关系
> [!NOTE]
> 本页提供有关使用 Fluent API 设置 Code First 模型中的关系的信息。 有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅 [关系 & 导航属性](xref:ef6/fundamentals/relationships)。  

使用 Code First 时，可通过定义域 CLR 类定义模型。 默认情况下，实体框架使用 Code First 约定将类映射到数据库架构。 如果你使用 Code First 命名约定，则在大多数情况下，你可以依赖于 Code First 根据你在类上定义的外键和导航属性来设置表之间的关系。 如果在定义类时不遵循约定，或者若要更改约定的工作方式，可以使用 Fluent API 或数据批注来配置类，以便 Code First 可以映射表之间的关系。  

## <a name="introduction"></a>简介  

在配置与 Fluent API 的关系时，请从 EntityTypeConfiguration 实例开始，然后使用 HasRequired、HasOptional 或 HasMany 方法来指定此实体参与的关系的类型。 HasRequired 和 HasOptional 方法采用表示引用导航属性的 lambda 表达式。 HasMany 方法采用表示集合导航属性的 lambda 表达式。 然后，可以通过使用 WithRequired、WithOptional 和 WithMany 方法配置反向导航属性。 这些方法具有不带参数的重载，可用于通过单向导航指定基数。  

然后，可以使用 HasForeignKey 方法配置外键属性。 此方法采用一个表示要用作外键的属性的 lambda 表达式。  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>将必需的可选关系配置 (一对零或一)   

下面的示例将配置一对零或一关系。 OfficeAssignment 具有 InstructorID 属性，该属性是主键和外键，因为属性的名称不遵循该约定。 HasKey 方法用于配置主键。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>配置一种关系，在这种情况下，这两个端都需要 (一对一)   

在大多数情况下实体框架可以推断哪个类型是依赖项并且是关系中的主体。 但是，如果需要关系的两端，或者两个两侧都是可选的，则实体框架无法识别依赖项和主体。 如果关系的两端都是必需的，请在 HasRequired 方法后面使用 WithRequiredPrincipal 或 WithRequiredDependent。 如果关系两端都是可选的，请在 HasOptional 方法后面使用 WithOptionalPrincipal 或 WithOptionalDependent。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>配置多对多关系  

下面的代码配置课程类型和指导员类型之间的多对多关系。 在下面的示例中，使用了默认 Code First 约定来创建联接表。 因此，Courseinstructor.courseid 表是使用 Course_CourseID 和 Instructor_InstructorID 列创建的。  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

如果要使用 Map 方法指定联接表名称和表中列的名称，则需要执行其他配置。 下面的代码生成包含 CourseID 和 InstructorID 列的 Courseinstructor.courseid 表。  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>使用一个导航属性配置关系  

一种单向 (也称为单向) 关系，只是在一个关系端上定义导航属性，而不是在两者上定义。 按照约定，Code First 始终将单向关系解释为一对多。 例如，如果你想要在讲师与 OfficeAssignment 之间进行一对一关系，其中仅有指导员类型的导航属性，则需要使用 Fluent API 来配置此关系。  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>启用级联删除  

您可以通过使用 WillCascadeOnDelete 方法为关系配置级联删除。 如果从属实体上的外键不可为 null，则 Code First 在关系上设置级联删除。 如果从属实体上的外键可为 null，则 Code First 不会对关系设置级联删除，并且在删除主体时，外键将设置为 null。  

您可以使用以下方法删除这些级联删除约定：  

modelBuilder \<OneToManyCascadeDeleteConvention\> ( # A1  
modelBuilder \<ManyToManyCascadeDeleteConvention\> ( # A1  

下面的代码将关系配置为 "必需"，然后禁用级联删除。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>配置复合外键  

如果部门类型上的主键由 DepartmentID 和 Name 属性组成，则可以为该部门配置主键，并为课程类型配置外键，如下所示：  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>重命名未在模型中定义的外键  

如果选择不在 CLR 类型上定义外键，但要指定它应在数据库中具有的名称，请执行以下操作：  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>配置不遵循 Code First 约定的外键名称  

如果课程类的外键属性称为 SomeDepartmentID 而不是 DepartmentID，则需要执行以下操作，以指定要 SomeDepartmentID 为外键：  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>示例中使用的模型  

以下 Code First 模型用于此页上的示例。  

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
