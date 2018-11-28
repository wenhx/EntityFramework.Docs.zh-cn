---
title: Fluent API 的配置和映射属性和类型的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
ms.openlocfilehash: 7371cc99142ccf8fc6bea237d7d58d1e67fcecec
ms.sourcegitcommit: 75f8a179ac9a70ad390fc7ab2a6c5e714e701b8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52339798"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="c0261-102">Fluent API-配置和映射属性和类型</span><span class="sxs-lookup"><span data-stu-id="c0261-102">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="c0261-103">使用 Entity Framework Code First 时的默认行为是将您的 POCO 类映射到表使用一系列融入 EF 的约定。</span><span class="sxs-lookup"><span data-stu-id="c0261-103">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="c0261-104">有时，但是，您无法或不希望遵循这些约定，并且需要将实体映射到以外的约定的规定。</span><span class="sxs-lookup"><span data-stu-id="c0261-104">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="c0261-105">有两种主要方式，你可以配置以外的约定，即使用 EF[批注](~/ef6/modeling/code-first/data-annotations.md)或 EFs fluent API。</span><span class="sxs-lookup"><span data-stu-id="c0261-105">There are two main ways you can configure EF to use something other than conventions, namely [annotations](~/ef6/modeling/code-first/data-annotations.md) or EFs fluent API.</span></span> <span data-ttu-id="c0261-106">批注仅涵盖 fluent API 功能的子集，因此不能使用批注来实现的映射方案。</span><span class="sxs-lookup"><span data-stu-id="c0261-106">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="c0261-107">本文旨在演示如何使用 fluent API 配置属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-107">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="c0261-108">Code first fluent API 最常访问通过重写[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)方法在派生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)。</span><span class="sxs-lookup"><span data-stu-id="c0261-108">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="c0261-109">以下示例旨在演示如何执行各种任务使用 fluent api，并允许您将代码复制和自定义它以满足您的模型，如果你想要看到它们可用作与该模型的则本文结尾处提供。</span><span class="sxs-lookup"><span data-stu-id="c0261-109">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="c0261-110">模型范围内的设置</span><span class="sxs-lookup"><span data-stu-id="c0261-110">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="c0261-111">默认架构 (EF6 及更高版本)</span><span class="sxs-lookup"><span data-stu-id="c0261-111">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="c0261-112">从 EF6 您可以使用 HasDefaultSchema 方法上 DbModelBuilder 来指定要使用的所有表、 存储的过程等的数据库架构。此默认设置将被重写的任何对象的显式配置为不同的架构。</span><span class="sxs-lookup"><span data-stu-id="c0261-112">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="c0261-113">自定义约定 (EF6 及更高版本)</span><span class="sxs-lookup"><span data-stu-id="c0261-113">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="c0261-114">从 EF6，您可以创建您自己的约定来补充包括在 Code First 的开始。</span><span class="sxs-lookup"><span data-stu-id="c0261-114">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="c0261-115">有关更多详细信息，请参阅[自定义代码优先约定](~/ef6/modeling/code-first/conventions/custom.md)。</span><span class="sxs-lookup"><span data-stu-id="c0261-115">For more details, see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="c0261-116">属性映射</span><span class="sxs-lookup"><span data-stu-id="c0261-116">Property Mapping</span></span>  

<span data-ttu-id="c0261-117">[属性](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)方法用于配置每个属性属于实体或复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-117">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="c0261-118">属性方法用于获取给定属性的配置对象。</span><span class="sxs-lookup"><span data-stu-id="c0261-118">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="c0261-119">是特定于要配置; 的类型上的配置对象选项例如，IsUnicode 是仅适用于字符串属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-119">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="c0261-120">配置为主键</span><span class="sxs-lookup"><span data-stu-id="c0261-120">Configuring a Primary Key</span></span>  

