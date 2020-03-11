---
title: Code First 约定-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
ms.openlocfilehash: 4d03a32db5d84eb37c22617a95005b272172a65d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415945"
---
# <a name="code-first-conventions"></a>Code First 约定
Code First 使你可以使用C#或 Visual Basic .net 类来描述模型。 使用约定检测模型的基本形状。 约定是一组规则，这些规则用于在使用 Code First 时，基于类定义自动配置概念模型。 约定是在 ModelConfiguration 命名空间中定义的。  

您可以通过使用数据批注或 Fluent API 来进一步配置模型。 优先级是通过 Fluent API 后跟数据注释和约定的方式进行配置。 有关详细信息，请参阅[数据批注](~/ef6/modeling/code-first/data-annotations.md)、[流畅的 api 关系](~/ef6/modeling/code-first/fluent/relationships.md)、[熟知的 api 类型 & 属性](~/ef6/modeling/code-first/fluent/types-and-properties.md)和[VB.NET 的流畅 api](~/ef6/modeling/code-first/fluent/vb.md)。  

[API 文档](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)中提供了 Code First 约定的详细列表。 本主题概述了 Code First 使用的约定。  

## <a name="type-discovery"></a>类型发现  

使用 Code First 开发时，通常首先编写定义概念（域）模型 .NET Framework 类。 除了定义类之外，还需要让**DbContext**知道要在模型中包含的类型。 为此，您定义了一个从**DbContext**派生的上下文类，并公开了要成为模型一部分的类型的**DbSet**属性。 Code First 将包含这些类型，并且还将请求任何引用的类型，即使引用的类型在不同的程序集中定义也是如此。  

如果你的类型参与了继承层次结构，则为基类定义**DbSet**属性就足够了，如果派生类型与基类位于同一程序集中，则它们将自动包含。  

在下面的示例中，在**SchoolEntities**类（**部门**）上只定义了一个**DbSet**属性。 Code First 使用此属性来发现和请求任何引用的类型。  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

如果要从模型中排除某一类型，请使用**NotMapped**属性或**DbModelBuilder** Fluent API。  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>Primary Key 约定  

Code First 推断，如果类的属性命名为 "ID" （不区分大小写）或类名称后跟 "ID"，则属性是主键。 如果主键属性的类型是数值或 GUID，则将其配置为标识列。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>关系约定  

在实体框架中，导航属性提供了一种方法，用于在两个实体类型之间导航关系。 针对对象参与到其中的每个关系，各对象均可以具有导航属性。 使用导航属性，您可以在两个方向上导航和管理关系，返回引用对象（如果重数为1或零）或集合（如果重数为多个）。 Code First 根据在您的类型上定义的导航属性推断关系。  

除了导航属性外，我们还建议你在表示依赖对象的类型上包含外键属性。 任何数据类型与主体主键属性相同且名称遵循以下格式之一的任何属性都表示关系的外键： "\<导航属性名称\>\<主体主键属性名称\>"、"\<主体类名称\>\<primary key 属性名称\>" 或 "\<主体主键属性名称\>"。 如果找到多个匹配项，则优先顺序按上面列出的顺序提供。 外键检测不区分大小写。 当检测到外键属性时，Code First 根据外键的为 null 性推断关系的重数。 如果该属性可以为 null，则会将该关系注册为可选;否则，将根据需要注册此关系。  

如果从属实体上的外键不可为 null，则 Code First 在关系上设置级联删除。 如果从属实体上的外键可为 null，则 Code First 不会对关系设置级联删除，并且在删除主体时，外键将设置为 null。 可以使用 Fluent API 重写约定检测到的重数和级联删除行为。  

在下面的示例中，导航属性和外键用于定义部门和课程类之间的关系。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> 如果相同类型之间有多个关系（例如，假设你定义了**person**和**book**类，其中**Person**类包含**ReviewedBooks**和**AuthoredBooks**导航属性， **Book**类包含**Author**和**审阅者**导航属性），则需要通过使用数据批注或 Fluent API 来手动配置关系。 有关详细信息，请参阅[数据批注-关系](~/ef6/modeling/code-first/data-annotations.md)和[熟知 API 关系](~/ef6/modeling/code-first/fluent/relationships.md)。  

## <a name="complex-types-convention"></a>复杂类型约定  

如果 Code First 发现不能推断出主键的类定义，且没有通过数据批注或 Fluent API 注册主键，则该类型将自动注册为复杂类型。 复杂类型检测还要求类型没有引用实体类型的属性，并且没有从另一类型的集合属性中引用。 给定以下类定义 Code First 将推断，**详细信息**是复杂类型，因为它没有主键。  

``` csharp
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
```  

## <a name="connection-string-convention"></a>连接字符串约定  

若要了解 DbContext 用于发现连接所使用的约定，请参阅[连接和模型](~/ef6/fundamentals/configuring/connection-strings.md)。  

## <a name="removing-conventions"></a>删除约定  

您可以删除在 ModelConfiguration 命名空间中定义的任何约定。 下面的示例将删除**PluralizingTableNameConvention**。  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a>自定义约定  

EF6 中支持自定义约定。 有关详细信息，请参阅[自定义 Code First 约定](~/ef6/modeling/code-first/conventions/custom.md)。
