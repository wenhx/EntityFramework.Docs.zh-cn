---
title: 关系-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 1e9c62bec47263ef452c7ac425a0bb446f9371d8
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197651"
---
# <a name="relationships"></a><span data-ttu-id="c8425-102">关系</span><span class="sxs-lookup"><span data-stu-id="c8425-102">Relationships</span></span>

<span data-ttu-id="c8425-103">关系定义两个实体之间的关系。</span><span class="sxs-lookup"><span data-stu-id="c8425-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="c8425-104">在关系数据库中，这由外键约束表示。</span><span class="sxs-lookup"><span data-stu-id="c8425-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="c8425-105">本文中的大多数示例都使用一对多关系来演示概念。</span><span class="sxs-lookup"><span data-stu-id="c8425-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="c8425-106">有关一对一关系和多对多关系的示例，请参阅文章末尾的[其他关系模式](#other-relationship-patterns)部分。</span><span class="sxs-lookup"><span data-stu-id="c8425-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="c8425-107">术语定义</span><span class="sxs-lookup"><span data-stu-id="c8425-107">Definition of Terms</span></span>

<span data-ttu-id="c8425-108">有许多术语用于描述关系</span><span class="sxs-lookup"><span data-stu-id="c8425-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="c8425-109">**相关实体：** 这是包含外键属性的实体。</span><span class="sxs-lookup"><span data-stu-id="c8425-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="c8425-110">有时称为关系的 "子级"。</span><span class="sxs-lookup"><span data-stu-id="c8425-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="c8425-111">**主体实体：** 这是包含主/备用键属性的实体。</span><span class="sxs-lookup"><span data-stu-id="c8425-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="c8425-112">有时称为关系的 "父项"。</span><span class="sxs-lookup"><span data-stu-id="c8425-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="c8425-113">**外键：** 依赖实体中的属性，用于存储与实体相关的主体键属性的值。</span><span class="sxs-lookup"><span data-stu-id="c8425-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="c8425-114">**主体密钥：** 唯一标识主体实体的属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="c8425-115">这可能是主键或备用密钥。</span><span class="sxs-lookup"><span data-stu-id="c8425-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="c8425-116">**导航属性：** 在主体和/或从属实体上定义的属性，该属性包含对相关实体的引用。</span><span class="sxs-lookup"><span data-stu-id="c8425-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="c8425-117">**集合导航属性：** 一个导航属性，其中包含对多个相关实体的引用。</span><span class="sxs-lookup"><span data-stu-id="c8425-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="c8425-118">**引用导航属性：** 保存对单个相关实体的引用的导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="c8425-119">**反向导航属性：** 讨论特定导航属性时，此术语是指关系另一端的导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="c8425-120">下面的代码列表显示了与之间`Blog`的一对多关系`Post`</span><span class="sxs-lookup"><span data-stu-id="c8425-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="c8425-121">`Post`是依赖实体</span><span class="sxs-lookup"><span data-stu-id="c8425-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="c8425-122">`Blog`是主体实体</span><span class="sxs-lookup"><span data-stu-id="c8425-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="c8425-123">`Post.BlogId`为外键</span><span class="sxs-lookup"><span data-stu-id="c8425-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="c8425-124">`Blog.BlogId` 是主体键（在这种情况下是主键，而不是备用键）</span><span class="sxs-lookup"><span data-stu-id="c8425-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="c8425-125">`Post.Blog`是一个引用导航属性</span><span class="sxs-lookup"><span data-stu-id="c8425-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="c8425-126">`Blog.Posts`是集合导航属性</span><span class="sxs-lookup"><span data-stu-id="c8425-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="c8425-127">`Post.Blog`是的`Blog.Posts`反向导航属性（反之亦然）</span><span class="sxs-lookup"><span data-stu-id="c8425-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="c8425-128">约定</span><span class="sxs-lookup"><span data-stu-id="c8425-128">Conventions</span></span>

<span data-ttu-id="c8425-129">按照约定，当发现类型上有导航属性时，将创建关系。</span><span class="sxs-lookup"><span data-stu-id="c8425-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="c8425-130">如果属性指向的类型不能由当前的数据库提供程序映射为标量类型，则该属性视为一个导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="c8425-131">按约定发现的关系将始终以主体实体的主键为目标。</span><span class="sxs-lookup"><span data-stu-id="c8425-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="c8425-132">若要以备用密钥为目标，则必须使用熟知的 API 执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="c8425-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="c8425-133">完全定义的关系</span><span class="sxs-lookup"><span data-stu-id="c8425-133">Fully Defined Relationships</span></span>

