---
title: 相关数据的预先加载 - EF Core
description: 使用 Entity Framework Core 预先加载相关数据
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 97ec45a0f8bfecce4d4a59e5d1c36c0268d96052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062571"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="35f45-103">相关数据的预先加载</span><span class="sxs-lookup"><span data-stu-id="35f45-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="35f45-104">预先加载</span><span class="sxs-lookup"><span data-stu-id="35f45-104">Eager loading</span></span>

<span data-ttu-id="35f45-105">可以使用 `Include` 方法来指定要包含在查询结果中的关联数据。</span><span class="sxs-lookup"><span data-stu-id="35f45-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="35f45-106">在以下示例中，结果中返回的blogs将使用关联的posts填充其 `Posts` 属性。</span><span class="sxs-lookup"><span data-stu-id="35f45-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="35f45-107">Entity Framework Core 会根据之前已加载到上下文实例中的实体自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="35f45-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="35f45-108">因此，即使不显式包含导航属性的数据，如果先前加载了部分或所有关联实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="35f45-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="35f45-109">可以在单个查询中包含多个关系的关联数据。</span><span class="sxs-lookup"><span data-stu-id="35f45-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="35f45-110">包含多个层级</span><span class="sxs-lookup"><span data-stu-id="35f45-110">Including multiple levels</span></span>

<span data-ttu-id="35f45-111">使用 `ThenInclude` 方法可以依循关系包含多个层级的关联数据。</span><span class="sxs-lookup"><span data-stu-id="35f45-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="35f45-112">以下示例加载了所有博客、其关联文章及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="35f45-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="35f45-113">可通过链式调用 `ThenInclude`，进一步包含更深级别的关联数据。</span><span class="sxs-lookup"><span data-stu-id="35f45-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="35f45-114">可以将对来自多个级别和多个根的关联数据的所有调用合并到同一查询中。</span><span class="sxs-lookup"><span data-stu-id="35f45-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="35f45-115">你可能希望将已包含的某个实体的多个关联实体都包含进来。</span><span class="sxs-lookup"><span data-stu-id="35f45-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="35f45-116">例如，当查询 `Blogs` 时，你会包含 `Posts`，然后希望同时包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="35f45-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="35f45-117">为了包含这两项内容，需要从根级别开始指定每个包含路径。</span><span class="sxs-lookup"><span data-stu-id="35f45-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="35f45-118">例如，`Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="35f45-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="35f45-119">这并不意味着会获得冗余联接查询，在大多数情况下，EF 会在生成 SQL 时合并相应的联接查询。</span><span class="sxs-lookup"><span data-stu-id="35f45-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="35f45-120">单个查询和拆分查询</span><span class="sxs-lookup"><span data-stu-id="35f45-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="35f45-121">单个查询</span><span class="sxs-lookup"><span data-stu-id="35f45-121">Single queries</span></span>

<span data-ttu-id="35f45-122">在关系数据库中，所有相关实体默认通过引入 JOIN 来加载：</span><span class="sxs-lookup"><span data-stu-id="35f45-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="35f45-123">如果典型博客有多篇相关文章，这些文章对应的行会复制博客的信息，进而导致所谓的“笛卡尔爆炸”问题发生。</span><span class="sxs-lookup"><span data-stu-id="35f45-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="35f45-124">随着加载更多的一对多关系，重复的数据量可能会增长，并对应用程序性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="35f45-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="35f45-125">默认情况下，如果 EF Core 检测到查询加载集合包含内容可能会导致性能问题，便会发出警告。</span><span class="sxs-lookup"><span data-stu-id="35f45-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="35f45-126">拆分查询</span><span class="sxs-lookup"><span data-stu-id="35f45-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="35f45-127">EF Core 5.0 中已引入了此功能。</span><span class="sxs-lookup"><span data-stu-id="35f45-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="35f45-128">通过 EF，可以指定应将给定 LINQ 查询拆分为多个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="35f45-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="35f45-129">与 JOIN 不同，拆分查询为包含的每个一对多导航执行额外的 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="35f45-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="35f45-130">这会生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="35f45-130">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="35f45-131">一对一相关实体始终都是通过 JOIN 在同一查询中进行加载的，因为这对性能没有影响。</span><span class="sxs-lookup"><span data-stu-id="35f45-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="35f45-132">全局启用拆分查询</span><span class="sxs-lookup"><span data-stu-id="35f45-132">Enabling split queries globally</span></span>

<span data-ttu-id="35f45-133">还可以将拆分查询配置为应用程序上下文的默认查询：</span><span class="sxs-lookup"><span data-stu-id="35f45-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="35f45-134">将拆分查询配置为默认查询后，仍然可以将特定查询配置为以单个查询的形式执行：</span><span class="sxs-lookup"><span data-stu-id="35f45-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="35f45-135">如果未显式指定查询拆分模式（既不是全局指定，也不是在查询中指定），并且 EF Core 检测到单个查询加载了多项包含内容，便会发出警告，提醒这可能导致的性能问题。</span><span class="sxs-lookup"><span data-stu-id="35f45-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="35f45-136">将查询模式设置为 SingleQuery 将导致无法生成警告。</span><span class="sxs-lookup"><span data-stu-id="35f45-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="35f45-137">拆分查询的特征</span><span class="sxs-lookup"><span data-stu-id="35f45-137">Characteristics of split queries</span></span>

