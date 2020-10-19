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
# <a name="query-tags"></a>查询标记

查询标记有助于将代码中的 LINQ 查询与日志中捕获的已生成 SQL 查询相关联。
使用新增的 `TagWith()` 方法对 LINQ 查询进行批注：

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags)。

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

此 LINQ 查询转换为以下 SQL 语句：

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

可以对同一个查询多次调用 `TagWith()`。
查询标记具有累积性。
例如，给定方法如下：

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

以下查询：

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

转换为：

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

也可以使用多线串作为查询标记。
例如：

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

生成以下 SQL：

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>已知的限制

**查询标记不可参数化：** EF Core 始终将 LINQ 查询中的查询标记视为，已生成 SQL 中包含的字符串文本。
禁止使用将查询标记用作参数的已编译查询。
