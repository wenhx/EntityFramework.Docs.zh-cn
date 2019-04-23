---
title: 关系的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 9ef1a9269fc99f5b27a81c11a161ed5f9d74180d
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929932"
---
# <a name="relationships"></a><span data-ttu-id="1cd54-102">关系</span><span class="sxs-lookup"><span data-stu-id="1cd54-102">Relationships</span></span>

<span data-ttu-id="1cd54-103">关系定义了两个实体互相关联起来。</span><span class="sxs-lookup"><span data-stu-id="1cd54-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="1cd54-104">在关系数据库中，这表示通过外键约束。</span><span class="sxs-lookup"><span data-stu-id="1cd54-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="1cd54-105">大部分这篇文章中的示例使用一个对多关系来演示概念。</span><span class="sxs-lookup"><span data-stu-id="1cd54-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="1cd54-106">有关一对一和多对多关系的示例，请参阅[其他关系模式](#other-relationship-patterns)本文末尾部分。</span><span class="sxs-lookup"><span data-stu-id="1cd54-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="1cd54-107">术语的定义</span><span class="sxs-lookup"><span data-stu-id="1cd54-107">Definition of Terms</span></span>

<span data-ttu-id="1cd54-108">提供多种用于描述关系的术语</span><span class="sxs-lookup"><span data-stu-id="1cd54-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="1cd54-109">**依赖的实体：** 这是包含外键属性的实体。</span><span class="sxs-lookup"><span data-stu-id="1cd54-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="1cd54-110">有时称为 child 的关系。</span><span class="sxs-lookup"><span data-stu-id="1cd54-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="1cd54-111">**主体实体：** 这是包含主/备用键属性的实体。</span><span class="sxs-lookup"><span data-stu-id="1cd54-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="1cd54-112">有时称为 parent 的关系。</span><span class="sxs-lookup"><span data-stu-id="1cd54-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="1cd54-113">**外键：** 中用于存储主体与相关实体的键属性的值的相关实体属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="1cd54-114">**主体密钥：** 唯一标识的主体实体的属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="1cd54-115">这可能是 primary key 或备用键。</span><span class="sxs-lookup"><span data-stu-id="1cd54-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="1cd54-116">**导航属性：** 包含对相关实体引用的主体和/或相关实体上定义的属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="1cd54-117">**集合导航属性：** 一个导航属性，包含对多个相关实体的引用。</span><span class="sxs-lookup"><span data-stu-id="1cd54-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="1cd54-118">**引用导航属性：** 一个导航属性，保存对单个相关实体的引用。</span><span class="sxs-lookup"><span data-stu-id="1cd54-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="1cd54-119">**反转导航属性：** 在讨论特定导航属性时，此术语指关系另一端的导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="1cd54-120">以下代码列表显示之间的一个对多关系`Blog`和 `Post`</span><span class="sxs-lookup"><span data-stu-id="1cd54-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="1cd54-121">`Post` 是依赖实体</span><span class="sxs-lookup"><span data-stu-id="1cd54-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="1cd54-122">`Blog` 是主体实体</span><span class="sxs-lookup"><span data-stu-id="1cd54-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="1cd54-123">`Post.BlogId` 是外键</span><span class="sxs-lookup"><span data-stu-id="1cd54-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="1cd54-124">`Blog.BlogId` 是主体键（在这种情况下是主键，而不是备用键）</span><span class="sxs-lookup"><span data-stu-id="1cd54-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="1cd54-125">`Post.Blog` 是引用导航属性</span><span class="sxs-lookup"><span data-stu-id="1cd54-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="1cd54-126">`Blog.Posts` 是一个集合导航属性</span><span class="sxs-lookup"><span data-stu-id="1cd54-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="1cd54-127">`Post.Blog` 是的反转导航属性`Blog.Posts`（反之亦然）</span><span class="sxs-lookup"><span data-stu-id="1cd54-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="1cd54-128">约定</span><span class="sxs-lookup"><span data-stu-id="1cd54-128">Conventions</span></span>

