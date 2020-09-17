---
title: 查询标记 - EF Core
description: 使用查询标记帮助识别 Entity Framework Core 发出的日志消息中的特定查询
author: divega
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: 27f757f4159a36bec324cce56d74b7860e1c3741
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070986"
---
# <a name="query-tags"></a><span data-ttu-id="9b537-103">查询标记</span><span class="sxs-lookup"><span data-stu-id="9b537-103">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="9b537-104">此为 EF Core 2.2 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="9b537-104">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="9b537-105">此功能有助于将代码中的 LINQ 查询与日志中捕获的已生成 SQL 查询相关联。</span><span class="sxs-lookup"><span data-stu-id="9b537-105">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="9b537-106">使用新增的 `TagWith()` 方法对 LINQ 查询进行批注：</span><span class="sxs-lookup"><span data-stu-id="9b537-106">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="9b537-107">此 LINQ 查询转换为以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="9b537-107">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="9b537-108">可以对同一个查询多次调用 `TagWith()`。</span><span class="sxs-lookup"><span data-stu-id="9b537-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="9b537-109">查询标记具有累积性。</span><span class="sxs-lookup"><span data-stu-id="9b537-109">Query tags are cumulative.</span></span>
<span data-ttu-id="9b537-110">例如，给定方法如下：</span><span class="sxs-lookup"><span data-stu-id="9b537-110">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="9b537-111">以下查询：</span><span class="sxs-lookup"><span data-stu-id="9b537-111">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="9b537-112">转换为：</span><span class="sxs-lookup"><span data-stu-id="9b537-112">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="9b537-113">也可以使用多线串作为查询标记。</span><span class="sxs-lookup"><span data-stu-id="9b537-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="9b537-114">例如：</span><span class="sxs-lookup"><span data-stu-id="9b537-114">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="9b537-115">生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="9b537-115">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="9b537-116">已知的限制</span><span class="sxs-lookup"><span data-stu-id="9b537-116">Known limitations</span></span>

<span data-ttu-id="9b537-117">**查询标记不可参数化：** EF Core 始终将 LINQ 查询中的查询标记视为，已生成 SQL 中包含的字符串文本。</span><span class="sxs-lookup"><span data-stu-id="9b537-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="9b537-118">禁止使用将查询标记用作参数的已编译查询。</span><span class="sxs-lookup"><span data-stu-id="9b537-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
