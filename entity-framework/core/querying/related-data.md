---
title: 加载相关数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: d3a1810599771befb451715d93454fff63949771
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238302"
---
# <a name="loading-related-data"></a><span data-ttu-id="e2af0-102">加载相关数据</span><span class="sxs-lookup"><span data-stu-id="e2af0-102">Loading Related Data</span></span>

<span data-ttu-id="e2af0-103">Entity Framework Core 允许你在模型中使用导航属性来加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="e2af0-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="e2af0-104">有三种常见的 O/RM 模式可用于加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="e2af0-105">**预先加载**表示从数据库中加载关联数据，作为初始查询的一部分。</span><span class="sxs-lookup"><span data-stu-id="e2af0-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="e2af0-106">**显式加载**表示稍后从数据库中显式加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="e2af0-107">**延迟加载**表示在访问导航属性时，从数据库中以透明方式加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="e2af0-108">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="e2af0-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="e2af0-109">预先加载</span><span class="sxs-lookup"><span data-stu-id="e2af0-109">Eager loading</span></span>

<span data-ttu-id="e2af0-110">可以使用 `Include` 方法来指定要包含在查询结果中的关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="e2af0-111">在以下示例中，结果中返回的blogs将使用关联的posts填充其 `Posts` 属性。</span><span class="sxs-lookup"><span data-stu-id="e2af0-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="e2af0-112">Entity Framework Core 会根据之前已加载到上下文实例中的实体自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="e2af0-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="e2af0-113">因此，即使不显式包含导航属性的数据，如果先前加载了部分或所有关联实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="e2af0-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="e2af0-114">可以在单个查询中包含多个关系的关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="e2af0-115">包含多个层级</span><span class="sxs-lookup"><span data-stu-id="e2af0-115">Including multiple levels</span></span>

<span data-ttu-id="e2af0-116">使用 `ThenInclude` 方法可以依循关系包含多个层级的关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="e2af0-117">以下示例加载了所有博客、其关联文章及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="e2af0-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="e2af0-118">可通过链式调用 `ThenInclude`，进一步包含更深级别的关联数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="e2af0-119">可以将来自多个级别和多个根的关联数据合并到同一查询中。</span><span class="sxs-lookup"><span data-stu-id="e2af0-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="e2af0-120">你可能希望将已包含的某个实体的多个关联实体都包含进来。</span><span class="sxs-lookup"><span data-stu-id="e2af0-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="e2af0-121">例如，当查询 `Blogs` 时，你会包含 `Posts`，然后希望同时包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="e2af0-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="e2af0-122">为此，需要从根级别开始指定每个包含路径。</span><span class="sxs-lookup"><span data-stu-id="e2af0-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="e2af0-123">例如，`Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="e2af0-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="e2af0-124">这并不意味着会获得冗余联接查询，在大多数情况下，EF 会在生成 SQL 时合并相应的联接查询。</span><span class="sxs-lookup"><span data-stu-id="e2af0-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="e2af0-125">单个查询和拆分查询</span><span class="sxs-lookup"><span data-stu-id="e2af0-125">Single and split queries</span></span>

> [!NOTE]
> <span data-ttu-id="e2af0-126">EF Core 5.0 中已引入了此功能。</span><span class="sxs-lookup"><span data-stu-id="e2af0-126">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e2af0-127">在关系数据库中，所有相关实体默认通过引入 JOIN 来加载：</span><span class="sxs-lookup"><span data-stu-id="e2af0-127">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="e2af0-128">如果典型博客有多篇相关文章，这些文章对应的行会复制博客的信息，进而导致所谓的“笛卡尔爆炸”问题发生。</span><span class="sxs-lookup"><span data-stu-id="e2af0-128">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="e2af0-129">随着加载更多的一对多关系，重复的数据量可能会增长，并对应用程序性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="e2af0-129">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

<span data-ttu-id="e2af0-130">通过 EF，可以指定应将给定 LINQ 查询拆分为多个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="e2af0-130">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="e2af0-131">与 JOIN 不同，拆分查询为包含的每个一对多导航执行额外的 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="e2af0-131">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="e2af0-132">这会生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="e2af0-132">This will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

