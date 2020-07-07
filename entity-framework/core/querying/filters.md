---
title: 全局查询筛选器 - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 233289b0c50e15f4555b342bc654211ce04c24d3
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370456"
---
# <a name="global-query-filters"></a><span data-ttu-id="69f00-102">全局查询筛选器</span><span class="sxs-lookup"><span data-stu-id="69f00-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="69f00-103">EF Core 2.0 中已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="69f00-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="69f00-104">全局查询筛选器是应用于元数据模型（通常为 *OnModelCreating*）中的实体类型的 LINQ 查询谓词（通常传递给 LINQ *Where* 查询运算符的布尔表达式）。</span><span class="sxs-lookup"><span data-stu-id="69f00-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="69f00-105">此类筛选器自动应用于涉及这些实体类型（包括通过使用 Include 或直接导航属性引用等方式间接引用的实体类型）的所有 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="69f00-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="69f00-106">此功能的一些常见应用如下：</span><span class="sxs-lookup"><span data-stu-id="69f00-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="69f00-107">**软删除** - 实体类型定义“IsDeleted”属性。</span><span class="sxs-lookup"><span data-stu-id="69f00-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="69f00-108">**多租户** - 实体类型定义“TenantId”属性。</span><span class="sxs-lookup"><span data-stu-id="69f00-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="69f00-109">示例</span><span class="sxs-lookup"><span data-stu-id="69f00-109">Example</span></span>

<span data-ttu-id="69f00-110">下面的示例显示了如何使用全局查询筛选器在简单的博客模型中实现软删除和多租户查询行为。</span><span class="sxs-lookup"><span data-stu-id="69f00-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="69f00-111">可在 GitHub 上查看[多租户示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)和[使用导航的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)。</span><span class="sxs-lookup"><span data-stu-id="69f00-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="69f00-112">首先，定义实体：</span><span class="sxs-lookup"><span data-stu-id="69f00-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="69f00-113">请注意 _Blog_ 实体上的 _tenantId_ 字段的声明。</span><span class="sxs-lookup"><span data-stu-id="69f00-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="69f00-114">这会用于将每个 _Blog_ 实例与特定租户相关联。</span><span class="sxs-lookup"><span data-stu-id="69f00-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="69f00-115">同时在 _Post_ 实体类型上定义了 _IsDeleted_ 属性。</span><span class="sxs-lookup"><span data-stu-id="69f00-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="69f00-116">这会用于跟踪一个 _Post_ 实例是否已“软删除”。</span><span class="sxs-lookup"><span data-stu-id="69f00-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="69f00-117">也就是说，实例只是被标记为已删除，而非真正删除了基础数据。</span><span class="sxs-lookup"><span data-stu-id="69f00-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="69f00-118">接下来，使用 `HasQueryFilter` API 在 _OnModelCreating_ 中配置查询筛选器。</span><span class="sxs-lookup"><span data-stu-id="69f00-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="69f00-119">传递给 _HasQueryFilter_ 调用的谓词表达式将立即自动应用于这些类型的任何 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="69f00-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="69f00-120">请注意 DbContext 实例级别字段的使用：`_tenantId` 用于设置当前租户。</span><span class="sxs-lookup"><span data-stu-id="69f00-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="69f00-121">模型级筛选器将使用正确上下文实例（即执行查询的实例）中的值。</span><span class="sxs-lookup"><span data-stu-id="69f00-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="69f00-122">目前不能在同一个实体中定义多个查询筛选器，只会应用最后一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="69f00-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="69f00-123">但是，可以使用逻辑 _AND_ 运算符（[C# 中为 `&&`](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)）定义含有多种条件的单个筛选器。</span><span class="sxs-lookup"><span data-stu-id="69f00-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="69f00-124">使用导航</span><span class="sxs-lookup"><span data-stu-id="69f00-124">Use of navigations</span></span>