<span data-ttu-id="35f45-138">虽然拆分查询避免了与 JOIN 和笛卡尔爆炸相关的性能问题，但它也有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="35f45-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="35f45-139">虽然大多数数据库对单个查询保证数据一致性，但对多个查询不存在这样的保证。</span><span class="sxs-lookup"><span data-stu-id="35f45-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="35f45-140">如果在执行查询时同时更新数据库，生成的数据可能会不一致。</span><span class="sxs-lookup"><span data-stu-id="35f45-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="35f45-141">这可以通过将查询包装在可序列化或快照事务中来缓解，尽管这样做本身可能会产生性能问题。</span><span class="sxs-lookup"><span data-stu-id="35f45-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="35f45-142">有关详细信息，请参见数据库器文档。</span><span class="sxs-lookup"><span data-stu-id="35f45-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="35f45-143">当前，每个查询都意味着对数据库进行一次额外的网络往返。</span><span class="sxs-lookup"><span data-stu-id="35f45-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="35f45-144">多次网络往返会降低性能，尤其是在数据库延迟很高的情况下（例如云服务）。</span><span class="sxs-lookup"><span data-stu-id="35f45-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="35f45-145">虽然有些数据库（带有 MARS 的 SQL Server、Sqlite）允许同时使用多个查询的结果，但大多数数据库在任何给定时间点只允许一个查询处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="35f45-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="35f45-146">因此，在执行以后的查询之前，必须先在应用程序的内存中缓冲先前查询的所有结果，这将增加内存需求。</span><span class="sxs-lookup"><span data-stu-id="35f45-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="35f45-147">遗憾的是，没有一种加载相关实体的策略可以适用于所有情况。</span><span class="sxs-lookup"><span data-stu-id="35f45-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="35f45-148">请仔细考虑单个查询和拆分查询的优缺点，然后选择能够满足你需求的策略。</span><span class="sxs-lookup"><span data-stu-id="35f45-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="35f45-149">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="35f45-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="35f45-150">EF Core 5.0 中已引入了此功能。</span><span class="sxs-lookup"><span data-stu-id="35f45-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="35f45-151">在应用包含功能来加载相关数据时，可对已包含的集合导航应用某些可枚举的操作，这样就可对结果进行筛选和排序。</span><span class="sxs-lookup"><span data-stu-id="35f45-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="35f45-152">支持的操作包括：`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip` 和 `Take`。</span><span class="sxs-lookup"><span data-stu-id="35f45-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="35f45-153">应对传递到 Include 方法的 Lambda 中的集合导航应用这类操作，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="35f45-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="35f45-154">只能对每个包含的导航执行一组唯一的筛选器操作。</span><span class="sxs-lookup"><span data-stu-id="35f45-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="35f45-155">如果为某个给定的集合导航应用了多个包含操作（下例中为 `blog.Posts`），则只能对其中一个导航指定筛选器操作：</span><span class="sxs-lookup"><span data-stu-id="35f45-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="35f45-156">可对多次包含的每个导航应用相同的操作：</span><span class="sxs-lookup"><span data-stu-id="35f45-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="35f45-157">在跟踪查询时，由于[导航修正](xref:core/querying/tracking)，Filtered Include 的结果可能不符合预期。</span><span class="sxs-lookup"><span data-stu-id="35f45-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="35f45-158">之前已查询且已存储在更改跟踪器的所有相关实体都将在 Filtered Include 查询的结果中显示，即使它们不符合筛选器的要求也是如此。</span><span class="sxs-lookup"><span data-stu-id="35f45-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="35f45-159">在这些情况下使用 Filtered Include 时，请考虑使用 `NoTracking` 查询或重新创建 DbContext。</span><span class="sxs-lookup"><span data-stu-id="35f45-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="35f45-160">示例：</span><span class="sxs-lookup"><span data-stu-id="35f45-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="35f45-161">派生类型上的包含</span><span class="sxs-lookup"><span data-stu-id="35f45-161">Include on derived types</span></span>

<span data-ttu-id="35f45-162">可以使用 `Include` 和 `ThenInclude` 包含仅在派生类型上定义的导航的关联数据。</span><span class="sxs-lookup"><span data-stu-id="35f45-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="35f45-163">给定以下模型：</span><span class="sxs-lookup"><span data-stu-id="35f45-163">Given the following model:</span></span>

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

<span data-ttu-id="35f45-164">所有人员（可以使用许多模式预先加载的学生）的 `School` 导航的内容：</span><span class="sxs-lookup"><span data-stu-id="35f45-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="35f45-165">使用强制转换</span><span class="sxs-lookup"><span data-stu-id="35f45-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="35f45-166">使用 `as` 运算符</span><span class="sxs-lookup"><span data-stu-id="35f45-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="35f45-167">使用 `Include` 的重载，该方法采用 `string` 类型的参数</span><span class="sxs-lookup"><span data-stu-id="35f45-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