<span data-ttu-id="c0261-121">为主键的实体框架约定是：</span><span class="sxs-lookup"><span data-stu-id="c0261-121">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="c0261-122">类定义其名称为"ID"或"Id"的属性</span><span class="sxs-lookup"><span data-stu-id="c0261-122">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="c0261-123">或类名称后跟"ID"或"Id"</span><span class="sxs-lookup"><span data-stu-id="c0261-123">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="c0261-124">若要显式设置的属性是主键，可以使用 HasKey 方法。</span><span class="sxs-lookup"><span data-stu-id="c0261-124">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="c0261-125">在以下示例中，HasKey 方法用于在 OfficeAssignment 类型配置 InstructorID 为主键。</span><span class="sxs-lookup"><span data-stu-id="c0261-125">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="c0261-126">配置组合主键</span><span class="sxs-lookup"><span data-stu-id="c0261-126">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="c0261-127">下面的示例配置为部门类型的复合主键的 DepartmentID 和名称属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-127">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="c0261-128">对于数值主键关闭标识切换</span><span class="sxs-lookup"><span data-stu-id="c0261-128">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="c0261-129">下面的示例将 DepartmentID 属性设置为 System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None 以指示将由数据库生成值。</span><span class="sxs-lookup"><span data-stu-id="c0261-129">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="c0261-130">在属性上指定的最大长度</span><span class="sxs-lookup"><span data-stu-id="c0261-130">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="c0261-131">在以下示例中，Name 属性应为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="c0261-131">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="c0261-132">如果将值设置超过 50 个字符，则会收到[DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx)异常。</span><span class="sxs-lookup"><span data-stu-id="c0261-132">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="c0261-133">如果 Code First 从此模型创建数据库它还将名称列的最大长度为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="c0261-133">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="c0261-134">配置要为所需的属性</span><span class="sxs-lookup"><span data-stu-id="c0261-134">Configuring the Property to be Required</span></span>  

