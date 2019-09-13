---
title: 基本查询 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ab6e35f1-397f-41c0-9ef4-85aec5466377
uid: core/querying/basic
ms.openlocfilehash: 49daa0d37175244617993cc6e911cbd59d27079f
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921743"
---
# <a name="basic-queries"></a><span data-ttu-id="8e10e-102">基本查询</span><span class="sxs-lookup"><span data-stu-id="8e10e-102">Basic Queries</span></span>

<span data-ttu-id="8e10e-103">了解如何使用语言集成查询 (LINQ) 从数据库加载实体。</span><span class="sxs-lookup"><span data-stu-id="8e10e-103">Learn how to load entities from the database using Language Integrated Query (LINQ).</span></span>

> [!TIP]  
> <span data-ttu-id="8e10e-104">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="8e10e-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="101-linq-samples"></a><span data-ttu-id="8e10e-105">101 个 LINQ 示例</span><span class="sxs-lookup"><span data-stu-id="8e10e-105">101 LINQ samples</span></span>

<span data-ttu-id="8e10e-106">此页面显示了多个使用 Entity Framework Core 完成常见任务的示例。</span><span class="sxs-lookup"><span data-stu-id="8e10e-106">This page shows a few examples to achieve common tasks with Entity Framework Core.</span></span> <span data-ttu-id="8e10e-107">有关显示 LINQ 可完成的任务的大量示例，请参阅 [101 个 LINQ 示例](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b)。</span><span class="sxs-lookup"><span data-stu-id="8e10e-107">For an extensive set of samples showing what is possible with LINQ, see [101 LINQ Samples](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b).</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="8e10e-108">加载所有数据</span><span class="sxs-lookup"><span data-stu-id="8e10e-108">Loading all data</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.ToList();
}
```

## <a name="loading-a-single-entity"></a><span data-ttu-id="8e10e-109">加载单个实体</span><span class="sxs-lookup"><span data-stu-id="8e10e-109">Loading a single entity</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Single(b => b.BlogId == 1);
}
```

## <a name="filtering"></a><span data-ttu-id="8e10e-110">筛选</span><span class="sxs-lookup"><span data-stu-id="8e10e-110">Filtering</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Where(b => b.Url.Contains("dotnet"))
        .ToList();
}
```
