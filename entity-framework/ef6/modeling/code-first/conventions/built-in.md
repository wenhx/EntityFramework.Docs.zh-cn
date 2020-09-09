---
title: Code First 约定-EF6
description: 实体框架6中的 Code First 约定
author: divega
ms.date: 10/23/2016
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: 78471a1d4925e57146b8e9f2f43e57b626d164b6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617196"
---
# <a name="code-first-conventions"></a><span data-ttu-id="7029e-103">Code First 约定</span><span class="sxs-lookup"><span data-stu-id="7029e-103">Code First Conventions</span></span>
<span data-ttu-id="7029e-104">Code First 使你可以使用 c # 或 Visual Basic .NET 类来描述模型。</span><span class="sxs-lookup"><span data-stu-id="7029e-104">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="7029e-105">使用约定检测模型的基本形状。</span><span class="sxs-lookup"><span data-stu-id="7029e-105">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="7029e-106">约定是一组规则，这些规则用于在使用 Code First 时，基于类定义自动配置概念模型。</span><span class="sxs-lookup"><span data-stu-id="7029e-106">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="7029e-107">约定是在 ModelConfiguration 命名空间中定义的。</span><span class="sxs-lookup"><span data-stu-id="7029e-107">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="7029e-108">您可以通过使用数据批注或 Fluent API 来进一步配置模型。</span><span class="sxs-lookup"><span data-stu-id="7029e-108">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="7029e-109">优先级是通过 Fluent API 后跟数据注释和约定的方式进行配置。</span><span class="sxs-lookup"><span data-stu-id="7029e-109">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="7029e-110">有关详细信息，请参阅 [数据批注](xref:ef6/modeling/code-first/data-annotations)、 [流畅的 api 关系](xref:ef6/modeling/code-first/fluent/relationships)、 [熟知的 api 类型 & 属性](xref:ef6/modeling/code-first/fluent/types-and-properties) 和 [VB.NET 的流畅 api](xref:ef6/modeling/code-first/fluent/vb)。</span><span class="sxs-lookup"><span data-stu-id="7029e-110">For more information see [Data Annotations](xref:ef6/modeling/code-first/data-annotations), [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API - Types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span></span>  

<span data-ttu-id="7029e-111">[API 文档](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)中提供了 Code First 约定的详细列表。</span><span class="sxs-lookup"><span data-stu-id="7029e-111">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="7029e-112">本主题概述了 Code First 使用的约定。</span><span class="sxs-lookup"><span data-stu-id="7029e-112">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="7029e-113">类型发现</span><span class="sxs-lookup"><span data-stu-id="7029e-113">Type Discovery</span></span>  

<span data-ttu-id="7029e-114">使用 Code First 开发时，通常首先编写 .NET Framework 类来定义概念 (域) 模型。</span><span class="sxs-lookup"><span data-stu-id="7029e-114">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="7029e-115">除了定义类之外，还需要让 **DbContext** 知道要在模型中包含的类型。</span><span class="sxs-lookup"><span data-stu-id="7029e-115">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="7029e-116">为此，您定义了一个从 **DbContext** 派生的上下文类，并公开了要成为模型一部分的类型的 **DbSet** 属性。</span><span class="sxs-lookup"><span data-stu-id="7029e-116">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="7029e-117">Code First 将包含这些类型，并且还将请求任何引用的类型，即使引用的类型在不同的程序集中定义也是如此。</span><span class="sxs-lookup"><span data-stu-id="7029e-117">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="7029e-118">如果你的类型参与了继承层次结构，则为基类定义 **DbSet** 属性就足够了，如果派生类型与基类位于同一程序集中，则它们将自动包含。</span><span class="sxs-lookup"><span data-stu-id="7029e-118">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="7029e-119">在下面的示例中， **SchoolEntities**类上只定义了一个**DbSet**属性 (**部门**) 。</span><span class="sxs-lookup"><span data-stu-id="7029e-119">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="7029e-120">Code First 使用此属性来发现和请求任何引用的类型。</span><span class="sxs-lookup"><span data-stu-id="7029e-120">Code First uses this property to discover and pull in any referenced types.</span></span>  

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

<span data-ttu-id="7029e-121">如果要从模型中排除某一类型，请使用 **NotMapped** 属性或 **DbModelBuilder** Fluent API。</span><span class="sxs-lookup"><span data-stu-id="7029e-121">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="7029e-122">Primary Key 约定</span><span class="sxs-lookup"><span data-stu-id="7029e-122">Primary Key Convention</span></span>  

<span data-ttu-id="7029e-123">Code First 推断，如果类的属性命名为 "ID" (不区分大小写) ，则为; 如果类名称后跟 "ID"，则推断属性是否为主键。</span><span class="sxs-lookup"><span data-stu-id="7029e-123">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="7029e-124">如果主键属性的类型是数值或 GUID，则将其配置为标识列。</span><span class="sxs-lookup"><span data-stu-id="7029e-124">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="7029e-125">关系约定</span><span class="sxs-lookup"><span data-stu-id="7029e-125">Relationship Convention</span></span>  

