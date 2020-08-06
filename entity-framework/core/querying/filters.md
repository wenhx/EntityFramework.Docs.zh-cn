---
title: 全局查询筛选器 - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 57d81919dedb853d2a41066f76ec20685ae41d6b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526896"
---
# <a name="global-query-filters"></a>全局查询筛选器

> [!NOTE]
> EF Core 2.0 中已引入此功能。

全局查询筛选器是应用于元数据模型（通常为 *OnModelCreating*）中的实体类型的 LINQ 查询谓词（通常传递给 LINQ *Where* 查询运算符的布尔表达式）。 此类筛选器自动应用于涉及这些实体类型（包括通过使用 Include 或直接导航属性引用等方式间接引用的实体类型）的所有 LINQ 查询。 此功能的一些常见应用如下：

* **软删除** - 实体类型定义“IsDeleted”属性。
* **多租户** - 实体类型定义“TenantId”属性。

## <a name="example"></a>示例

下面的示例显示了如何使用全局查询筛选器在简单的博客模型中实现软删除和多租户查询行为。

> [!TIP]
> 可在 GitHub 上查看[多租户示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)和[使用导航的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)。 

首先，定义实体：

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

请注意 _Blog_ 实体上的 _tenantId_ 字段的声明。 这会用于将每个 _Blog_ 实例与特定租户相关联。 同时在 _Post_ 实体类型上定义了 _IsDeleted_ 属性。 这会用于跟踪一个 _Post_ 实例是否已“软删除”。 也就是说，实例只是被标记为已删除，而非真正删除了基础数据。

接下来，使用 `HasQueryFilter` API 在 _OnModelCreating_ 中配置查询筛选器。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

传递给 _HasQueryFilter_ 调用的谓词表达式将立即自动应用于这些类型的任何 LINQ 查询。

> [!TIP]
> 请注意 DbContext 实例级别字段的使用：`_tenantId` 用于设置当前租户。 模型级筛选器将使用正确上下文实例（即执行查询的实例）中的值。

> [!NOTE]
> 目前不能在同一个实体中定义多个查询筛选器，只会应用最后一个筛选器。 但是，可以使用逻辑 _AND_ 运算符（[C# 中为 `&&`](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)）定义含有多种条件的单个筛选器。

## <a name="use-of-navigations"></a>使用导航

可在定义全局查询筛选器时使用导航。 以递归的方式应用它们 - 转换查询筛选器中使用的导航时，还将应用在引用的实体上定义的查询筛选器，这可能会添加更多导航。

> [!NOTE]
> 目前，EF Core 不会检测全局查询筛选器定义中的循环，因此需在定义它们时小心谨慎。 如果指定错误，这可能在查询转换期间导致无限循环。

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>使用必需的导航访问具有查询筛选器的实体

> [!CAUTION]
> 如果使用必需的导航访问定义了全局查询筛选器的实体，则可能导致意外结果。 

必需的导航要求始终存在相关实体。 如果查询筛选器筛选出了必需的相关实体，则父实体可能导致出现意外状态。 这可能导致返回的元素数量比预期数量少。 

为了说明此问题，我们可使用上面指定的 `Blog` 和 `Post` 实体，同时使用下面的 OnModelCreating 方法：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

可通过以下数据对模型进行种子设定：

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

执行两个查询时，可能会观察到此问题：

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

在此设置中，第一个查询返回全部 6 个 `Post`，而第二个查询仅返回 3 个。 发生这种情况的原因是第二个查询中的 Include 方法会加载相关的 `Blog` 实体。 由于需要在 `Blog` 和 `Post` 之间导航，因此在构造查询时，EF Core 使用了 `INNER JOIN`：

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

使用 `INNER JOIN` 会筛选出其相关 `Blog` 已被全局查询筛选器删除的所有 `Post`。 

可使用可选导航来解决此问题，而不使用必需导航。 这样一来，第一个查询与之前相同，但第二个查询现将生成 `LEFT JOIN` 并返回 6 个结果。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

替代方法是在 `Blog` 和 `Post` 实体上指定一致的筛选器。
这样，匹配的筛选器就会同时应用于 `Blog` 和 `Post`。 可能导致出现意外状态的 `Post` 已被删除，且两个查询都返回 3 个结果。 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>禁用筛选器

可使用 `IgnoreQueryFilters()` 运算符对各个 LINQ 查询禁用筛选器。

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>限制

全局查询筛选器具有以下限制：

* 仅可为继承层次结构的根实体类型定义筛选器。
