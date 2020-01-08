---
title: 关系-EF Core
description: 如何在使用 Entity Framework Core 时配置实体类型之间的关系
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 6b3e0636bfa266b78baafe1b6e318c9707294560
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502183"
---
# <a name="relationships"></a><span data-ttu-id="0a863-103">关系</span><span class="sxs-lookup"><span data-stu-id="0a863-103">Relationships</span></span>

<span data-ttu-id="0a863-104">关系定义两个实体之间的关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="0a863-105">在关系数据库中，这由外键约束表示。</span><span class="sxs-lookup"><span data-stu-id="0a863-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="0a863-106">本文中的大多数示例都使用一对多关系来演示概念。</span><span class="sxs-lookup"><span data-stu-id="0a863-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="0a863-107">有关一对一关系和多对多关系的示例，请参阅文章末尾的[其他关系模式](#other-relationship-patterns)部分。</span><span class="sxs-lookup"><span data-stu-id="0a863-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="0a863-108">术语定义</span><span class="sxs-lookup"><span data-stu-id="0a863-108">Definition of terms</span></span>

<span data-ttu-id="0a863-109">有许多术语用于描述关系</span><span class="sxs-lookup"><span data-stu-id="0a863-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="0a863-110">**相关实体：** 这是包含外键属性的实体。</span><span class="sxs-lookup"><span data-stu-id="0a863-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="0a863-111">有时称为关系的 "子级"。</span><span class="sxs-lookup"><span data-stu-id="0a863-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="0a863-112">**主体实体：** 这是包含主/备用键属性的实体。</span><span class="sxs-lookup"><span data-stu-id="0a863-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="0a863-113">有时称为关系的 "父项"。</span><span class="sxs-lookup"><span data-stu-id="0a863-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="0a863-114">**外键：** 用于存储相关实体的主体键值的依赖实体中的属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-114">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="0a863-115">**主体密钥：** 唯一标识主体实体的属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-115">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="0a863-116">这可能是主键或备用密钥。</span><span class="sxs-lookup"><span data-stu-id="0a863-116">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="0a863-117">**导航属性：** 在主体和/或从属实体上定义的属性，该属性引用相关实体。</span><span class="sxs-lookup"><span data-stu-id="0a863-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="0a863-118">**集合导航属性：** 一个导航属性，其中包含对多个相关实体的引用。</span><span class="sxs-lookup"><span data-stu-id="0a863-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="0a863-119">**引用导航属性：** 保存对单个相关实体的引用的导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="0a863-120">**反向导航属性：** 讨论特定导航属性时，此术语是指关系另一端的导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="0a863-121">**自引用关系：** 依赖关系和主体实体类型相同的关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="0a863-122">下面的代码显示 `Blog` 与 `Post` 之间的一对多关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="0a863-123">`Post` 是依赖实体</span><span class="sxs-lookup"><span data-stu-id="0a863-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="0a863-124">`Blog` 是主体实体</span><span class="sxs-lookup"><span data-stu-id="0a863-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="0a863-125">`Post.BlogId` 为外键</span><span class="sxs-lookup"><span data-stu-id="0a863-125">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="0a863-126">`Blog.BlogId` 是主体键（在这种情况下是主键，而不是备用键）</span><span class="sxs-lookup"><span data-stu-id="0a863-126">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="0a863-127">`Post.Blog` 是一个引用导航属性</span><span class="sxs-lookup"><span data-stu-id="0a863-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="0a863-128">`Blog.Posts` 是集合导航属性</span><span class="sxs-lookup"><span data-stu-id="0a863-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="0a863-129">`Post.Blog` 是 `Blog.Posts` 的反向导航属性（反之亦然）</span><span class="sxs-lookup"><span data-stu-id="0a863-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="0a863-130">约定</span><span class="sxs-lookup"><span data-stu-id="0a863-130">Conventions</span></span>

<span data-ttu-id="0a863-131">默认情况下，当在某个类型上发现导航属性时，将创建一个关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="0a863-132">如果属性指向的类型不能由当前的数据库提供程序映射为标量类型，则该属性视为一个导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="0a863-133">按约定发现的关系将始终以主体实体的主键为目标。</span><span class="sxs-lookup"><span data-stu-id="0a863-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="0a863-134">若要以备用密钥为目标，则必须使用熟知的 API 执行其他配置。</span><span class="sxs-lookup"><span data-stu-id="0a863-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="0a863-135">完全定义的关系</span><span class="sxs-lookup"><span data-stu-id="0a863-135">Fully defined relationships</span></span>

<span data-ttu-id="0a863-136">关系最常见的模式是在关系两端定义导航属性，在依赖实体类中定义外键属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="0a863-137">如果在两个类型之间找到一对导航属性，则这些属性将配置为同一关系的反向导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="0a863-138">如果依赖实体包含名称与其中一种模式相匹配的属性，则该属性将被配置为外键：</span><span class="sxs-lookup"><span data-stu-id="0a863-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="0a863-139">在此示例中，突出显示的属性将用于配置关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="0a863-140">如果属性为主键，或者为与主体键不兼容的类型，则不会将其配置为外键。</span><span class="sxs-lookup"><span data-stu-id="0a863-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="0a863-141">在 EF Core 3.0 之前，名为与主体键属性完全相同的属性[也与外键匹配](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="0a863-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="0a863-142">无外键属性</span><span class="sxs-lookup"><span data-stu-id="0a863-142">No foreign key property</span></span>

<span data-ttu-id="0a863-143">尽管建议在依赖实体类中定义外键属性，但这并不是必需的。</span><span class="sxs-lookup"><span data-stu-id="0a863-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="0a863-144">如果未找到外键属性，则会引入名称为 `<navigation property name><principal key property name>` 或 `<principal entity name><principal key property name>` 的[阴影外键属性](shadow-properties.md)（如果依赖类型上没有导航）。</span><span class="sxs-lookup"><span data-stu-id="0a863-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="0a863-145">在此示例中，隐藏外键是 `BlogId` 的，因为预先计算导航名称将是冗余的。</span><span class="sxs-lookup"><span data-stu-id="0a863-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="0a863-146">如果已存在具有相同名称的属性，则会以数字作为后缀的阴影属性名称。</span><span class="sxs-lookup"><span data-stu-id="0a863-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="0a863-147">单个导航属性</span><span class="sxs-lookup"><span data-stu-id="0a863-147">Single navigation property</span></span>

<span data-ttu-id="0a863-148">只包含一个导航属性（无反向导航，没有外键属性）就足以具有约定定义的关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="0a863-149">还可以有一个导航属性和一个外键属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="0a863-150">限制</span><span class="sxs-lookup"><span data-stu-id="0a863-150">Limitations</span></span>

<span data-ttu-id="0a863-151">如果有多个在两种类型之间定义的导航属性（即，不仅仅是一对相互指向的导航属性），则导航属性表示的关系是不明确的。</span><span class="sxs-lookup"><span data-stu-id="0a863-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="0a863-152">你将需要手动对其进行配置以解决歧义。</span><span class="sxs-lookup"><span data-stu-id="0a863-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="0a863-153">级联删除</span><span class="sxs-lookup"><span data-stu-id="0a863-153">Cascade delete</span></span>

<span data-ttu-id="0a863-154">按照约定，级联删除将对所需的关系和*ClientSetNull*设置为*cascade* ，以实现可选关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="0a863-155">*Cascade*表示也会删除依赖实体。</span><span class="sxs-lookup"><span data-stu-id="0a863-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="0a863-156">*ClientSetNull*表示未加载到内存中的依赖实体将保持不变，必须手动删除，或将其更新为指向有效的主体实体。</span><span class="sxs-lookup"><span data-stu-id="0a863-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="0a863-157">对于加载到内存中的实体，EF Core 将尝试将外键属性设置为 null。</span><span class="sxs-lookup"><span data-stu-id="0a863-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="0a863-158">请参阅 required[和 optional](#required-and-optional-relationships)关系部分，了解必需和可选关系之间的差异。</span><span class="sxs-lookup"><span data-stu-id="0a863-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="0a863-159">有关不同的删除行为和约定使用的默认值的详细信息，请参阅[级联删除](../saving/cascade-delete.md)。</span><span class="sxs-lookup"><span data-stu-id="0a863-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="0a863-160">手动配置</span><span class="sxs-lookup"><span data-stu-id="0a863-160">Manual configuration</span></span>

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="0a863-161">熟知 API</span><span class="sxs-lookup"><span data-stu-id="0a863-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="0a863-162">若要在熟知的 API 中配置关系，请首先标识构成关系的导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="0a863-163">`HasOne` 或 `HasMany` 标识正在开始配置的实体类型上的导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="0a863-164">然后，将调用链接到 `WithOne` 或 `WithMany` 来标识反向导航。</span><span class="sxs-lookup"><span data-stu-id="0a863-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="0a863-165">`HasOne`/`WithOne` 用于引用导航属性，`HasMany`/用于集合导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="0a863-166">数据批注</span><span class="sxs-lookup"><span data-stu-id="0a863-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="0a863-167">您可以使用数据批注来配置依赖项和主体实体上的导航属性如何配对。</span><span class="sxs-lookup"><span data-stu-id="0a863-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="0a863-168">这通常在两个实体类型之间存在多个导航属性对时执行。</span><span class="sxs-lookup"><span data-stu-id="0a863-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="0a863-169">只能在依赖实体上的属性上使用 [Required] 来影响关系的 requiredness。</span><span class="sxs-lookup"><span data-stu-id="0a863-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="0a863-170">[必需] 在主体实体的导航中通常会忽略，但这可能会导致实体成为依赖实体。</span><span class="sxs-lookup"><span data-stu-id="0a863-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="0a863-171">数据批注 `[ForeignKey]` 和 `[InverseProperty]` 在 `System.ComponentModel.DataAnnotations.Schema` 命名空间中可用。</span><span class="sxs-lookup"><span data-stu-id="0a863-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="0a863-172">`[Required]` 在 `System.ComponentModel.DataAnnotations` 命名空间中可用。</span><span class="sxs-lookup"><span data-stu-id="0a863-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="0a863-173">单个导航属性</span><span class="sxs-lookup"><span data-stu-id="0a863-173">Single navigation property</span></span>

<span data-ttu-id="0a863-174">如果只有一个导航属性，则 `WithOne` 和 `WithMany`有无参数重载。</span><span class="sxs-lookup"><span data-stu-id="0a863-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="0a863-175">这表示在概念上，关系的另一端有一个引用或集合，但实体类中不包含导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

### <a name="foreign-key"></a><span data-ttu-id="0a863-176">外键</span><span class="sxs-lookup"><span data-stu-id="0a863-176">Foreign key</span></span>

#### <a name="fluent-api-simple-keytabfluent-api-simple-key"></a>[<span data-ttu-id="0a863-177">熟知 API （简单密钥）</span><span class="sxs-lookup"><span data-stu-id="0a863-177">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="0a863-178">您可以使用熟知的 API 来配置应用作给定关系的外键属性的属性：</span><span class="sxs-lookup"><span data-stu-id="0a863-178">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-keytabfluent-api-composite-key"></a>[<span data-ttu-id="0a863-179">熟知 API （组合键）</span><span class="sxs-lookup"><span data-stu-id="0a863-179">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="0a863-180">您可以使用熟知的 API 来配置哪些属性应用作给定关系的复合外键属性：</span><span class="sxs-lookup"><span data-stu-id="0a863-180">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-keytabdata-annotations-simple-key"></a>[<span data-ttu-id="0a863-181">数据批注（简单键）</span><span class="sxs-lookup"><span data-stu-id="0a863-181">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="0a863-182">您可以使用数据批注来配置应用作给定关系的外键属性的属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-182">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="0a863-183">通常，当不按约定发现外键属性时，会执行此操作：</span><span class="sxs-lookup"><span data-stu-id="0a863-183">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="0a863-184">可以将 `[ForeignKey]` 批注放置在关系中的任一导航属性上。</span><span class="sxs-lookup"><span data-stu-id="0a863-184">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="0a863-185">它不需要在依赖实体类中定位导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-185">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="0a863-186">使用导航属性 `[ForeignKey]` 指定的属性不需要存在于依赖类型中。</span><span class="sxs-lookup"><span data-stu-id="0a863-186">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist the the dependent type.</span></span> <span data-ttu-id="0a863-187">在这种情况下，将使用指定的名称创建阴影外键。</span><span class="sxs-lookup"><span data-stu-id="0a863-187">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="0a863-188">影子外键</span><span class="sxs-lookup"><span data-stu-id="0a863-188">Shadow foreign key</span></span>

<span data-ttu-id="0a863-189">您可以使用 `HasForeignKey(...)` 的字符串重载将影子属性配置为外键（有关详细信息，请参阅[影子属性](shadow-properties.md)）。</span><span class="sxs-lookup"><span data-stu-id="0a863-189">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="0a863-190">建议先将影子属性显式添加到模型，然后再将其用作外键（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="0a863-190">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="0a863-191">Foreign key 约束名称</span><span class="sxs-lookup"><span data-stu-id="0a863-191">Foreign key constraint name</span></span>

<span data-ttu-id="0a863-192">按照约定，以关系数据库为目标时，外键约束将命名 FK_<dependent type name> _<principal type name>_ <foreign key property name>。</span><span class="sxs-lookup"><span data-stu-id="0a863-192">By convention, when targeting a relational database, foreign key constraints are named FK_<dependent type name>_<principal type name>_<foreign key property name>.</span></span> <span data-ttu-id="0a863-193">对于复合外键 <foreign key property name> 成为外键属性名称的以下划线分隔的列表。</span><span class="sxs-lookup"><span data-stu-id="0a863-193">For composite foreign keys <foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="0a863-194">你还可以配置约束名称，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0a863-194">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="0a863-195">无导航属性</span><span class="sxs-lookup"><span data-stu-id="0a863-195">Without navigation property</span></span>

<span data-ttu-id="0a863-196">不一定需要提供导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-196">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="0a863-197">您可以直接在关系的一端提供外键。</span><span class="sxs-lookup"><span data-stu-id="0a863-197">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="0a863-198">主体密钥</span><span class="sxs-lookup"><span data-stu-id="0a863-198">Principal key</span></span>

<span data-ttu-id="0a863-199">如果你希望外键引用主键之外的属性，则可以使用熟知的 API 来配置关系的主体键属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-199">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="0a863-200">配置为主体密钥的属性将自动设置为[备用密钥](alternate-keys.md)。</span><span class="sxs-lookup"><span data-stu-id="0a863-200">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

#### <a name="simple-keytabsimple-key"></a>[<span data-ttu-id="0a863-201">简单键</span><span class="sxs-lookup"><span data-stu-id="0a863-201">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-keytabcomposite-key"></a>[<span data-ttu-id="0a863-202">组合键</span><span class="sxs-lookup"><span data-stu-id="0a863-202">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="0a863-203">指定主体键属性的顺序必须与为外键指定这些属性的顺序一致。</span><span class="sxs-lookup"><span data-stu-id="0a863-203">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="0a863-204">必需和可选的关系</span><span class="sxs-lookup"><span data-stu-id="0a863-204">Required and optional relationships</span></span>

<span data-ttu-id="0a863-205">您可以使用熟知的 API 来配置关系是必需的还是可选的。</span><span class="sxs-lookup"><span data-stu-id="0a863-205">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="0a863-206">最终，这会控制外键属性是必需的还是可选的。</span><span class="sxs-lookup"><span data-stu-id="0a863-206">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="0a863-207">当使用阴影状态外键时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="0a863-207">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="0a863-208">如果实体类中具有外键属性，则关系的 requiredness 取决于外键属性是必需还是可选（有关详细信息，请参阅[必需和可选属性](required-optional.md)）。</span><span class="sxs-lookup"><span data-stu-id="0a863-208">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="0a863-209">调用 `IsRequired(false)` 还会使外键属性为可选，除非已对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="0a863-209">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="0a863-210">级联删除</span><span class="sxs-lookup"><span data-stu-id="0a863-210">Cascade delete</span></span>

<span data-ttu-id="0a863-211">您可以使用熟知的 API 显式配置给定关系的级联删除行为。</span><span class="sxs-lookup"><span data-stu-id="0a863-211">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="0a863-212">有关每个选项的详细讨论，请参阅[级联删除](../saving/cascade-delete.md)。</span><span class="sxs-lookup"><span data-stu-id="0a863-212">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="0a863-213">其他关系模式</span><span class="sxs-lookup"><span data-stu-id="0a863-213">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="0a863-214">一对一</span><span class="sxs-lookup"><span data-stu-id="0a863-214">One-to-one</span></span>

<span data-ttu-id="0a863-215">一对多关系在两侧都有一个引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="0a863-215">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="0a863-216">它们遵循与一对多关系相同的约定，但在外键属性上引入了唯一索引，以确保只有一个依赖项与每个主体相关。</span><span class="sxs-lookup"><span data-stu-id="0a863-216">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="0a863-217">EF 会根据其检测外键属性的能力，选择其中一个实体作为依赖项。</span><span class="sxs-lookup"><span data-stu-id="0a863-217">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="0a863-218">如果选择了错误的实体作为依赖项，则可以使用熟知的 API 来更正此问题。</span><span class="sxs-lookup"><span data-stu-id="0a863-218">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="0a863-219">使用熟知 API 配置关系时，可使用 `HasOne` 和 `WithOne` 方法。</span><span class="sxs-lookup"><span data-stu-id="0a863-219">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="0a863-220">配置外键时，需要指定依赖实体类型-请注意以下列表中 `HasForeignKey` 提供的泛型参数。</span><span class="sxs-lookup"><span data-stu-id="0a863-220">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="0a863-221">在一对多关系中，可以清楚地表明具有引用导航的实体是依赖项，并且具有集合的实体是主体。</span><span class="sxs-lookup"><span data-stu-id="0a863-221">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="0a863-222">但这并不是一对一的关系，因此需要显式定义它。</span><span class="sxs-lookup"><span data-stu-id="0a863-222">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="0a863-223">多对多</span><span class="sxs-lookup"><span data-stu-id="0a863-223">Many-to-many</span></span>

<span data-ttu-id="0a863-224">目前尚不支持多对多关系，没有实体类来表示联接表。</span><span class="sxs-lookup"><span data-stu-id="0a863-224">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="0a863-225">但是，您可以通过包含联接表的实体类并映射两个不同的一对多关系，来表示多对多关系。</span><span class="sxs-lookup"><span data-stu-id="0a863-225">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