<span data-ttu-id="e2af0-133">虽然这避免了与 JOIN 和笛卡尔爆炸相关的性能问题，但它也有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="e2af0-133">While this avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="e2af0-134">虽然大多数数据库对单个查询保证数据一致性，但对多个查询不存在这样的保证。</span><span class="sxs-lookup"><span data-stu-id="e2af0-134">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="e2af0-135">也就是说，如果在执行查询的同时并发更新数据库，生成的数据可能会不一致。</span><span class="sxs-lookup"><span data-stu-id="e2af0-135">This means that if the database is being updated concurrently as your queries are being executed, resulting data may not be consistent.</span></span> <span data-ttu-id="e2af0-136">这可以通过将查询包装在可序列化或快照事务中来缓解，尽管这样做本身可能会产生性能问题。</span><span class="sxs-lookup"><span data-stu-id="e2af0-136">This may be mitigated by wrapping the queries in a serializable or snapshot transaction, although this may create performance issues of its own.</span></span> <span data-ttu-id="e2af0-137">如需了解更多详情，请查阅数据库的文档。</span><span class="sxs-lookup"><span data-stu-id="e2af0-137">Consult your database's documentation for more details.</span></span>
* <span data-ttu-id="e2af0-138">目前，每个查询意味着到数据库的额外网络往返；这可能会降低性能，尤其是在数据库延迟很高的情况下（例如，云服务）。</span><span class="sxs-lookup"><span data-stu-id="e2af0-138">Each query currently implies an additional network roundtrip to your database; this can degrade performance, especially where latency to the database is high (e.g. cloud services).</span></span> <span data-ttu-id="e2af0-139">未来 EF Core 将通过把查询批量处理为一次往返来改善这一点。</span><span class="sxs-lookup"><span data-stu-id="e2af0-139">EF Core will improve this in the future by batching the queries into a single roundtrip.</span></span>
* <span data-ttu-id="e2af0-140">虽然有些数据库（带有 MARS 的 SQL Server、Sqlite）允许同时使用多个查询的结果，但大多数数据库在任何给定时间点只允许一个查询处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="e2af0-140">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="e2af0-141">也就是说，在执行以后的查询之前，必须先在应用程序的内存中缓冲先前查询的所有结果，进而极大地增加了可能的内存需求。</span><span class="sxs-lookup"><span data-stu-id="e2af0-141">This means that all results from earlier queries must be buffered in your application's memory before executing later queries, increasing your memory requirements in a potentially significant way.</span></span>

<span data-ttu-id="e2af0-142">遗憾的是，没有一种加载相关实体的策略可以适用于所有情况。</span><span class="sxs-lookup"><span data-stu-id="e2af0-142">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="e2af0-143">请仔细考虑单个查询和拆分查询的优缺点，然后选择能够满足你需求的策略。</span><span class="sxs-lookup"><span data-stu-id="e2af0-143">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

