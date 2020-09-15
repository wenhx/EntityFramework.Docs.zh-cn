---
title: 熟知 API-配置和映射属性和类型-EF6
description: 熟知 API-配置和映射实体框架6中的属性和类型
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: ddd56ff280b72adbfd2247a4f1a85e37bf2f4879
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072585"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="1507e-103">熟知 API-配置和映射属性和类型</span><span class="sxs-lookup"><span data-stu-id="1507e-103">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="1507e-104">在使用实体框架时 Code First 默认行为是使用一组约定融入为 EF 将 POCO 类映射到表。</span><span class="sxs-lookup"><span data-stu-id="1507e-104">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="1507e-105">但有时，您不能或不想遵循这些约定，也不需要将实体映射到约定规定的内容。</span><span class="sxs-lookup"><span data-stu-id="1507e-105">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="1507e-106">可以通过两种主要方式将 EF 配置为使用约定以外的其他内容，即 [批注](xref:ef6/modeling/code-first/data-annotations) 或 EFs Fluent API。</span><span class="sxs-lookup"><span data-stu-id="1507e-106">There are two main ways you can configure EF to use something other than conventions, namely [annotations](xref:ef6/modeling/code-first/data-annotations) or EFs fluent API.</span></span> <span data-ttu-id="1507e-107">批注仅涵盖 Fluent API 功能的子集，因此存在无法使用批注实现的映射方案。</span><span class="sxs-lookup"><span data-stu-id="1507e-107">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="1507e-108">本文旨在演示如何使用 Fluent API 来配置属性。</span><span class="sxs-lookup"><span data-stu-id="1507e-108">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="1507e-109">通过重写派生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)上的[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法，最常访问的代码优先 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="1507e-109">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="1507e-110">下面的示例旨在演示如何使用流畅 api 完成各种任务，并允许你复制代码并对其进行自定义以适合你的模型，如果你希望查看可以按原样使用的模型，则在本文末尾提供。</span><span class="sxs-lookup"><span data-stu-id="1507e-110">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="1507e-111">模型范围内的设置</span><span class="sxs-lookup"><span data-stu-id="1507e-111">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="1507e-112">默认架构 (EF6 向前) </span><span class="sxs-lookup"><span data-stu-id="1507e-112">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="1507e-113">从 EF6 开始，可以对 DbModelBuilder 使用 HasDefaultSchema 方法，以指定要用于所有表、存储过程等的数据库架构。对于为其显式配置不同架构的任何对象，将重写此默认设置。</span><span class="sxs-lookup"><span data-stu-id="1507e-113">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="1507e-114">自定义约定 (EF6 向前) </span><span class="sxs-lookup"><span data-stu-id="1507e-114">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="1507e-115">从 EF6 开始，你可以创建自己的约定来补充 Code First 中包含的约定。</span><span class="sxs-lookup"><span data-stu-id="1507e-115">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="1507e-116">有关更多详细信息，请参阅 [自定义 Code First 约定](xref:ef6/modeling/code-first/conventions/custom)。</span><span class="sxs-lookup"><span data-stu-id="1507e-116">For more details, see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="1507e-117">属性映射</span><span class="sxs-lookup"><span data-stu-id="1507e-117">Property Mapping</span></span>  