<span data-ttu-id="c0261-135">在以下示例中，该名称属性是必需的。</span><span class="sxs-lookup"><span data-stu-id="c0261-135">In the following example, the Name property is required.</span></span> <span data-ttu-id="c0261-136">如果不指定名称，则会 DbEntityValidationException 异常。</span><span class="sxs-lookup"><span data-stu-id="c0261-136">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="c0261-137">如果第一个代码从此模型创建数据库然后用于存储此属性的列通常是不可以为 null。</span><span class="sxs-lookup"><span data-stu-id="c0261-137">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="c0261-138">在某些情况下它不可能要为不可为 null，即使该属性是必需的数据库中的列。</span><span class="sxs-lookup"><span data-stu-id="c0261-138">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="c0261-139">例如，当对多个类型使用 TPH 继承策略数据存储在单个表。</span><span class="sxs-lookup"><span data-stu-id="c0261-139">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="c0261-140">如果派生的类型包括必需的属性列不能成为不可为 null 因为层次结构中的不是所有类型都将都具有此属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-140">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="c0261-141">上一个或多个属性配置索引</span><span class="sxs-lookup"><span data-stu-id="c0261-141">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="c0261-142">**EF6.1 及更高版本仅**-Entity Framework 6.1 中引入了索引属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-142">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="c0261-143">如果您使用的是早期版本不适用于此部分中的信息。</span><span class="sxs-lookup"><span data-stu-id="c0261-143">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="c0261-144">创建索引并不本机支持通过 Fluent API，但可以使用支持的**IndexAttribute**通过 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="c0261-144">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="c0261-145">索引属性由包括然后转换为更高版本中管道的数据库中的索引对模型的模型注释处理。</span><span class="sxs-lookup"><span data-stu-id="c0261-145">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="c0261-146">您可以手动添加这些相同使用 Fluent API 的批注。</span><span class="sxs-lookup"><span data-stu-id="c0261-146">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="c0261-147">若要执行此操作的最简单方法是创建的实例**IndexAttribute** ，其中包含用于新索引的所有设置。</span><span class="sxs-lookup"><span data-stu-id="c0261-147">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="c0261-148">然后可以创建的实例**IndexAnnotation**即会将转换的 EF 特定类型**IndexAttribute**到可以存储在 EF 模型的模型批注的设置。</span><span class="sxs-lookup"><span data-stu-id="c0261-148">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="c0261-149">这些然后传递给**HasColumnAnnotation** Fluent api 中，指定名称的方法**索引**的批注。</span><span class="sxs-lookup"><span data-stu-id="c0261-149">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="c0261-150">有关中可用的设置的完整列表**IndexAttribute**，请参阅*索引*一部分[Code First 数据批注](~/ef6/modeling/code-first/data-annotations.md)。</span><span class="sxs-lookup"><span data-stu-id="c0261-150">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](~/ef6/modeling/code-first/data-annotations.md).</span></span> <span data-ttu-id="c0261-151">这包括自定义索引名称、 创建唯一索引，以及创建多列索引。</span><span class="sxs-lookup"><span data-stu-id="c0261-151">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="c0261-152">可以通过传递数组的单个属性上指定多个索引批注**IndexAttribute**的构造函数**IndexAnnotation**。</span><span class="sxs-lookup"><span data-stu-id="c0261-152">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="c0261-153">指定不将映射到数据库中的列的 CLR 属性</span><span class="sxs-lookup"><span data-stu-id="c0261-153">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="c0261-154">下面的示例演示如何指定 CLR 类型上的属性未映射到数据库中的列。</span><span class="sxs-lookup"><span data-stu-id="c0261-154">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="c0261-155">映射到数据库中的特定列的 CLR 属性</span><span class="sxs-lookup"><span data-stu-id="c0261-155">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="c0261-156">下面的示例将名称 CLR 属性映射到 DepartmentName 数据库列。</span><span class="sxs-lookup"><span data-stu-id="c0261-156">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="c0261-157">重命名模型中未定义的外键</span><span class="sxs-lookup"><span data-stu-id="c0261-157">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="c0261-158">如果您选择不在 CLR 类型上定义外键，但想要在数据库中指定它应具有什么名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c0261-158">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="c0261-159">配置是否为字符串属性支持 Unicode 内容</span><span class="sxs-lookup"><span data-stu-id="c0261-159">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="c0261-160">默认情况下字符串是 Unicode (SQL Server 中的 nvarchar)。</span><span class="sxs-lookup"><span data-stu-id="c0261-160">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="c0261-161">IsUnicode 方法可用于指定的 varchar 类型应为字符串。</span><span class="sxs-lookup"><span data-stu-id="c0261-161">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="c0261-162">配置数据库列的数据类型</span><span class="sxs-lookup"><span data-stu-id="c0261-162">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="c0261-163">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)方法使映射到相同的基本类型的不同表示形式。</span><span class="sxs-lookup"><span data-stu-id="c0261-163">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="c0261-164">使用此方法不会启用你执行任何转换的数据在运行时。</span><span class="sxs-lookup"><span data-stu-id="c0261-164">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="c0261-165">请注意 IsUnicode 是 varchar、 设置列的首选的方式，这是因为它与数据库无关。</span><span class="sxs-lookup"><span data-stu-id="c0261-165">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="c0261-166">复杂类型上配置属性</span><span class="sxs-lookup"><span data-stu-id="c0261-166">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="c0261-167">有两种方法来配置上的复杂类型的标量属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-167">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="c0261-168">可以对 ComplexTypeConfiguration 调用属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-168">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="c0261-169">此外可以使用点表示法访问复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-169">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="c0261-170">配置要用作开放式并发标记的属性</span><span class="sxs-lookup"><span data-stu-id="c0261-170">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="c0261-171">若要指定实体中的某一属性表示并发标记，可以使用 ConcurrencyCheck 属性或 IsConcurrencyToken 方法。</span><span class="sxs-lookup"><span data-stu-id="c0261-171">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="c0261-172">IsRowVersion 方法还可用于将属性配置为在数据库中的行版本。</span><span class="sxs-lookup"><span data-stu-id="c0261-172">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="c0261-173">属性设置为行版本会自动将其配置为进行开放式并发标记。</span><span class="sxs-lookup"><span data-stu-id="c0261-173">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="c0261-174">类型映射</span><span class="sxs-lookup"><span data-stu-id="c0261-174">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="c0261-175">指定类一种复杂类型</span><span class="sxs-lookup"><span data-stu-id="c0261-175">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="c0261-176">按照约定，指定没有主键的类型被视为一种复杂类型。</span><span class="sxs-lookup"><span data-stu-id="c0261-176">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="c0261-177">有某些情况下，其中第一个代码将不会检测复杂类型 （例如，如果您具有一个名为 ID 属性，但您并不意味着它是主键）。</span><span class="sxs-lookup"><span data-stu-id="c0261-177">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="c0261-178">在这种情况下，将使用 fluent API 来显式指定类型是复杂类型。</span><span class="sxs-lookup"><span data-stu-id="c0261-178">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="c0261-179">指定不将 CLR 实体类型映射到数据库中的表</span><span class="sxs-lookup"><span data-stu-id="c0261-179">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="c0261-180">下面的示例演示如何从映射到数据库中的表中排除的 CLR 类型。</span><span class="sxs-lookup"><span data-stu-id="c0261-180">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="c0261-181">一个实体类型映射到数据库中的特定表</span><span class="sxs-lookup"><span data-stu-id="c0261-181">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="c0261-182">部门的所有属性将都映射到一个名为 t_ 部门表中的列。</span><span class="sxs-lookup"><span data-stu-id="c0261-182">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="c0261-183">此外可以指定架构名称如下：</span><span class="sxs-lookup"><span data-stu-id="c0261-183">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="c0261-184">映射的每个层次结构一张表 (TPH) 继承</span><span class="sxs-lookup"><span data-stu-id="c0261-184">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="c0261-185">TPH 映射方案中，在继承层次结构中的所有类型都映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="c0261-185">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="c0261-186">鉴别器列用于标识每行的类型。</span><span class="sxs-lookup"><span data-stu-id="c0261-186">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="c0261-187">当使用 Code First 创建模型，TPH 是参与继承层次结构的类型的默认策略。</span><span class="sxs-lookup"><span data-stu-id="c0261-187">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="c0261-188">默认情况下，鉴别器列添加到具有名称"Discriminator"表和层次结构中的每种类型的 CLR 类型名称用于鉴别器值。</span><span class="sxs-lookup"><span data-stu-id="c0261-188">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="c0261-189">可以通过使用 fluent API 来修改默认行为。</span><span class="sxs-lookup"><span data-stu-id="c0261-189">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="c0261-190">映射的每种类型一个表 (TPT) 继承</span><span class="sxs-lookup"><span data-stu-id="c0261-190">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="c0261-191">TPT 映射方案中，在所有类型都映射到单独的表。</span><span class="sxs-lookup"><span data-stu-id="c0261-191">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="c0261-192">仅属于某个基类型或派生类型的属性存储在映射到该类型的一个表中。</span><span class="sxs-lookup"><span data-stu-id="c0261-192">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="c0261-193">用于派生类型的映射的表还存储将派生表与基础表的外键。</span><span class="sxs-lookup"><span data-stu-id="c0261-193">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="c0261-194">映射表每个具体类 (TPC) 继承</span><span class="sxs-lookup"><span data-stu-id="c0261-194">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="c0261-195">在 TPC 映射方案中，层次结构中的所有非抽象类型映射到单独的表。</span><span class="sxs-lookup"><span data-stu-id="c0261-195">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="c0261-196">将映射到派生类的表格具有映射到数据库中的基类的表没有关系。</span><span class="sxs-lookup"><span data-stu-id="c0261-196">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="c0261-197">类，包括继承的属性的所有属性都映射到相应的表的列。</span><span class="sxs-lookup"><span data-stu-id="c0261-197">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="c0261-198">调用 MapInheritedProperties 方法来配置每个派生的类型。</span><span class="sxs-lookup"><span data-stu-id="c0261-198">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="c0261-199">MapInheritedProperties 将重新映射到的表的派生类中的新列从基类继承的所有属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-199">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="c0261-200">请注意，因为不共享参与 TPC 继承层次结构的表有主键将重复的实体键时如果具有生成的数据库值与相同的标识种子将映射到子类的表中插入。</span><span class="sxs-lookup"><span data-stu-id="c0261-200">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="c0261-201">若要解决此问题，您可以指定每个表的不同的初始种子值，也可以关闭主键属性上的标识。</span><span class="sxs-lookup"><span data-stu-id="c0261-201">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="c0261-202">使用 Code First 时，标识是整数键属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="c0261-202">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="c0261-203">实体类型的属性映射到数据库 （实体拆分） 中的多个表</span><span class="sxs-lookup"><span data-stu-id="c0261-203">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="c0261-204">实体拆分允许要分布在多个表的实体类型的属性。</span><span class="sxs-lookup"><span data-stu-id="c0261-204">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="c0261-205">在以下示例中，在部门实体拆分为两个表： 部门和 DepartmentDetails。</span><span class="sxs-lookup"><span data-stu-id="c0261-205">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="c0261-206">实体拆分使用 Map 方法对多个调用映射到特定的表属性的子集。</span><span class="sxs-lookup"><span data-stu-id="c0261-206">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="c0261-207">多个实体类型映射到数据库 （表拆分） 中的一个表</span><span class="sxs-lookup"><span data-stu-id="c0261-207">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="c0261-208">下面的示例将共享到一个表的主键的两个实体类型映射。</span><span class="sxs-lookup"><span data-stu-id="c0261-208">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="c0261-209">一个实体类型映射到 Insert/Update/Delete 存储过程 (EF6 及更高版本)</span><span class="sxs-lookup"><span data-stu-id="c0261-209">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="c0261-210">与 EF6 开始，可以将映射实体将存储的过程用于插入更新和删除。</span><span class="sxs-lookup"><span data-stu-id="c0261-210">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="c0261-211">有关更多详细信息，请参阅[代码第一个 Insert/Update/Delete 存储过程](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)。</span><span class="sxs-lookup"><span data-stu-id="c0261-211">For more details see, [Code First Insert/Update/Delete Stored Procedures](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="c0261-212">示例中所用的模型</span><span class="sxs-lookup"><span data-stu-id="c0261-212">Model Used in Samples</span></span>  

<span data-ttu-id="c0261-213">下面的代码优先模型用于在此页上的示例。</span><span class="sxs-lookup"><span data-stu-id="c0261-213">The following Code First model is used for the samples on this page.</span></span>  

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