> [!NOTE]
> <span data-ttu-id="e2af0-144">一对一相关实体始终都是通过 JOIN 进行加载的，因为这对性能没有影响。</span><span class="sxs-lookup"><span data-stu-id="e2af0-144">One-to-one related entities are always loaded via JOINs, as this has no performance impact.</span></span>
>
> <span data-ttu-id="e2af0-145">目前，若要在 SQL Server 上使用查询拆分，必须在连接字符串中设置 `MultipleActiveResultSets=true`。</span><span class="sxs-lookup"><span data-stu-id="e2af0-145">At the moment, use of query splitting on SQL Server requires settings `MultipleActiveResultSets=true` in your connection string.</span></span> <span data-ttu-id="e2af0-146">今后发布的预览版中将删除此要求。</span><span class="sxs-lookup"><span data-stu-id="e2af0-146">This requirement will be removed in a future preview.</span></span>
>
> <span data-ttu-id="e2af0-147">今后发布的 EF Core 5.0 预览版将允许指定对你的上下文默认使用查询拆分。</span><span class="sxs-lookup"><span data-stu-id="e2af0-147">Future previews of EF Core 5.0 will allow specifying query splitting as the default for your context.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="e2af0-148">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="e2af0-148">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="e2af0-149">EF Core 5.0 中已引入了此功能。</span><span class="sxs-lookup"><span data-stu-id="e2af0-149">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e2af0-150">在应用包含功能来加载相关数据时，可对已包含的集合导航应用某些可枚举的操作，这样就可对结果进行筛选和排序。</span><span class="sxs-lookup"><span data-stu-id="e2af0-150">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="e2af0-151">支持的操作包括：`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip` 和 `Take`。</span><span class="sxs-lookup"><span data-stu-id="e2af0-151">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="e2af0-152">应对传递到 Include 方法的 Lambda 中的集合导航应用这类操作，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="e2af0-152">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="e2af0-153">只能对每个包含的导航执行一组唯一的筛选器操作。</span><span class="sxs-lookup"><span data-stu-id="e2af0-153">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="e2af0-154">如果为某个给定的集合导航应用了多个包含操作（下例中为 `blog.Posts`），则只能对其中一个导航指定筛选器操作：</span><span class="sxs-lookup"><span data-stu-id="e2af0-154">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="e2af0-155">或者，可对多次包含的每个导航应用相同的操作：</span><span class="sxs-lookup"><span data-stu-id="e2af0-155">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="e2af0-156">在跟踪查询时，由于[导航修正](tracking.md)，Filtered Include 的结果可能不符合预期。</span><span class="sxs-lookup"><span data-stu-id="e2af0-156">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="e2af0-157">之前已查询且已存储在更改跟踪器的所有相关实体都将在 Filtered Include 查询的结果中显示，即使它们不符合筛选器的要求也是如此。</span><span class="sxs-lookup"><span data-stu-id="e2af0-157">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="e2af0-158">在这些情况下使用 Filtered Include 时，请考虑使用 `NoTracking` 查询或重新创建 DbContext。</span><span class="sxs-lookup"><span data-stu-id="e2af0-158">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="e2af0-159">示例：</span><span class="sxs-lookup"><span data-stu-id="e2af0-159">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="e2af0-160">派生类型上的包含</span><span class="sxs-lookup"><span data-stu-id="e2af0-160">Include on derived types</span></span>

<span data-ttu-id="e2af0-161">可以使用 `Include` 和 `ThenInclude` 包括来自仅在派生类型上定义的导航的相关数据。</span><span class="sxs-lookup"><span data-stu-id="e2af0-161">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="e2af0-162">给定以下模型：</span><span class="sxs-lookup"><span data-stu-id="e2af0-162">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="e2af0-163">所有人员（可以使用许多模式预先加载的学生）的 `School` 导航的内容：</span><span class="sxs-lookup"><span data-stu-id="e2af0-163">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="e2af0-164">使用强制转换</span><span class="sxs-lookup"><span data-stu-id="e2af0-164">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="e2af0-165">使用 `as` 运算符</span><span class="sxs-lookup"><span data-stu-id="e2af0-165">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="e2af0-166">使用 `Include` 的重载，该方法采用 `string` 类型的参数</span><span class="sxs-lookup"><span data-stu-id="e2af0-166">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="e2af0-167">显式加载</span><span class="sxs-lookup"><span data-stu-id="e2af0-167">Explicit loading</span></span>

<span data-ttu-id="e2af0-168">可以通过 `DbContext.Entry(...)` API 显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="e2af0-168">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="e2af0-169">还可以通过执行返回关联实体的单独查询来显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="e2af0-169">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="e2af0-170">如果已启用更改跟踪，则在加载实体时，EF Core 将自动设置新加载的实体的导航属性以引用任何已加载的实体，并设置已加载实体的导航属性以引用新加载的实体。</span><span class="sxs-lookup"><span data-stu-id="e2af0-170">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="e2af0-171">查询相关实体</span><span class="sxs-lookup"><span data-stu-id="e2af0-171">Querying related entities</span></span>

<span data-ttu-id="e2af0-172">还可以获得表示导航属性内容的 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="e2af0-172">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="e2af0-173">这样可以执行诸如通过相关实体运行聚合运算符而无需将其加载到内存中等操作。</span><span class="sxs-lookup"><span data-stu-id="e2af0-173">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="e2af0-174">还可以筛选要加载到内存中的关联实体。</span><span class="sxs-lookup"><span data-stu-id="e2af0-174">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="e2af0-175">延迟加载</span><span class="sxs-lookup"><span data-stu-id="e2af0-175">Lazy loading</span></span>

