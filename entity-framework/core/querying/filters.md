---
title: 全局查询筛选器 - EF Core
description: 将 Entity Framework Core 与全局查询筛选器结合使用来筛选结果
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616387"
---
# <a name="global-query-filters"></a><span data-ttu-id="1da87-103">全局查询筛选器</span><span class="sxs-lookup"><span data-stu-id="1da87-103">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="1da87-104">EF Core 2.0 中已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="1da87-104">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="1da87-105">全局查询筛选器是应用于元数据模型（通常为 OnModelCreating）中的实体类型的 LINQ 查询谓词。</span><span class="sxs-lookup"><span data-stu-id="1da87-105">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="1da87-106">查询谓词即通常传递给 LINQ Where 查询运算符的布尔表达式。</span><span class="sxs-lookup"><span data-stu-id="1da87-106">A query predicate is a boolean expression typically passed to the LINQ *Where* query operator.</span></span>  <span data-ttu-id="1da87-107">EF Core 会自动将此类筛选器应用于涉及这些实体类型的任何 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="1da87-107">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="1da87-108">EF Core 还将其应用于使用 Include 或导航属性进行间接引用的实体类型。</span><span class="sxs-lookup"><span data-stu-id="1da87-108">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="1da87-109">此功能的一些常见应用如下：</span><span class="sxs-lookup"><span data-stu-id="1da87-109">Some common applications of this feature are:</span></span>

* <span data-ttu-id="1da87-110">**软删除** - 实体类型定义“IsDeleted”属性。</span><span class="sxs-lookup"><span data-stu-id="1da87-110">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="1da87-111">**多租户** - 实体类型定义“TenantId”属性。</span><span class="sxs-lookup"><span data-stu-id="1da87-111">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="1da87-112">示例</span><span class="sxs-lookup"><span data-stu-id="1da87-112">Example</span></span>

<span data-ttu-id="1da87-113">下面的示例演示了如何使用全局查询筛选器在简单的博客模型中实现多租户和软删除查询行为。</span><span class="sxs-lookup"><span data-stu-id="1da87-113">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="1da87-114">可在 GitHub 上查看[多租户示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)和[使用导航的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)。</span><span class="sxs-lookup"><span data-stu-id="1da87-114">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span>

