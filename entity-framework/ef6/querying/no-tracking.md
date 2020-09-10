---
title: 无跟踪查询-EF6
description: 实体框架6中的无跟踪查询
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620294"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="65bd4-103">非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="65bd4-103">No-Tracking Queries</span></span>
<span data-ttu-id="65bd4-104">有时，可能需要从查询中获取实体，但不能通过上下文跟踪这些实体。</span><span class="sxs-lookup"><span data-stu-id="65bd4-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="65bd4-105">在只读方案中查询大量实体时，这可能会导致更好的性能。</span><span class="sxs-lookup"><span data-stu-id="65bd4-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="65bd4-106">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="65bd4-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="65bd4-107">新的扩展方法 AsNoTracking 允许以这种方式运行任何查询。</span><span class="sxs-lookup"><span data-stu-id="65bd4-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="65bd4-108">例如：</span><span class="sxs-lookup"><span data-stu-id="65bd4-108">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