<span data-ttu-id="7029e-126">在实体框架中，导航属性提供了一种在两个实体类型之间导航关系的方法。</span><span class="sxs-lookup"><span data-stu-id="7029e-126">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="7029e-127">针对对象参与到其中的每个关系，各对象均可以具有导航属性。</span><span class="sxs-lookup"><span data-stu-id="7029e-127">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="7029e-128">使用导航属性，您可以在两个方向上导航和管理关系，如果重数为1或零或一个) ，则返回引用对象 (; 如果重数为多) ，则返回 (集合。</span><span class="sxs-lookup"><span data-stu-id="7029e-128">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="7029e-129">Code First 根据在您的类型上定义的导航属性推断关系。</span><span class="sxs-lookup"><span data-stu-id="7029e-129">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="7029e-130">除了导航属性外，我们还建议你在表示依赖对象的类型上包含外键属性。</span><span class="sxs-lookup"><span data-stu-id="7029e-130">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="7029e-131">如果任何属性的数据类型与主体主键属性相同，并且具有以下格式之一的名称，则表示关系的外键： " \<navigation property name\> \<principal primary key property name\> "、" \<principal class name\> \<primary key property name\> " 或 " \<principal primary key property name\> "。</span><span class="sxs-lookup"><span data-stu-id="7029e-131">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="7029e-132">如果找到多个匹配项，则优先顺序按上面列出的顺序提供。</span><span class="sxs-lookup"><span data-stu-id="7029e-132">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="7029e-133">外键检测不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="7029e-133">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="7029e-134">当检测到外键属性时，Code First 根据外键的为 null 性推断关系的重数。</span><span class="sxs-lookup"><span data-stu-id="7029e-134">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="7029e-135">如果该属性可以为 null，则会将该关系注册为可选;否则，将根据需要注册此关系。</span><span class="sxs-lookup"><span data-stu-id="7029e-135">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="7029e-136">如果从属实体上的外键不可为 null，则 Code First 在关系上设置级联删除。</span><span class="sxs-lookup"><span data-stu-id="7029e-136">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="7029e-137">如果从属实体上的外键可为 null，则 Code First 不会对关系设置级联删除，并且在删除主体时，外键将设置为 null。</span><span class="sxs-lookup"><span data-stu-id="7029e-137">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="7029e-138">可以使用 Fluent API 重写约定检测到的重数和级联删除行为。</span><span class="sxs-lookup"><span data-stu-id="7029e-138">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="7029e-139">在下面的示例中，导航属性和外键用于定义部门和课程类之间的关系。</span><span class="sxs-lookup"><span data-stu-id="7029e-139">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

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
> <span data-ttu-id="7029e-140">如果同一类型之间有多个关系 (例如，假设你定义了 **person** 类和 **book** 类，其中 **Person** 类包含 **ReviewedBooks** 和 **AuthoredBooks** 导航属性， **Book** 类包含 **作者** 和 **审阅者** 导航属性，) 你需要使用数据批注或 Fluent API 手动配置关系。</span><span class="sxs-lookup"><span data-stu-id="7029e-140">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="7029e-141">有关详细信息，请参阅 [数据批注-关系](xref:ef6/modeling/code-first/data-annotations) 和 [熟知 API 关系](xref:ef6/modeling/code-first/fluent/relationships)。</span><span class="sxs-lookup"><span data-stu-id="7029e-141">For more information, see [Data Annotations - Relationships](xref:ef6/modeling/code-first/data-annotations) and [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="7029e-142">复杂类型约定</span><span class="sxs-lookup"><span data-stu-id="7029e-142">Complex Types Convention</span></span>  

<span data-ttu-id="7029e-143">如果 Code First 发现不能推断出主键的类定义，且没有通过数据批注或 Fluent API 注册主键，则该类型将自动注册为复杂类型。</span><span class="sxs-lookup"><span data-stu-id="7029e-143">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="7029e-144">复杂类型检测还要求类型没有引用实体类型的属性，并且没有从另一类型的集合属性中引用。</span><span class="sxs-lookup"><span data-stu-id="7029e-144">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="7029e-145">给定以下类定义 Code First 将推断， **详细信息** 是复杂类型，因为它没有主键。</span><span class="sxs-lookup"><span data-stu-id="7029e-145">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

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

## <a name="connection-string-convention"></a><span data-ttu-id="7029e-146">连接字符串约定</span><span class="sxs-lookup"><span data-stu-id="7029e-146">Connection String Convention</span></span>  

<span data-ttu-id="7029e-147">若要了解 DbContext 用于发现连接所使用的约定，请参阅 [连接和模型](xref:ef6/fundamentals/configuring/connection-strings)。</span><span class="sxs-lookup"><span data-stu-id="7029e-147">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](xref:ef6/fundamentals/configuring/connection-strings).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="7029e-148">删除约定</span><span class="sxs-lookup"><span data-stu-id="7029e-148">Removing Conventions</span></span>  

<span data-ttu-id="7029e-149">您可以删除在 ModelConfiguration 命名空间中定义的任何约定。</span><span class="sxs-lookup"><span data-stu-id="7029e-149">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="7029e-150">下面的示例将删除 **PluralizingTableNameConvention**。</span><span class="sxs-lookup"><span data-stu-id="7029e-150">The following example removes **PluralizingTableNameConvention**.</span></span>  

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

## <a name="custom-conventions"></a><span data-ttu-id="7029e-151">自定义约定</span><span class="sxs-lookup"><span data-stu-id="7029e-151">Custom Conventions</span></span>  

<span data-ttu-id="7029e-152">EF6 中支持自定义约定。</span><span class="sxs-lookup"><span data-stu-id="7029e-152">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="7029e-153">有关详细信息，请参阅 [自定义 Code First 约定](xref:ef6/modeling/code-first/conventions/custom)。</span><span class="sxs-lookup"><span data-stu-id="7029e-153">For more information see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>