<span data-ttu-id="1da87-115">首先，定义实体：</span><span class="sxs-lookup"><span data-stu-id="1da87-115">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="1da87-116">请注意 _Blog_ 实体上的 _tenantId_ 字段的声明。</span><span class="sxs-lookup"><span data-stu-id="1da87-116">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="1da87-117">此字段用于将每个 Blog 实例与特定租户相关联。</span><span class="sxs-lookup"><span data-stu-id="1da87-117">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="1da87-118">同时在 _Post_ 实体类型上定义了 _IsDeleted_ 属性。</span><span class="sxs-lookup"><span data-stu-id="1da87-118">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="1da87-119">此属性用于跟踪某个 Post 实例是否已“软删除”。</span><span class="sxs-lookup"><span data-stu-id="1da87-119">This property is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="1da87-120">也就是说，实例只是被标记为已删除，而非真正删除了基础数据。</span><span class="sxs-lookup"><span data-stu-id="1da87-120">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="1da87-121">接下来，使用 `HasQueryFilter` API 在 _OnModelCreating_ 中配置查询筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-121">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="1da87-122">传递给 _HasQueryFilter_ 调用的谓词表达式将立即自动应用于这些类型的任何 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="1da87-122">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="1da87-123">请注意 DbContext 实例级别字段的使用：`_tenantId` 用于设置当前租户。</span><span class="sxs-lookup"><span data-stu-id="1da87-123">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="1da87-124">模型级筛选器将使用正确上下文实例（即执行查询的实例）中的值。</span><span class="sxs-lookup"><span data-stu-id="1da87-124">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="1da87-125">目前不能在同一个实体中定义多个查询筛选器，只会应用最后一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-125">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="1da87-126">但是，可以使用逻辑 _AND_ 运算符（[C# 中为 `&&`](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)）定义含有多种条件的单个筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-126">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="1da87-127">使用导航</span><span class="sxs-lookup"><span data-stu-id="1da87-127">Use of navigations</span></span>

<span data-ttu-id="1da87-128">还可以在定义全局查询筛选器时使用导航。</span><span class="sxs-lookup"><span data-stu-id="1da87-128">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="1da87-129">在查询筛选器中使用导航将导致以递归方式应用查询筛选器这一结果。</span><span class="sxs-lookup"><span data-stu-id="1da87-129">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="1da87-130">当 EF Core 展开查询筛选器中使用的导航时，它还会应用在引用的实体上定义的查询筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-130">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="1da87-131">目前，EF Core 不会检测全局查询筛选器定义中的循环，因此需在定义它们时小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="1da87-131">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="1da87-132">如果指定错误，这些循环可能在查询转换期间导致无限循环。</span><span class="sxs-lookup"><span data-stu-id="1da87-132">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="1da87-133">使用必需的导航访问具有查询筛选器的实体</span><span class="sxs-lookup"><span data-stu-id="1da87-133">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="1da87-134">如果使用必需的导航访问定义了全局查询筛选器的实体，则可能导致意外结果。</span><span class="sxs-lookup"><span data-stu-id="1da87-134">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="1da87-135">必需的导航要求始终存在相关实体。</span><span class="sxs-lookup"><span data-stu-id="1da87-135">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="1da87-136">如果查询筛选器筛选出了必需的相关实体，则父实体不会出现在结果中。</span><span class="sxs-lookup"><span data-stu-id="1da87-136">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="1da87-137">因此，结果中的元素数量可能会低于预期。</span><span class="sxs-lookup"><span data-stu-id="1da87-137">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="1da87-138">为了说明此问题，我们可使用上面指定的 `Blog` 和 `Post` 实体，同时使用下面的 OnModelCreating 方法：</span><span class="sxs-lookup"><span data-stu-id="1da87-138">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="1da87-139">可通过以下数据对模型进行种子设定：</span><span class="sxs-lookup"><span data-stu-id="1da87-139">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="1da87-140">执行两个查询时，可能会观察到此问题：</span><span class="sxs-lookup"><span data-stu-id="1da87-140">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="1da87-141">在上述设置中，第一个查询返回全部 6 个 `Post`，而第二个查询仅返回 3 个。</span><span class="sxs-lookup"><span data-stu-id="1da87-141">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="1da87-142">发生这种不匹配情况的原因是第二个查询中的 Include 方法会加载相关的 `Blog` 实体。</span><span class="sxs-lookup"><span data-stu-id="1da87-142">This mismatch happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="1da87-143">由于需要在 `Blog` 和 `Post` 之间导航，因此在构造查询时，EF Core 使用了 `INNER JOIN`：</span><span class="sxs-lookup"><span data-stu-id="1da87-143">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="1da87-144">使用 `INNER JOIN` 会筛选出其相关 `Blog` 已被全局查询筛选器删除的所有 `Post`。</span><span class="sxs-lookup"><span data-stu-id="1da87-144">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="1da87-145">可使用可选导航来解决此问题，而不使用必需导航。</span><span class="sxs-lookup"><span data-stu-id="1da87-145">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="1da87-146">这样一来，第一个查询与之前相同，但第二个查询现将生成 `LEFT JOIN` 并返回 6 个结果。</span><span class="sxs-lookup"><span data-stu-id="1da87-146">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="1da87-147">替代方法是在 `Blog` 和 `Post` 实体上指定一致的筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-147">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="1da87-148">这样，匹配的筛选器就会同时应用于 `Blog` 和 `Post`。</span><span class="sxs-lookup"><span data-stu-id="1da87-148">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="1da87-149">可能导致出现意外状态的 `Post` 已被删除，且两个查询都返回 3 个结果。</span><span class="sxs-lookup"><span data-stu-id="1da87-149">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="1da87-150">禁用筛选器</span><span class="sxs-lookup"><span data-stu-id="1da87-150">Disabling Filters</span></span>

<span data-ttu-id="1da87-151">可使用 `IgnoreQueryFilters()` 运算符对各个 LINQ 查询禁用筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-151">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="1da87-152">限制</span><span class="sxs-lookup"><span data-stu-id="1da87-152">Limitations</span></span>

<span data-ttu-id="1da87-153">全局查询筛选器具有以下限制：</span><span class="sxs-lookup"><span data-stu-id="1da87-153">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="1da87-154">仅可为继承层次结构的根实体类型定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="1da87-154">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
