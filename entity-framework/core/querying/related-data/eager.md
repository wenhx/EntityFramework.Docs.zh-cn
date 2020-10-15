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
# <a name="eager-loading-of-related-data"></a>相关数据的预先加载

## <a name="eager-loading"></a>预先加载

可以使用 `Include` 方法来指定要包含在查询结果中的关联数据。 在以下示例中，结果中返回的blogs将使用关联的posts填充其 `Posts` 属性。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core 会根据之前已加载到上下文实例中的实体自动填充导航属性。 因此，即使不显式包含导航属性的数据，如果先前加载了部分或所有关联实体，则仍可能填充该属性。

可以在单个查询中包含多个关系的关联数据。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>包含多个层级

使用 `ThenInclude` 方法可以依循关系包含多个层级的关联数据。 以下示例加载了所有博客、其关联文章及每篇文章的作者。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

可通过链式调用 `ThenInclude`，进一步包含更深级别的关联数据。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

可以将对来自多个级别和多个根的关联数据的所有调用合并到同一查询中。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

你可能希望将已包含的某个实体的多个关联实体都包含进来。 例如，当查询 `Blogs` 时，你会包含 `Posts`，然后希望同时包含 `Posts` 的 `Author` 和 `Tags`。 为了包含这两项内容，需要从根级别开始指定每个包含路径。 例如，`Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。 这并不意味着会获得冗余联接查询，在大多数情况下，EF 会在生成 SQL 时合并相应的联接查询。

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>单个查询和拆分查询

### <a name="single-queries"></a>单个查询

在关系数据库中，所有相关实体默认通过引入 JOIN 来加载：

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

如果典型博客有多篇相关文章，这些文章对应的行会复制博客的信息，进而导致所谓的“笛卡尔爆炸”问题发生。 随着加载更多的一对多关系，重复的数据量可能会增长，并对应用程序性能产生负面影响。 默认情况下，如果 EF Core 检测到查询加载集合包含内容可能会导致性能问题，便会发出警告。

### <a name="split-queries"></a>拆分查询

> [!NOTE]
> EF Core 5.0 中已引入了此功能。

通过 EF，可以指定应将给定 LINQ 查询拆分为多个 SQL 查询。 与 JOIN 不同，拆分查询为包含的每个一对多导航执行额外的 SQL 查询：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

这会生成以下 SQL：

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
> 一对一相关实体始终都是通过 JOIN 在同一查询中进行加载的，因为这对性能没有影响。

### <a name="enabling-split-queries-globally"></a>全局启用拆分查询

还可以将拆分查询配置为应用程序上下文的默认查询：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

将拆分查询配置为默认查询后，仍然可以将特定查询配置为以单个查询的形式执行：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

如果未显式指定查询拆分模式（既不是全局指定，也不是在查询中指定），并且 EF Core 检测到单个查询加载了多项包含内容，便会发出警告，提醒这可能导致的性能问题。 将查询模式设置为 SingleQuery 将导致无法生成警告。

### <a name="characteristics-of-split-queries"></a>拆分查询的特征

虽然拆分查询避免了与 JOIN 和笛卡尔爆炸相关的性能问题，但它也有一些缺点：

* 虽然大多数数据库对单个查询保证数据一致性，但对多个查询不存在这样的保证。 如果在执行查询时同时更新数据库，生成的数据可能会不一致。 这可以通过将查询包装在可序列化或快照事务中来缓解，尽管这样做本身可能会产生性能问题。 有关详细信息，请参见数据库器文档。
* 当前，每个查询都意味着对数据库进行一次额外的网络往返。 多次网络往返会降低性能，尤其是在数据库延迟很高的情况下（例如云服务）。
* 虽然有些数据库（带有 MARS 的 SQL Server、Sqlite）允许同时使用多个查询的结果，但大多数数据库在任何给定时间点只允许一个查询处于活动状态。 因此，在执行以后的查询之前，必须先在应用程序的内存中缓冲先前查询的所有结果，这将增加内存需求。

遗憾的是，没有一种加载相关实体的策略可以适用于所有情况。 请仔细考虑单个查询和拆分查询的优缺点，然后选择能够满足你需求的策略。

## <a name="filtered-include"></a>经过筛选的包含

> [!NOTE]
> EF Core 5.0 中已引入了此功能。

在应用包含功能来加载相关数据时，可对已包含的集合导航应用某些可枚举的操作，这样就可对结果进行筛选和排序。

支持的操作包括：`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip` 和 `Take`。

应对传递到 Include 方法的 Lambda 中的集合导航应用这类操作，如下例所示：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

只能对每个包含的导航执行一组唯一的筛选器操作。 如果为某个给定的集合导航应用了多个包含操作（下例中为 `blog.Posts`），则只能对其中一个导航指定筛选器操作：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

可对多次包含的每个导航应用相同的操作：

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> 在跟踪查询时，由于[导航修正](xref:core/querying/tracking)，Filtered Include 的结果可能不符合预期。 之前已查询且已存储在更改跟踪器的所有相关实体都将在 Filtered Include 查询的结果中显示，即使它们不符合筛选器的要求也是如此。 在这些情况下使用 Filtered Include 时，请考虑使用 `NoTracking` 查询或重新创建 DbContext。

示例：

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a>派生类型上的包含

可以使用 `Include` 和 `ThenInclude` 包含仅在派生类型上定义的导航的关联数据。

给定以下模型：

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

所有人员（可以使用许多模式预先加载的学生）的 `School` 导航的内容：

* 使用强制转换

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* 使用 `as` 运算符

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* 使用 `Include` 的重载，该方法采用 `string` 类型的参数

  ```csharp
  context.People.Include("School").ToList()
  ```