<span data-ttu-id="c8425-134">关系最常见的模式是在关系两端定义导航属性，在依赖实体类中定义外键属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="c8425-135">如果在两个类型之间找到一对导航属性，则这些属性将配置为同一关系的反向导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="c8425-136">如果依赖实体包含名为`<primary key property name>`、 `<navigation property name><primary key property name>`或`<principal entity name><primary key property name>`的属性，则该属性将被配置为外键。</span><span class="sxs-lookup"><span data-stu-id="c8425-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="c8425-137">如果有多个在两种类型之间定义的导航属性（即，多个指向彼此的不同类型的导航），则不会按约定创建任何关系，你将需要手动对其进行配置以确定导航属性配对。</span><span class="sxs-lookup"><span data-stu-id="c8425-137">If there are multiple navigation properties defined between two types (that is, more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="c8425-138">无外键属性</span><span class="sxs-lookup"><span data-stu-id="c8425-138">No Foreign Key Property</span></span>

<span data-ttu-id="c8425-139">尽管建议在依赖实体类中定义外键属性，但这并不是必需的。</span><span class="sxs-lookup"><span data-stu-id="c8425-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="c8425-140">如果未找到外键属性，则会以该名称`<navigation property name><principal key property name>`引入阴影外键属性（有关详细信息，请参阅[影子属性](shadow-properties.md)）。</span><span class="sxs-lookup"><span data-stu-id="c8425-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="c8425-141">单个导航属性</span><span class="sxs-lookup"><span data-stu-id="c8425-141">Single Navigation Property</span></span>

<span data-ttu-id="c8425-142">只包含一个导航属性（无反向导航，没有外键属性）就足以具有约定定义的关系。</span><span class="sxs-lookup"><span data-stu-id="c8425-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="c8425-143">还可以有一个导航属性和一个外键属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="c8425-144">级联删除</span><span class="sxs-lookup"><span data-stu-id="c8425-144">Cascade Delete</span></span>

