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
# <a name="relationships-navigation-properties-and-foreign-keys"></a>关系、导航属性和外键

本文概述了实体框架如何管理实体之间的关系。 它还就如何映射和操作关系提供了一些指导。

## <a name="relationships-in-ef"></a>EF 中的关系

在关系数据库中，表之间的关系（也称为关联）通过外键定义。 外键 (FK) 是用于建立和加强两个表数据之间的链接的一列或多列。 通常有三种类型的关系：一对一、一对多和多对多。 在一对多关系中，外键在表上定义，该表表示关系的多端。 多对多关系涉及定义第三个表（称为连接表或联接表），其主键由两个相关表中的外键组成。 在一对一关系中，主键另外充当外键，并且两个表没有单独的外键列。

下图显示了两个参与一对多关系的表。 **课程**表是从属表，因为它包含将其链接到 **"部门"** 表的 **"部门 ID"** 列。

![部门和课程表](~/ef6/media/database2.png)

在实体框架中，实体可以通过关联或关系与其他实体关联。 每个关系包含两个端，用于描述该关系中两个实体的实体类型和类型的多重性（一个、零或一个或多个）。 关系可由引用约束控制，该引用约束描述了关系中的哪端为 Principal Role 以及哪端为 Dependent Role。

导航属性提供了一种导航两种实体类型之间的关联的方法。 针对对象参与到其中的每个关系，各对象均可以具有导航属性。 导航属性允许您在两个方向上导航和管理关系，返回引用对象（如果多重性为 1 或为零或 1）或集合（如果多重性多）。 您还可以选择单向导航，在这种情况下，您只对参与关系的一种类型定义导航属性，而不是同时在两种类型上定义导航属性。

建议在模型中包括映射到数据库中的外键的属性。 加入了外键属性，您就可以通过修改依赖对象的外键值来创建或更改关系。 此类关联称为外键关联。 使用断开连接的实体时，使用外键更为重要。 请注意，使用 1 比 1 或 1 比 0 时。1 个关系，没有单独的外键列，主键属性充当外键，始终包含在模型中。

当模型中不包括外键列时，关联信息将作为独立对象进行管理。 关系通过对象引用而不是外键属性进行跟踪。 这种类型的关联称为*独立关联*。 修改*独立关联的*最常见方法是修改为参与关联的每个实体生成的导航属性。

可以在您的模型中选择使用一种或两种类型的关联。 但是，如果您有一个纯多对多关系，该关系由仅包含外键的联接表连接，则 EF 将使用独立关联来管理此类多对多关系。   

下图显示了使用实体框架设计器创建的概念模型。 该模型包含两个参与一对多关系的实体。 两个实体都具有导航属性。 **课程**是从属实体，并且定义了**DepartmentID**外键属性。

![具有导航属性的部门和课程表](~/ef6/media/relationshipefdesigner.png)

以下代码段显示使用代码优先创建的模型。

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

## <a name="configuring-or-mapping-relationships"></a>配置或映射关系

本页的其余部分介绍如何使用关系访问和操作数据。 有关在模型中设置关系的信息，请参阅以下页面。

-   要在代码优先中配置关系，请参阅[数据注释](~/ef6/modeling/code-first/data-annotations.md)和[Fluent API = 关系](~/ef6/modeling/code-first/fluent/relationships.md)。
-   要使用实体框架设计器配置关系，请参阅[与 EF 设计器的关系](~/ef6/modeling/designer/relationships.md)。

## <a name="creating-and-modifying-relationships"></a>创建和修改关系

在*外键关联*中，当您更改关系时，具有`EntityState.Unchanged`状态的从属对象的状态将更改为 。 `EntityState.Modified` 在独立关系中，更改关系不会更新从属对象的状态。

以下示例演示如何使用外键属性和导航属性来关联相关对象。 使用外键关联，可以使用任一方法更改、创建或修改关系。 使用独立关联，则不能使用外键属性。

