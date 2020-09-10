---
title: 熟知 API-配置和映射属性和类型-EF6
description: 熟知 API-配置和映射实体框架6中的属性和类型
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: d49da416b2df5828fce5618cc196e22dfbc2bd34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618059"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>熟知 API-配置和映射属性和类型
在使用实体框架时 Code First 默认行为是使用一组约定融入为 EF 将 POCO 类映射到表。 但有时，您不能或不想遵循这些约定，也不需要将实体映射到约定规定的内容。  

可以通过两种主要方式将 EF 配置为使用约定以外的其他内容，即 [批注](xref:ef6/modeling/code-first/data-annotations) 或 EFs Fluent API。 批注仅涵盖 Fluent API 功能的子集，因此存在无法使用批注实现的映射方案。 本文旨在演示如何使用 Fluent API 来配置属性。  

通过重写派生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)上的[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法，最常访问的代码优先 Fluent API。 下面的示例旨在演示如何使用流畅 api 完成各种任务，并允许你复制代码并对其进行自定义以适合你的模型，如果你希望查看可以按原样使用的模型，则在本文末尾提供。  

## <a name="model-wide-settings"></a>模型范围内的设置  

### <a name="default-schema-ef6-onwards"></a>默认架构 (EF6 向前)   

从 EF6 开始，可以对 DbModelBuilder 使用 HasDefaultSchema 方法，以指定要用于所有表、存储过程等的数据库架构。对于为其显式配置不同架构的任何对象，将重写此默认设置。  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>自定义约定 (EF6 向前)   

从 EF6 开始，你可以创建自己的约定来补充 Code First 中包含的约定。 有关更多详细信息，请参阅 [自定义 Code First 约定](xref:ef6/modeling/code-first/conventions/custom)。  

## <a name="property-mapping"></a>属性映射  

[属性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法用于为属于实体或复杂类型的每个属性配置特性。 属性方法用于获取给定属性的配置对象。 配置对象上的选项特定于正在配置的类型;IsUnicode 仅可用于字符串属性（例如）。  

### <a name="configuring-a-primary-key"></a>配置主键  

主键的实体框架约定为：  

1. 类定义的属性的名称为 "ID" 或 "Id"  
2. 或类名后跟 "ID" 或 "Id"  

若要将属性显式设置为主键，可以使用 HasKey 方法。 在下面的示例中，HasKey 方法用于在 OfficeAssignment 类型上配置 InstructorID 主键。  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>配置组合主键  

下面的示例将 DepartmentID 和 Name 属性配置为部门类型的复合主键。  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>关闭数字主键的标识  

下面的示例将 DepartmentID 属性设置为 System.componentmodel，以指示数据库将不会生成此值。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>指定属性的最大长度  

在下面的示例中，Name 属性的长度不应超过50个字符。 如果将值设置为长度超过50个字符，则会收到 [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) 异常。 如果 Code First 通过此模型创建数据库，则还会将 "名称" 列的最大长度设置为50个字符。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>将属性配置为必填  

在下面的示例中，Name 属性是必需的。 如果未指定名称，则会收到 DbEntityValidationException 异常。 如果 Code First 通过此模型创建数据库，则用于存储此属性的列通常不能为 null。  

> [!NOTE]
> 在某些情况下，即使属性是必需的，也无法使数据库中的列不可为 null。 例如，对多个类型使用 TPH 继承战略数据时，会将其存储在一个表中。 如果派生的类型包含所需的属性，则列不能为 null，因为并不是层次结构中的所有类型都具有此属性。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>对一个或多个属性配置索引  

> [!NOTE]
> **Ef 6.1 仅** 往上-索引属性是在实体框架6.1 中引入的。 如果你使用的是早期版本，则本部分中的信息不适用。  

流畅的 API 不支持创建索引，但你可以通过流畅的 API 使用对 **IndexAttribute** 的支持。 索引属性的处理方式是在模型中包含模型批注，然后在管道中的后续部分将其转换为数据库中的索引。 可以使用熟知的 API 手动添加这些相同的注释。  

执行此操作的最简单方法是创建一个 **IndexAttribute** 实例，其中包含新索引的所有设置。 然后，你可以创建一个 **IndexAnnotation** 实例，该实例是一个 EF 特定类型，它将 **IndexAttribute** 设置转换为可存储在 EF 模型上的模型注释。 然后，可以将这些方法传递到熟知 API 上的 **HasColumnAnnotation** 方法，并指定该批注的名称 **索引** 。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

有关**IndexAttribute**中可用设置的完整列表，请参阅[Code First 数据批注](xref:ef6/modeling/code-first/data-annotations)的*索引*部分。 这包括自定义索引名称、创建唯一索引以及创建多列索引。  

可以通过将 **IndexAttribute** 数组传递到 **IndexAnnotation**的构造函数，在单个属性上指定多个索引批注。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>指定不将 CLR 属性映射到数据库中的列  

下面的示例演示如何指定 CLR 类型的属性未映射到数据库中的列。  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>将 CLR 属性映射到数据库中的特定列  

下面的示例将 Name CLR 属性映射到 DepartmentName 数据库列。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>重命名未在模型中定义的外键  

如果选择不在 CLR 类型上定义外键，但要指定它应在数据库中具有的名称，请执行以下操作：  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>配置字符串属性是否支持 Unicode 内容  

默认情况下，字符串是 SQL Server) 中 (nvarchar 的 Unicode。 可以使用 IsUnicode 方法来指定字符串应为 varchar 类型。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>配置数据库列的数据类型  

[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法允许映射到相同基本类型的不同表示形式。 使用此方法不能在运行时执行数据的任何转换。 请注意，IsUnicode 是将列设置为 varchar 的首选方式，因为它是数据库不可知的。  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>配置复杂类型的属性  

可以通过两种方法来配置复杂类型的标量属性。  

可以在 ComplexTypeConfiguration 上调用属性。  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

你还可以使用点表示法访问复杂类型的属性。  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>配置要用作开放式并发标记的属性  

若要指定实体中的属性表示并发标记，可以使用 ConcurrencyCheck 特性或 IsConcurrencyToken 方法。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

还可以使用 IsRowVersion 方法将属性配置为数据库中的行版本。 将属性设置为行版本会自动将它配置为开放式并发标记。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>类型映射  

### <a name="specifying-that-a-class-is-a-complex-type"></a>指定类为复杂类型  

按照约定，未指定主键的类型将被视为复杂类型。 在某些情况下，Code First 将检测不到复杂类型 (例如，如果你有一个名为 ID 的属性，但你并不意味着它是主键) 。 在这种情况下，可以使用 Fluent API 显式指定类型为复杂类型。  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>指定不将 CLR 实体类型映射到数据库中的表  

下面的示例演示如何将 CLR 类型从映射到数据库中的表。  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>将实体类型映射到数据库中的特定表  

部门的所有属性都将映射到名为 t_ 部门的表中的列。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

你还可以指定架构名称，如下所示：  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>将每个层次结构一个表映射 (TPH) 继承  

在 TPH 映射方案中，继承层次结构中的所有类型都映射到单个表。 鉴别器列用于标识每行的类型。 在 Code First 创建模型时，TPH 是参与继承层次结构的类型的默认策略。 默认情况下，鉴别器列将添加到名称为 "鉴别器" 的表中，层次结构中的每个类型的 CLR 类型名称将用于鉴别器值。 您可以使用 Fluent API 修改默认行为。  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>将每种类型一个表映射 (TPT) 继承  

在 TPT 映射方案中，所有类型都映射到单个表。 仅属于某个基类型或派生类型的属性存储在映射到该类型的一个表中。 映射到派生类型的表还存储将派生表与基表联接的外键。  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>将每个具体的表类映射 (TPC) 继承  

在 TPC 映射方案中，层次结构中的所有非抽象类型都将映射到单个表。 映射到派生类的表与映射到数据库中的基类的表没有关系。 类的所有属性（包括继承的属性）都映射到对应表的列。  

调用 MapInheritedProperties 方法来配置每个派生类型。 对于派生类，MapInheritedProperties 将从基类继承的所有属性重新映射到表中的新列。  

> [!NOTE]
> 请注意，由于参与了 TPC 继承层次结构的表不共享主键，因此，如果数据库生成的值具有相同的标识种子，则在映射到子类的表中插入时，将会出现重复的实体键。 若要解决此问题，可以为每个表指定不同的初始种子值，或者在 primary key 属性上关闭 "标识"。 当使用 Code First 时，标识是整数键属性的默认值。  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>将实体类型的属性映射到数据库中的多个表 (实体拆分)   

实体拆分允许将某个实体类型的属性分布到多个表中。 在下面的示例中，部门实体拆分为两个表：部门和 DepartmentDetails。 实体拆分对 Map 方法使用多个调用，以将属性的子集映射到特定的表。  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>将多个实体类型映射到数据库中的一个表 (表拆分)   

下面的示例将共享主键的两个实体类型映射到一个表。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>将实体类型映射到插入/更新/删除存储过程 (EF6 向前)   

从 EF6 开始，可以将实体映射为使用存储过程来执行插入更新和删除操作。 有关更多详细信息，请参阅 [Code First 插入/更新/删除存储过程](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)。  

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