<span data-ttu-id="c8425-145">按照约定，级联删除将对所需的关系和*ClientSetNull*设置为*cascade* ，以实现可选关系。</span><span class="sxs-lookup"><span data-stu-id="c8425-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="c8425-146">*Cascade*表示也会删除依赖实体。</span><span class="sxs-lookup"><span data-stu-id="c8425-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="c8425-147">*ClientSetNull*表示未加载到内存中的依赖实体将保持不变，必须手动删除，或将其更新为指向有效的主体实体。</span><span class="sxs-lookup"><span data-stu-id="c8425-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="c8425-148">对于加载到内存中的实体，EF Core 将尝试将外键属性设置为 null。</span><span class="sxs-lookup"><span data-stu-id="c8425-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="c8425-149">请参阅 required[和 optional](#required-and-optional-relationships)关系部分，了解必需和可选关系之间的差异。</span><span class="sxs-lookup"><span data-stu-id="c8425-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="c8425-150">有关不同的删除行为和约定使用的默认值的详细信息，请参阅[级联删除](../saving/cascade-delete.md)。</span><span class="sxs-lookup"><span data-stu-id="c8425-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c8425-151">数据注释</span><span class="sxs-lookup"><span data-stu-id="c8425-151">Data Annotations</span></span>

<span data-ttu-id="c8425-152">可以使用两个数据批注来配置关系`[ForeignKey]`和。 `[InverseProperty]`</span><span class="sxs-lookup"><span data-stu-id="c8425-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span> <span data-ttu-id="c8425-153">`System.ComponentModel.DataAnnotations.Schema`命名空间中提供了这些项。</span><span class="sxs-lookup"><span data-stu-id="c8425-153">These are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="c8425-154">ForeignKey</span><span class="sxs-lookup"><span data-stu-id="c8425-154">[ForeignKey]</span></span>

<span data-ttu-id="c8425-155">您可以使用数据批注来配置应用作给定关系的外键属性的属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-155">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="c8425-156">通常，当不按约定发现外键属性时，会执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c8425-156">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="c8425-157">`[ForeignKey]`批注可放置在关系中的任一导航属性上。</span><span class="sxs-lookup"><span data-stu-id="c8425-157">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="c8425-158">它不需要在依赖实体类中定位导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-158">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="c8425-159">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="c8425-159">[InverseProperty]</span></span>

<span data-ttu-id="c8425-160">您可以使用数据批注来配置依赖项和主体实体上的导航属性如何配对。</span><span class="sxs-lookup"><span data-stu-id="c8425-160">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="c8425-161">这通常在两个实体类型之间存在多个导航属性对时执行。</span><span class="sxs-lookup"><span data-stu-id="c8425-161">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a><span data-ttu-id="c8425-162">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c8425-162">Fluent API</span></span>

<span data-ttu-id="c8425-163">若要在熟知的 API 中配置关系，请首先标识构成关系的导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-163">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="c8425-164">`HasOne`或`HasMany`标识要开始配置的实体类型上的导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-164">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="c8425-165">然后，将调用链接到`WithOne`或`WithMany`以标识反向导航。</span><span class="sxs-lookup"><span data-stu-id="c8425-165">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="c8425-166">`HasOne`/`WithOne`用于引用导航属性， `HasMany` / `WithMany`用于集合导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-166">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a><span data-ttu-id="c8425-167">单个导航属性</span><span class="sxs-lookup"><span data-stu-id="c8425-167">Single Navigation Property</span></span>

<span data-ttu-id="c8425-168">如果只有一个导航属性，则和`WithOne` `WithMany`的无参数重载。</span><span class="sxs-lookup"><span data-stu-id="c8425-168">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="c8425-169">这表示在概念上，关系的另一端有一个引用或集合，但实体类中不包含导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-169">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="c8425-170">外键</span><span class="sxs-lookup"><span data-stu-id="c8425-170">Foreign Key</span></span>

<span data-ttu-id="c8425-171">您可以使用 "熟知 API" 来配置应用作给定关系的外键属性的属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-171">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

<span data-ttu-id="c8425-172">下面的代码列表演示如何配置复合外键。</span><span class="sxs-lookup"><span data-stu-id="c8425-172">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="c8425-173">您可以使用的`HasForeignKey(...)`字符串重载将影子属性配置为外键（有关详细信息，请参阅[影子属性](shadow-properties.md)）。</span><span class="sxs-lookup"><span data-stu-id="c8425-173">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="c8425-174">建议先将影子属性显式添加到模型，然后再将其用作外键（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="c8425-174">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a><span data-ttu-id="c8425-175">无导航属性</span><span class="sxs-lookup"><span data-stu-id="c8425-175">Without Navigation Property</span></span>

<span data-ttu-id="c8425-176">不一定需要提供导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-176">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="c8425-177">您可以直接在关系的一端提供外键。</span><span class="sxs-lookup"><span data-stu-id="c8425-177">You can simply provide a Foreign Key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a><span data-ttu-id="c8425-178">主体密钥</span><span class="sxs-lookup"><span data-stu-id="c8425-178">Principal Key</span></span>

<span data-ttu-id="c8425-179">如果你希望外键引用主键之外的属性，则可以使用熟知的 API 来配置关系的主体键属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-179">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="c8425-180">配置为主体密钥的属性将自动设置为备用密钥（有关详细信息，请参阅[备用](alternate-keys.md)密钥）。</span><span class="sxs-lookup"><span data-stu-id="c8425-180">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => s.CarLicensePlate)
            .HasPrincipalKey(c => c.LicensePlate);
    }
}

public class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

<span data-ttu-id="c8425-181">下面的代码列表演示如何配置复合主体键。</span><span class="sxs-lookup"><span data-stu-id="c8425-181">The following code listing shows how to configure a composite principal key.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => new { s.CarState, s.CarLicensePlate })
            .HasPrincipalKey(c => new { c.State, c.LicensePlate });
    }
}

public class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarState { get; set; }
    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

> [!WARNING]  
> <span data-ttu-id="c8425-182">指定主体键属性的顺序必须与为外键指定这些属性的顺序一致。</span><span class="sxs-lookup"><span data-stu-id="c8425-182">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="c8425-183">必需和可选的关系</span><span class="sxs-lookup"><span data-stu-id="c8425-183">Required and Optional Relationships</span></span>