<span data-ttu-id="1cd54-129">按照约定，当发现类型上有导航属性时，将创建关系。</span><span class="sxs-lookup"><span data-stu-id="1cd54-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="1cd54-130">如果属性指向的类型不能由当前的数据库提供程序映射为标量类型，则该属性视为一个导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="1cd54-131">由约定发现的关系会始终为目标主体实体的主键。</span><span class="sxs-lookup"><span data-stu-id="1cd54-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="1cd54-132">若要针对备用键，必须使用 Fluent API 执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="1cd54-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="1cd54-133">完全定义的关系</span><span class="sxs-lookup"><span data-stu-id="1cd54-133">Fully Defined Relationships</span></span>

<span data-ttu-id="1cd54-134">关系的最常见模式是有的关系和依赖实体类中定义的外键属性的两端定义导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="1cd54-135">如果两个类型之间找到一对导航属性，则它们将被配置为同一关系的反转导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="1cd54-136">如果依赖实体包含名为的属性`<primary key property name>`， `<navigation property name><primary key property name>`，或`<principal entity name><primary key property name>`然后它将配置为外键。</span><span class="sxs-lookup"><span data-stu-id="1cd54-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="1cd54-137">如果有多个定义两个类型之间的导航属性 (也就是说，多个指向彼此的导航的非重复对)，则将按约定创建任何关系并将需要手动将它们配置为标识如何向上导航属性对。</span><span class="sxs-lookup"><span data-stu-id="1cd54-137">If there are multiple navigation properties defined between two types (that is, more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="1cd54-138">没有外键属性</span><span class="sxs-lookup"><span data-stu-id="1cd54-138">No Foreign Key Property</span></span>

<span data-ttu-id="1cd54-139">尽管建议以具有从属实体类中定义的外键属性，则不需要。</span><span class="sxs-lookup"><span data-stu-id="1cd54-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="1cd54-140">如果不找到任何外键属性，则将具有名称引入卷影的外键属性`<navigation property name><principal key property name>`(请参阅[隐藏属性](shadow-properties.md)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="1cd54-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="1cd54-141">单一导航属性</span><span class="sxs-lookup"><span data-stu-id="1cd54-141">Single Navigation Property</span></span>

<span data-ttu-id="1cd54-142">包括一个导航属性 （没有反导航栏中，并没有外键属性） 就足以通过约定定义了一个关系。</span><span class="sxs-lookup"><span data-stu-id="1cd54-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="1cd54-143">您还可以单个导航属性和外键属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="1cd54-144">级联删除</span><span class="sxs-lookup"><span data-stu-id="1cd54-144">Cascade Delete</span></span>

