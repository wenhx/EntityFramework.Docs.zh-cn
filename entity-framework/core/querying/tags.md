---
title: 查询标记 - EF Core
description: 使用查询标记帮助识别 Entity Framework Core 发出的日志消息中的特定查询
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065727"
---
# <a name="query-tags"></a><span data-ttu-id="f7986-103">查询标记</span><span class="sxs-lookup"><span data-stu-id="f7986-103">Query tags</span></span>

<span data-ttu-id="f7986-104">查询标记有助于将代码中的 LINQ 查询与日志中捕获的已生成 SQL 查询相关联。</span><span class="sxs-lookup"><span data-stu-id="f7986-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="f7986-105">使用新增的 `TagWith()` 方法对 LINQ 查询进行批注：</span><span class="sxs-lookup"><span data-stu-id="f7986-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="f7986-106">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags)。</span><span class="sxs-lookup"><span data-stu-id="f7986-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="f7986-107">此 LINQ 查询转换为以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="f7986-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="f7986-108">可以对同一个查询多次调用 `TagWith()`。</span><span class="sxs-lookup"><span data-stu-id="f7986-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="f7986-109">查询标记具有累积性。</span><span class="sxs-lookup"><span data-stu-id="f7986-109">Query tags are cumulative.</span></span>
<span data-ttu-id="f7986-110">例如，给定方法如下：</span><span class="sxs-lookup"><span data-stu-id="f7986-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="f7986-111">以下查询：</span><span class="sxs-lookup"><span data-stu-id="f7986-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="f7986-112">转换为：</span><span class="sxs-lookup"><span data-stu-id="f7986-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="f7986-113">也可以使用多线串作为查询标记。</span><span class="sxs-lookup"><span data-stu-id="f7986-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="f7986-114">例如：</span><span class="sxs-lookup"><span data-stu-id="f7986-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="f7986-115">生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="f7986-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="f7986-116">已知的限制</span><span class="sxs-lookup"><span data-stu-id="f7986-116">Known limitations</span></span>

<span data-ttu-id="f7986-117">**查询标记不可参数化：** EF Core 始终将 LINQ 查询中的查询标记视为，已生成 SQL 中包含的字符串文本。</span><span class="sxs-lookup"><span data-stu-id="f7986-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="f7986-118">禁止使用将查询标记用作参数的已编译查询。</span><span class="sxs-lookup"><span data-stu-id="f7986-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