<span data-ttu-id="c8425-184">您可以使用熟知的 API 来配置关系是必需的还是可选的。</span><span class="sxs-lookup"><span data-stu-id="c8425-184">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="c8425-185">最终，这会控制外键属性是必需的还是可选的。</span><span class="sxs-lookup"><span data-stu-id="c8425-185">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="c8425-186">当使用阴影状态外键时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="c8425-186">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="c8425-187">如果实体类中具有外键属性，则关系的 requiredness 取决于外键属性是必需还是可选（有关详细信息，请参阅[必需和可选属性](required-optional.md)）。</span><span class="sxs-lookup"><span data-stu-id="c8425-187">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

### <a name="cascade-delete"></a><span data-ttu-id="c8425-188">级联删除</span><span class="sxs-lookup"><span data-stu-id="c8425-188">Cascade Delete</span></span>

<span data-ttu-id="c8425-189">您可以使用熟知的 API 显式配置给定关系的级联删除行为。</span><span class="sxs-lookup"><span data-stu-id="c8425-189">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="c8425-190">有关每个选项的详细讨论，请参阅保存数据部分的[级联删除](../saving/cascade-delete.md)。</span><span class="sxs-lookup"><span data-stu-id="c8425-190">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .OnDelete(DeleteBehavior.Cascade);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="other-relationship-patterns"></a><span data-ttu-id="c8425-191">其他关系模式</span><span class="sxs-lookup"><span data-stu-id="c8425-191">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="c8425-192">一对一</span><span class="sxs-lookup"><span data-stu-id="c8425-192">One-to-one</span></span>

<span data-ttu-id="c8425-193">一对多关系在两侧都有一个引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="c8425-193">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="c8425-194">它们遵循与一对多关系相同的约定，但在外键属性上引入了唯一索引，以确保只有一个依赖项与每个主体相关。</span><span class="sxs-lookup"><span data-stu-id="c8425-194">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Relationships/OneToOne.cs?highlight=6,15,16)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

> [!NOTE]  
> <span data-ttu-id="c8425-195">EF 会根据其检测外键属性的能力，选择其中一个实体作为依赖项。</span><span class="sxs-lookup"><span data-stu-id="c8425-195">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="c8425-196">如果选择了错误的实体作为依赖项，则可以使用熟知的 API 来更正此问题。</span><span class="sxs-lookup"><span data-stu-id="c8425-196">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="c8425-197">使用 "流畅" API 配置关系时，请使用`HasOne`和`WithOne`方法。</span><span class="sxs-lookup"><span data-stu-id="c8425-197">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="c8425-198">配置外键时，需要指定依赖实体类型-请注意以下列表`HasForeignKey`中提供的泛型参数。</span><span class="sxs-lookup"><span data-stu-id="c8425-198">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="c8425-199">在一对多关系中，可以清楚地表明具有引用导航的实体是依赖项，并且具有集合的实体是主体。</span><span class="sxs-lookup"><span data-stu-id="c8425-199">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="c8425-200">但这并不是一对一的关系，因此需要显式定义它。</span><span class="sxs-lookup"><span data-stu-id="c8425-200">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<BlogImage> BlogImages { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasOne(p => p.BlogImage)
            .WithOne(i => i.Blog)
            .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogForeignKey { get; set; }
    public Blog Blog { get; set; }
}
```

### <a name="many-to-many"></a><span data-ttu-id="c8425-201">多对多</span><span class="sxs-lookup"><span data-stu-id="c8425-201">Many-to-many</span></span>

<span data-ttu-id="c8425-202">目前尚不支持多对多关系，没有实体类来表示联接表。</span><span class="sxs-lookup"><span data-stu-id="c8425-202">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="c8425-203">但是，您可以通过包含联接表的实体类并映射两个不同的一对多关系，来表示多对多关系。</span><span class="sxs-lookup"><span data-stu-id="c8425-203">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(pt => new { pt.PostId, pt.TagId });

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Post)
            .WithMany(p => p.PostTags)
            .HasForeignKey(pt => pt.PostId);

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Tag)
            .WithMany(t => t.PostTags)
            .HasForeignKey(pt => pt.TagId);
    }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class Tag
{
    public string TagId { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class PostTag
{
    public int PostId { get; set; }
    public Post Post { get; set; }

    public string TagId { get; set; }
    public Tag Tag { get; set; }
}
```