- 通过将新值分配给外键属性，如以下示例所示。  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- 以下代码通过将外键设置为**null**来删除关系。 请注意，外键属性必须为空。  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > 如果引用处于添加状态（在此示例中为课程对象），则在调用 SaveChanges 之前，引用导航属性不会与新对象的键值同步。 由于对象上下文在键值保存前不包含已添加对象的永久键，因此不发生同步。 如果在设置关系后立即必须完全同步新对象，请使用以下方法之一。

- 通过将一个新对象分配给导航属性。 以下代码在 课程和 之间`department`创建关系。 如果对象附加到上下文，`course`则 还会将 添加到`department.Courses`集合中，并且`course`对象上的相应外键属性将设置为部门的关键属性值。  
  ``` csharp
  course.Department = department;
  ```

- 要删除关系，请将导航属性设置为`null`。 如果使用基于 .NET 4.0 的实体框架，则需要在将其设置为 null 之前加载相关端。 例如：   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  从基于 .NET 4.5 的实体框架 5.0 开始，可以将关系设置为 null，而无需加载相关端。 您还可以使用以下方法将当前值设置为 null。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- 通过在实体集合中删除或添加对象。 例如，可以将类型`Course`对象添加到集合中。 `department.Courses` 此操作在特定**课程**和特定`department`之间创建关系。 如果对象附加到上下文，**则课程**对象的部门引用和外键属性将设置为相应的`department`。  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- 通过使用 方法`ChangeRelationshipState`更改两个实体对象之间指定关系的状态。 此方法最常用于使用 N-Tier 应用程序和独立*关联*（不能与外键关联一起使用）。 此外，要使用此方法，必须下拉至`ObjectContext`，如下例所示。  
在下面的示例中，教师和课程之间存在多对多关系。 调用`ChangeRelationshipState`方法并传递`EntityState.Added`参数，`SchoolContext`可以知道两个对象之间已添加了关系：
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  请注意，如果要更新（而不仅仅是添加）关系，则必须在添加新关系后删除旧关系：

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>同步外键和导航属性之间的更改

使用上述方法之一更改附加到上下文的对象之间的关系时，实体框架需要保持外键、引用和集合同步。实体框架自动管理具有代理的 POCO 实体的此同步（也称为关系修复）。 有关详细信息，请参阅[使用代理](~/ef6/fundamentals/proxies.md)。

如果使用没有代理的 POCO 实体，则必须确保调用**检测更改**方法以同步上下文中的相关对象。 请注意，以下 API 会自动触发**检测更改**调用。

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
-   针对 执行 LINQ 查询`DbSet`

## <a name="loading-related-objects"></a>加载相关对象

在实体框架中，通常使用导航属性加载由定义的关联与返回的实体相关的实体。 有关详细信息，请参阅[加载相关对象](~/ef6/querying/related-data.md)。

> [!NOTE]
> 在外键关联中，加载依赖对象的相关端时，将会基于当前位于内存中的依赖对象的外键值加载相关对象：

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

在独立关联中，基于当前数据库中的外键值查询依赖对象的相关端。 但是，如果修改了关系，并且从属对象的引用属性指向在对象上下文中加载的不同主体对象，实体框架将尝试创建在客户端上定义的关系。

## <a name="managing-concurrency"></a>管理并发

在外键和独立关联中，并发检查基于模型中定义的实体密钥和其他实体属性。 使用 EF 设计器创建模型时，将`ConcurrencyMode`属性设置为**固定**以指定应检查属性为并发性。 使用 Code First 定义模型时，对`ConcurrencyCheck`要检查为并发的属性使用注释。 使用 Code First 时，`TimeStamp`还可以使用注释指定应检查属性为并发性。 给定类中只能有一个时间戳属性。 代码 First 将此属性映射到数据库中的非空字段。

我们建议您在使用参与并发检查和解析的实体时始终使用外键关联。

有关详细信息，请参阅[处理并发冲突](~/ef6/saving/concurrency.md)。

## <a name="working-with-overlapping-keys"></a>使用重叠键

重叠键是指键中某些属性亦是实体中其他键的一部分的那些组合键。 在独立关联中不能包含重叠键。 若要更改包含重叠键的外键关联，我们建议您修改外键值而不要使用对象引用。