<span data-ttu-id="e2af0-176">使用延迟加载的最简单方式是通过安装 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) 包，并通过调用 `UseLazyLoadingProxies` 来启用该包。</span><span class="sxs-lookup"><span data-stu-id="e2af0-176">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="e2af0-177">例如：</span><span class="sxs-lookup"><span data-stu-id="e2af0-177">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="e2af0-178">或在使用 AddDbContext 时：</span><span class="sxs-lookup"><span data-stu-id="e2af0-178">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="e2af0-179">EF Core 接着会为可重写的任何导航属性（即，必须是 `virtual` 且在可被继承的类上）启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="e2af0-179">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="e2af0-180">例如，在以下实体中，`Post.Blog` 和 `Blog.Posts` 导航属性将被延迟加载。</span><span class="sxs-lookup"><span data-stu-id="e2af0-180">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="e2af0-181">不使用代理的延迟加载</span><span class="sxs-lookup"><span data-stu-id="e2af0-181">Lazy loading without proxies</span></span>

<span data-ttu-id="e2af0-182">使用代理进行延迟加载的工作方式是将 `ILazyLoader` 注入到实体中，如[实体类型构造函数](../modeling/constructors.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="e2af0-182">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="e2af0-183">例如：</span><span class="sxs-lookup"><span data-stu-id="e2af0-183">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="e2af0-184">这不要求实体类型为可继承的类型，也不要求导航属性必须是虚拟的，且允许通过 `new` 创建的实体实例在附加到上下文后可进行延迟加载。</span><span class="sxs-lookup"><span data-stu-id="e2af0-184">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="e2af0-185">但它需要对 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 包中定义的 `ILazyLoader` 服务的引用。</span><span class="sxs-lookup"><span data-stu-id="e2af0-185">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="e2af0-186">此包包含所允许的最少的一组类型，以便将依赖此包时所产生的影响降至最低。</span><span class="sxs-lookup"><span data-stu-id="e2af0-186">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="e2af0-187">不过，可以将 `ILazyLoader.Load` 方法以委托的形式注入，这样就可以完全避免依赖于实体类型的任何 EF Core 包。</span><span class="sxs-lookup"><span data-stu-id="e2af0-187">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="e2af0-188">例如：</span><span class="sxs-lookup"><span data-stu-id="e2af0-188">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="e2af0-189">上述代码使用 `Load` 扩展方法，以便更干净地使用委托：</span><span class="sxs-lookup"><span data-stu-id="e2af0-189">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="e2af0-190">延迟加载委托的构造函数参数必须名为“lazyLoader”。</span><span class="sxs-lookup"><span data-stu-id="e2af0-190">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="e2af0-191">未来的一个版本中的配置将计划采用另一个名称。</span><span class="sxs-lookup"><span data-stu-id="e2af0-191">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="e2af0-192">关联数据和序列化</span><span class="sxs-lookup"><span data-stu-id="e2af0-192">Related data and serialization</span></span>

<span data-ttu-id="e2af0-193">由于 EF Core 会自动修正导航属性，因此在对象图中可能会产生循环引用。</span><span class="sxs-lookup"><span data-stu-id="e2af0-193">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="e2af0-194">例如，加载博客及其关联文章会生成引用文章集合的博客对象。</span><span class="sxs-lookup"><span data-stu-id="e2af0-194">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="e2af0-195">而其中每篇文章又会引用该博客。</span><span class="sxs-lookup"><span data-stu-id="e2af0-195">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="e2af0-196">某些序列化框架不允许使用循环引用。</span><span class="sxs-lookup"><span data-stu-id="e2af0-196">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="e2af0-197">例如，Json.NET 在产生循环引用的情况下，会引发以下异常。</span><span class="sxs-lookup"><span data-stu-id="e2af0-197">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="e2af0-198">Newtonsoft.Json.JsonSerializationException：为“MyApplication.Models.Blog”类型的“Blog”属性检测到自引用循环。</span><span class="sxs-lookup"><span data-stu-id="e2af0-198">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="e2af0-199">如果正在使用 ASP.NET Core，则可以将 Json.NET 配置为忽略在对象图中找到的循环引用。</span><span class="sxs-lookup"><span data-stu-id="e2af0-199">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="e2af0-200">这是在 `Startup.cs` 中通过 `ConfigureServices(...)` 方法实现的。</span><span class="sxs-lookup"><span data-stu-id="e2af0-200">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="e2af0-201">另一种方法是使用 `[JsonIgnore]` 特性修饰其中一个导航属性，该特性指示 Json.NET 在序列化时不遍历该导航属性。</span><span class="sxs-lookup"><span data-stu-id="e2af0-201">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