<span data-ttu-id="1507e-118">[属性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法用于为属于实体或复杂类型的每个属性配置特性。</span><span class="sxs-lookup"><span data-stu-id="1507e-118">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="1507e-119">属性方法用于获取给定属性的配置对象。</span><span class="sxs-lookup"><span data-stu-id="1507e-119">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="1507e-120">配置对象上的选项特定于正在配置的类型;IsUnicode 仅可用于字符串属性（例如）。</span><span class="sxs-lookup"><span data-stu-id="1507e-120">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="1507e-121">配置主键</span><span class="sxs-lookup"><span data-stu-id="1507e-121">Configuring a Primary Key</span></span>  

<span data-ttu-id="1507e-122">主键的实体框架约定为：</span><span class="sxs-lookup"><span data-stu-id="1507e-122">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="1507e-123">类定义的属性的名称为 "ID" 或 "Id"</span><span class="sxs-lookup"><span data-stu-id="1507e-123">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="1507e-124">或类名后跟 "ID" 或 "Id"</span><span class="sxs-lookup"><span data-stu-id="1507e-124">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="1507e-125">若要将属性显式设置为主键，可以使用 HasKey 方法。</span><span class="sxs-lookup"><span data-stu-id="1507e-125">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="1507e-126">在下面的示例中，HasKey 方法用于在 OfficeAssignment 类型上配置 InstructorID 主键。</span><span class="sxs-lookup"><span data-stu-id="1507e-126">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="1507e-127">配置组合主键</span><span class="sxs-lookup"><span data-stu-id="1507e-127">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="1507e-128">下面的示例将 DepartmentID 和 Name 属性配置为部门类型的复合主键。</span><span class="sxs-lookup"><span data-stu-id="1507e-128">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="1507e-129">关闭数字主键的标识</span><span class="sxs-lookup"><span data-stu-id="1507e-129">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="1507e-130">下面的示例将 DepartmentID 属性设置为 System.componentmodel，以指示数据库将不会生成此值。</span><span class="sxs-lookup"><span data-stu-id="1507e-130">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="1507e-131">指定属性的最大长度</span><span class="sxs-lookup"><span data-stu-id="1507e-131">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="1507e-132">在下面的示例中，Name 属性的长度不应超过50个字符。</span><span class="sxs-lookup"><span data-stu-id="1507e-132">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="1507e-133">如果将值设置为长度超过50个字符，则会收到 [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) 异常。</span><span class="sxs-lookup"><span data-stu-id="1507e-133">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="1507e-134">如果 Code First 通过此模型创建数据库，则还会将 "名称" 列的最大长度设置为50个字符。</span><span class="sxs-lookup"><span data-stu-id="1507e-134">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="1507e-135">将属性配置为必填</span><span class="sxs-lookup"><span data-stu-id="1507e-135">Configuring the Property to be Required</span></span>  

<span data-ttu-id="1507e-136">在下面的示例中，Name 属性是必需的。</span><span class="sxs-lookup"><span data-stu-id="1507e-136">In the following example, the Name property is required.</span></span> <span data-ttu-id="1507e-137">如果未指定名称，则会收到 DbEntityValidationException 异常。</span><span class="sxs-lookup"><span data-stu-id="1507e-137">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="1507e-138">如果 Code First 通过此模型创建数据库，则用于存储此属性的列通常不能为 null。</span><span class="sxs-lookup"><span data-stu-id="1507e-138">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="1507e-139">在某些情况下，即使属性是必需的，也无法使数据库中的列不可为 null。</span><span class="sxs-lookup"><span data-stu-id="1507e-139">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="1507e-140">例如，对多个类型使用 TPH 继承战略数据时，会将其存储在一个表中。</span><span class="sxs-lookup"><span data-stu-id="1507e-140">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="1507e-141">如果派生的类型包含所需的属性，则列不能为 null，因为并不是层次结构中的所有类型都具有此属性。</span><span class="sxs-lookup"><span data-stu-id="1507e-141">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="1507e-142">对一个或多个属性配置索引</span><span class="sxs-lookup"><span data-stu-id="1507e-142">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="1507e-143">**Ef 6.1 仅** 往上-索引属性是在实体框架6.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="1507e-143">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="1507e-144">如果你使用的是早期版本，则本部分中的信息不适用。</span><span class="sxs-lookup"><span data-stu-id="1507e-144">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="1507e-145">流畅的 API 不支持创建索引，但你可以通过流畅的 API 使用对 **IndexAttribute** 的支持。</span><span class="sxs-lookup"><span data-stu-id="1507e-145">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="1507e-146">索引属性的处理方式是在模型中包含模型批注，然后在管道中的后续部分将其转换为数据库中的索引。</span><span class="sxs-lookup"><span data-stu-id="1507e-146">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="1507e-147">可以使用熟知的 API 手动添加这些相同的注释。</span><span class="sxs-lookup"><span data-stu-id="1507e-147">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="1507e-148">执行此操作的最简单方法是创建一个 **IndexAttribute** 实例，其中包含新索引的所有设置。</span><span class="sxs-lookup"><span data-stu-id="1507e-148">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="1507e-149">然后，你可以创建一个 **IndexAnnotation** 实例，该实例是一个 EF 特定类型，它将 **IndexAttribute** 设置转换为可存储在 EF 模型上的模型注释。</span><span class="sxs-lookup"><span data-stu-id="1507e-149">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="1507e-150">然后，可以将这些方法传递到熟知 API 上的 **HasColumnAnnotation** 方法，并指定该批注的名称 **索引** 。</span><span class="sxs-lookup"><span data-stu-id="1507e-150">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="1507e-151">有关**IndexAttribute**中可用设置的完整列表，请参阅[Code First 数据批注](xref:ef6/modeling/code-first/data-annotations)的*索引*部分。</span><span class="sxs-lookup"><span data-stu-id="1507e-151">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](xref:ef6/modeling/code-first/data-annotations).</span></span> <span data-ttu-id="1507e-152">这包括自定义索引名称、创建唯一索引以及创建多列索引。</span><span class="sxs-lookup"><span data-stu-id="1507e-152">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="1507e-153">可以通过将 **IndexAttribute** 数组传递到 **IndexAnnotation**的构造函数，在单个属性上指定多个索引批注。</span><span class="sxs-lookup"><span data-stu-id="1507e-153">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="1507e-154">指定不将 CLR 属性映射到数据库中的列</span><span class="sxs-lookup"><span data-stu-id="1507e-154">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="1507e-155">下面的示例演示如何指定 CLR 类型的属性未映射到数据库中的列。</span><span class="sxs-lookup"><span data-stu-id="1507e-155">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="1507e-156">将 CLR 属性映射到数据库中的特定列</span><span class="sxs-lookup"><span data-stu-id="1507e-156">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="1507e-157">下面的示例将 Name CLR 属性映射到 DepartmentName 数据库列。</span><span class="sxs-lookup"><span data-stu-id="1507e-157">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="1507e-158">重命名未在模型中定义的外键</span><span class="sxs-lookup"><span data-stu-id="1507e-158">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="1507e-159">如果选择不在 CLR 类型上定义外键，但要指定它应在数据库中具有的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1507e-159">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="1507e-160">配置字符串属性是否支持 Unicode 内容</span><span class="sxs-lookup"><span data-stu-id="1507e-160">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="1507e-161">默认情况下，字符串是 SQL Server) 中 (nvarchar 的 Unicode。</span><span class="sxs-lookup"><span data-stu-id="1507e-161">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="1507e-162">可以使用 IsUnicode 方法来指定字符串应为 varchar 类型。</span><span class="sxs-lookup"><span data-stu-id="1507e-162">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="1507e-163">配置数据库列的数据类型</span><span class="sxs-lookup"><span data-stu-id="1507e-163">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="1507e-164">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法允许映射到相同基本类型的不同表示形式。</span><span class="sxs-lookup"><span data-stu-id="1507e-164">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="1507e-165">使用此方法不能在运行时执行数据的任何转换。</span><span class="sxs-lookup"><span data-stu-id="1507e-165">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="1507e-166">请注意，IsUnicode 是将列设置为 varchar 的首选方式，因为它是数据库不可知的。</span><span class="sxs-lookup"><span data-stu-id="1507e-166">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="1507e-167">配置复杂类型的属性</span><span class="sxs-lookup"><span data-stu-id="1507e-167">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="1507e-168">可以通过两种方法来配置复杂类型的标量属性。</span><span class="sxs-lookup"><span data-stu-id="1507e-168">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="1507e-169">可以在 ComplexTypeConfiguration 上调用属性。</span><span class="sxs-lookup"><span data-stu-id="1507e-169">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="1507e-170">你还可以使用点表示法访问复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="1507e-170">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="1507e-171">配置要用作开放式并发标记的属性</span><span class="sxs-lookup"><span data-stu-id="1507e-171">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="1507e-172">若要指定实体中的属性表示并发标记，可以使用 ConcurrencyCheck 特性或 IsConcurrencyToken 方法。</span><span class="sxs-lookup"><span data-stu-id="1507e-172">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="1507e-173">还可以使用 IsRowVersion 方法将属性配置为数据库中的行版本。</span><span class="sxs-lookup"><span data-stu-id="1507e-173">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="1507e-174">将属性设置为行版本会自动将它配置为开放式并发标记。</span><span class="sxs-lookup"><span data-stu-id="1507e-174">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="1507e-175">类型映射</span><span class="sxs-lookup"><span data-stu-id="1507e-175">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="1507e-176">指定类为复杂类型</span><span class="sxs-lookup"><span data-stu-id="1507e-176">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="1507e-177">按照约定，未指定主键的类型将被视为复杂类型。</span><span class="sxs-lookup"><span data-stu-id="1507e-177">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="1507e-178">在某些情况下，Code First 将检测不到复杂类型 (例如，如果你有一个名为 ID 的属性，但你并不意味着它是主键) 。</span><span class="sxs-lookup"><span data-stu-id="1507e-178">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="1507e-179">在这种情况下，可以使用 Fluent API 显式指定类型为复杂类型。</span><span class="sxs-lookup"><span data-stu-id="1507e-179">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="1507e-180">指定不将 CLR 实体类型映射到数据库中的表</span><span class="sxs-lookup"><span data-stu-id="1507e-180">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="1507e-181">下面的示例演示如何将 CLR 类型从映射到数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="1507e-181">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="1507e-182">将实体类型映射到数据库中的特定表</span><span class="sxs-lookup"><span data-stu-id="1507e-182">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="1507e-183">部门的所有属性都将映射到名为 t_ 部门的表中的列。</span><span class="sxs-lookup"><span data-stu-id="1507e-183">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="1507e-184">你还可以指定架构名称，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1507e-184">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="1507e-185">将每个层次结构一个表映射 (TPH) 继承</span><span class="sxs-lookup"><span data-stu-id="1507e-185">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="1507e-186">在 TPH 映射方案中，继承层次结构中的所有类型都映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="1507e-186">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="1507e-187">鉴别器列用于标识每行的类型。</span><span class="sxs-lookup"><span data-stu-id="1507e-187">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="1507e-188">在 Code First 创建模型时，TPH 是参与继承层次结构的类型的默认策略。</span><span class="sxs-lookup"><span data-stu-id="1507e-188">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="1507e-189">默认情况下，鉴别器列将添加到名称为 "鉴别器" 的表中，层次结构中的每个类型的 CLR 类型名称将用于鉴别器值。</span><span class="sxs-lookup"><span data-stu-id="1507e-189">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="1507e-190">您可以使用 Fluent API 修改默认行为。</span><span class="sxs-lookup"><span data-stu-id="1507e-190">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="1507e-191">将每种类型一个表映射 (TPT) 继承</span><span class="sxs-lookup"><span data-stu-id="1507e-191">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="1507e-192">在 TPT 映射方案中，所有类型都映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="1507e-192">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="1507e-193">仅属于某个基类型或派生类型的属性存储在映射到该类型的一个表中。</span><span class="sxs-lookup"><span data-stu-id="1507e-193">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="1507e-194">映射到派生类型的表还存储将派生表与基表联接的外键。</span><span class="sxs-lookup"><span data-stu-id="1507e-194">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="1507e-195">将每个具体的表类映射 (TPC) 继承</span><span class="sxs-lookup"><span data-stu-id="1507e-195">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="1507e-196">在 TPC 映射方案中，层次结构中的所有非抽象类型都将映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="1507e-196">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="1507e-197">映射到派生类的表与映射到数据库中的基类的表没有关系。</span><span class="sxs-lookup"><span data-stu-id="1507e-197">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="1507e-198">类的所有属性（包括继承的属性）都映射到对应表的列。</span><span class="sxs-lookup"><span data-stu-id="1507e-198">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="1507e-199">调用 MapInheritedProperties 方法来配置每个派生类型。</span><span class="sxs-lookup"><span data-stu-id="1507e-199">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="1507e-200">对于派生类，MapInheritedProperties 将从基类继承的所有属性重新映射到表中的新列。</span><span class="sxs-lookup"><span data-stu-id="1507e-200">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="1507e-201">请注意，由于参与了 TPC 继承层次结构的表不共享主键，因此，如果数据库生成的值具有相同的标识种子，则在映射到子类的表中插入时，将会出现重复的实体键。</span><span class="sxs-lookup"><span data-stu-id="1507e-201">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="1507e-202">若要解决此问题，可以为每个表指定不同的初始种子值，或者在 primary key 属性上关闭 "标识"。</span><span class="sxs-lookup"><span data-stu-id="1507e-202">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="1507e-203">当使用 Code First 时，标识是整数键属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="1507e-203">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="1507e-204">将实体类型的属性映射到数据库中的多个表 (实体拆分) </span><span class="sxs-lookup"><span data-stu-id="1507e-204">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="1507e-205">实体拆分允许将某个实体类型的属性分布到多个表中。</span><span class="sxs-lookup"><span data-stu-id="1507e-205">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="1507e-206">在下面的示例中，部门实体拆分为两个表：部门和 DepartmentDetails。</span><span class="sxs-lookup"><span data-stu-id="1507e-206">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="1507e-207">实体拆分对 Map 方法使用多个调用，以将属性的子集映射到特定的表。</span><span class="sxs-lookup"><span data-stu-id="1507e-207">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="1507e-208">将多个实体类型映射到数据库中的一个表 (表拆分) </span><span class="sxs-lookup"><span data-stu-id="1507e-208">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="1507e-209">下面的示例将共享主键的两个实体类型映射到一个表。</span><span class="sxs-lookup"><span data-stu-id="1507e-209">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="1507e-210">将实体类型映射到插入/更新/删除存储过程 (EF6 向前) </span><span class="sxs-lookup"><span data-stu-id="1507e-210">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="1507e-211">从 EF6 开始，可以将实体映射为使用存储过程来执行插入更新和删除操作。</span><span class="sxs-lookup"><span data-stu-id="1507e-211">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="1507e-212">有关更多详细信息，请参阅 [Code First 插入/更新/删除存储过程](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)。</span><span class="sxs-lookup"><span data-stu-id="1507e-212">For more details see, [Code First Insert/Update/Delete Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="1507e-213">示例中使用的模型</span><span class="sxs-lookup"><span data-stu-id="1507e-213">Model Used in Samples</span></span>  

<span data-ttu-id="1507e-214">以下 Code First 模型用于此页上的示例。</span><span class="sxs-lookup"><span data-stu-id="1507e-214">The following Code First model is used for the samples on this page.</span></span>  

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