<span data-ttu-id="69f00-125">可在定义全局查询筛选器时使用导航。</span><span class="sxs-lookup"><span data-stu-id="69f00-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="69f00-126">以递归的方式应用它们 - 转换查询筛选器中使用的导航时，还将应用在引用的实体上定义的查询筛选器，这可能会添加更多导航。</span><span class="sxs-lookup"><span data-stu-id="69f00-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="69f00-127">目前，EF Core 不会检测全局查询筛选器定义中的循环，因此需在定义它们时小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="69f00-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="69f00-128">如果指定错误，这可能在查询转换期间导致无限循环。</span><span class="sxs-lookup"><span data-stu-id="69f00-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="69f00-129">使用必需的导航访问具有查询筛选器的实体</span><span class="sxs-lookup"><span data-stu-id="69f00-129">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="69f00-130">如果使用必需的导航访问定义了全局查询筛选器的实体，则可能导致意外结果。</span><span class="sxs-lookup"><span data-stu-id="69f00-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="69f00-131">必需的导航要求始终存在相关实体。</span><span class="sxs-lookup"><span data-stu-id="69f00-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="69f00-132">如果查询筛选器筛选出了必需的相关实体，则父实体可能导致出现意外状态。</span><span class="sxs-lookup"><span data-stu-id="69f00-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="69f00-133">这可能导致返回的元素数量比预期数量少。</span><span class="sxs-lookup"><span data-stu-id="69f00-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="69f00-134">为了说明此问题，我们可使用上面指定的 `Blog` 和 `Post` 实体，同时使用下面的 OnModelCreating 方法：</span><span class="sxs-lookup"><span data-stu-id="69f00-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="69f00-135">可通过以下数据对模型进行种子设定：</span><span class="sxs-lookup"><span data-stu-id="69f00-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="69f00-136">执行两个查询时，可能会观察到此问题：</span><span class="sxs-lookup"><span data-stu-id="69f00-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="69f00-137">在此设置中，第一个查询返回全部 6 个 `Post`，而第二个查询仅返回 3 个。</span><span class="sxs-lookup"><span data-stu-id="69f00-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="69f00-138">发生这种情况的原因是第二个查询中的 Include 方法会加载相关的 `Blog` 实体。</span><span class="sxs-lookup"><span data-stu-id="69f00-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="69f00-139">由于需要在 `Blog` 和 `Post` 之间导航，因此在构造查询时，EF Core 使用了 `INNER JOIN`：</span><span class="sxs-lookup"><span data-stu-id="69f00-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="69f00-140">使用 `INNER JOIN` 会筛选出其相关 `Blog` 已被全局查询筛选器删除的所有 `Post`。</span><span class="sxs-lookup"><span data-stu-id="69f00-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="69f00-141">可使用可选导航来解决此问题，而不使用必需导航。</span><span class="sxs-lookup"><span data-stu-id="69f00-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="69f00-142">这样一来，第一个查询与之前相同，但第二个查询现将生成 `LEFT JOIN` 并返回 6 个结果。</span><span class="sxs-lookup"><span data-stu-id="69f00-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="69f00-143">替代方法是在 `Blog` 和 `Post` 实体上指定一致的筛选器。</span><span class="sxs-lookup"><span data-stu-id="69f00-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="69f00-144">这样，匹配的筛选器就会同时应用于 `Blog` 和 `Post`。</span><span class="sxs-lookup"><span data-stu-id="69f00-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="69f00-145">可能导致出现意外状态的 `Post` 已被删除，且两个查询都返回 3 个结果。</span><span class="sxs-lookup"><span data-stu-id="69f00-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="69f00-146">禁用筛选器</span><span class="sxs-lookup"><span data-stu-id="69f00-146">Disabling Filters</span></span>

<span data-ttu-id="69f00-147">可使用 `IgnoreQueryFilters()` 运算符对各个 LINQ 查询禁用筛选器。</span><span class="sxs-lookup"><span data-stu-id="69f00-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="69f00-148">限制</span><span class="sxs-lookup"><span data-stu-id="69f00-148">Limitations</span></span>

<span data-ttu-id="69f00-149">全局查询筛选器具有以下限制：</span><span class="sxs-lookup"><span data-stu-id="69f00-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="69f00-150">仅可为继承层次结构的根实体类型定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="69f00-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