<span data-ttu-id="1cd54-145">按照约定，级联删除将设置为*Cascade*的所需的关系并*ClientSetNull*的可选关系。</span><span class="sxs-lookup"><span data-stu-id="1cd54-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="1cd54-146">*级联*意味着依赖实体也会被删除。</span><span class="sxs-lookup"><span data-stu-id="1cd54-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="1cd54-147">*ClientSetNull*将保持未加载到内存中的依赖实体的方式保持不变，必须手动删除，或更新为指向有效的主体实体。</span><span class="sxs-lookup"><span data-stu-id="1cd54-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="1cd54-148">对于加载到内存的实体，EF Core将尝试将外键属性设置为 null。</span><span class="sxs-lookup"><span data-stu-id="1cd54-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="1cd54-149">请参阅[必需和可选关系](#required-and-optional-relationships)部分，了解必需和可选关系之间的差异。</span><span class="sxs-lookup"><span data-stu-id="1cd54-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="1cd54-150">请参阅[级联删除](../saving/cascade-delete.md)更多详细信息的不同删除行为和约定所使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="1cd54-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="1cd54-151">数据注释</span><span class="sxs-lookup"><span data-stu-id="1cd54-151">Data Annotations</span></span>

<span data-ttu-id="1cd54-152">有两个可用于配置关系的数据注释`[ForeignKey]`和`[InverseProperty]`。</span><span class="sxs-lookup"><span data-stu-id="1cd54-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span> <span data-ttu-id="1cd54-153">这些是可用在`System.ComponentModel.DataAnnotations.Schema`命名空间。</span><span class="sxs-lookup"><span data-stu-id="1cd54-153">These are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="1cd54-154">[ForeignKey]</span><span class="sxs-lookup"><span data-stu-id="1cd54-154">[ForeignKey]</span></span>

<span data-ttu-id="1cd54-155">可以使用数据注释来配置哪些属性应用作给定关系外键属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-155">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="1cd54-156">这通常是外键属性不由约定发现时。</span><span class="sxs-lookup"><span data-stu-id="1cd54-156">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="1cd54-157">`[ForeignKey]`可以在关系中这两个导航属性上放置批注。</span><span class="sxs-lookup"><span data-stu-id="1cd54-157">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="1cd54-158">它不需要继续依赖实体类中的导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-158">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="1cd54-159">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="1cd54-159">[InverseProperty]</span></span>

<span data-ttu-id="1cd54-160">数据注释可用于配置如何对从属和主体实体的导航属性配对。</span><span class="sxs-lookup"><span data-stu-id="1cd54-160">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="1cd54-161">这通常是多个对两个实体类型之间的导航属性时。</span><span class="sxs-lookup"><span data-stu-id="1cd54-161">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a><span data-ttu-id="1cd54-162">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1cd54-162">Fluent API</span></span>

<span data-ttu-id="1cd54-163">若要配置关系 Fluent API 中，您首先确定构成关系的导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-163">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="1cd54-164">`HasOne` 或`HasMany`标识您在开始配置的实体类型上的导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-164">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="1cd54-165">然后链接到调用`WithOne`或`WithMany`来标识反导航。</span><span class="sxs-lookup"><span data-stu-id="1cd54-165">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="1cd54-166">`HasOne`/`WithOne` 用于引用导航属性和`HasMany` / `WithMany`用于集合导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-166">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a><span data-ttu-id="1cd54-167">单一导航属性</span><span class="sxs-lookup"><span data-stu-id="1cd54-167">Single Navigation Property</span></span>

<span data-ttu-id="1cd54-168">如果只有一个导航属性，则无参数的重载`WithOne`和`WithMany`。</span><span class="sxs-lookup"><span data-stu-id="1cd54-168">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="1cd54-169">这表示没有从概念上讲一个引用或集合上的另一端的关系，但没有包含在实体类中没有导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-169">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="1cd54-170">外键</span><span class="sxs-lookup"><span data-stu-id="1cd54-170">Foreign Key</span></span>

<span data-ttu-id="1cd54-171">可以使用 Fluent API 配置哪些属性应用作给定关系外键属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-171">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?highlight=17)]

<span data-ttu-id="1cd54-172">以下代码列表演示如何配置复合外键。</span><span class="sxs-lookup"><span data-stu-id="1cd54-172">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="1cd54-173">可以使用的字符串重载`HasForeignKey(...)`若要配置卷影属性作为外键 (请参阅[隐藏属性](shadow-properties.md)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="1cd54-173">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="1cd54-174">我们建议显式将卷影属性添加到模型，然后才能使用它作为外键 （作为如下所示）。</span><span class="sxs-lookup"><span data-stu-id="1cd54-174">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a><span data-ttu-id="1cd54-175">主体键</span><span class="sxs-lookup"><span data-stu-id="1cd54-175">Principal Key</span></span>

<span data-ttu-id="1cd54-176">如果你想要引用主键之外的属性的外键，可以使用 Fluent API 来配置此关系的主体键属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-176">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="1cd54-177">为该主体的密钥将自动配置的属性是作为备用的密钥的安装程序 (请参阅[备用键](alternate-keys.md)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="1cd54-177">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/PrincipalKey.cs?highlight=11)] -->
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

<span data-ttu-id="1cd54-178">以下代码列表演示如何配置主体的复合键。</span><span class="sxs-lookup"><span data-stu-id="1cd54-178">The following code listing shows how to configure a composite principal key.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
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
> <span data-ttu-id="1cd54-179">指定主体的键属性的顺序必须与将外键指定的顺序匹配。</span><span class="sxs-lookup"><span data-stu-id="1cd54-179">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="1cd54-180">必需和可选的关系</span><span class="sxs-lookup"><span data-stu-id="1cd54-180">Required and Optional Relationships</span></span>

<span data-ttu-id="1cd54-181">可以使用 Fluent API 来配置此关系为必需或可选。</span><span class="sxs-lookup"><span data-stu-id="1cd54-181">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="1cd54-182">这最终控制外键属性是必需还是可选。</span><span class="sxs-lookup"><span data-stu-id="1cd54-182">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="1cd54-183">使用卷影状态外键时，这是最有用。</span><span class="sxs-lookup"><span data-stu-id="1cd54-183">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="1cd54-184">如果您有实体类中的外键属性，根据外键属性是必需还是可选确定关系的 requiredness (请参阅[必需和可选属性](required-optional.md)的详细信息信息）。</span><span class="sxs-lookup"><span data-stu-id="1cd54-184">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/Required.cs?highlight=11)] -->
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

### <a name="cascade-delete"></a><span data-ttu-id="1cd54-185">级联删除</span><span class="sxs-lookup"><span data-stu-id="1cd54-185">Cascade Delete</span></span>

<span data-ttu-id="1cd54-186">可以使用 Fluent API 来显式配置给定关系的级联删除行为。</span><span class="sxs-lookup"><span data-stu-id="1cd54-186">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="1cd54-187">请参阅[级联删除](../saving/cascade-delete.md)上每个选项的详细讨论的保存的数据部分。</span><span class="sxs-lookup"><span data-stu-id="1cd54-187">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CascadeDelete.cs?highlight=11)] -->
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

## <a name="other-relationship-patterns"></a><span data-ttu-id="1cd54-188">其他关系模式</span><span class="sxs-lookup"><span data-stu-id="1cd54-188">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="1cd54-189">一对一</span><span class="sxs-lookup"><span data-stu-id="1cd54-189">One-to-one</span></span>

<span data-ttu-id="1cd54-190">一对一关系两端具有引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="1cd54-190">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="1cd54-191">它们遵循相同的约定作为一个对多关系，但在外键属性，以确保只有一个依赖于与每个主体上引入了唯一索引。</span><span class="sxs-lookup"><span data-stu-id="1cd54-191">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/Relationships/OneToOne.cs?highlight=6,15,16)] -->
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
> <span data-ttu-id="1cd54-192">EF 将选择一个要为基于它能够检测到外键属性的依赖项的实体。</span><span class="sxs-lookup"><span data-stu-id="1cd54-192">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="1cd54-193">如果错误的实体选择作为依赖项，可以使用 Fluent API 要更正此问题。</span><span class="sxs-lookup"><span data-stu-id="1cd54-193">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="1cd54-194">如果使用 Fluent API 配置此关系，则使用`HasOne`和`WithOne`方法。</span><span class="sxs-lookup"><span data-stu-id="1cd54-194">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="1cd54-195">配置需要指定依赖实体类型中的外键时请注意，泛型参数提供给`HasForeignKey`下面的列表中。</span><span class="sxs-lookup"><span data-stu-id="1cd54-195">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="1cd54-196">一个对多关系中很明显，引用导航的实体与相关和具有集合是的主体。</span><span class="sxs-lookup"><span data-stu-id="1cd54-196">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="1cd54-197">但这并不是一对一关系-因此中显式定义的需要。</span><span class="sxs-lookup"><span data-stu-id="1cd54-197">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/OneToOne.cs?highlight=11)] -->
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

### <a name="many-to-many"></a><span data-ttu-id="1cd54-198">多对多</span><span class="sxs-lookup"><span data-stu-id="1cd54-198">Many-to-many</span></span>

<span data-ttu-id="1cd54-199">尚不支持多对多关系，而不需要的实体类来表示联接表。</span><span class="sxs-lookup"><span data-stu-id="1cd54-199">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="1cd54-200">但是，您可以通过包括联接表和映射两个单独一个对多关系的实体类表示多对多关系。</span><span class="sxs-lookup"><span data-stu-id="1cd54-200">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(t => new { t.PostId, t.TagId });

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
