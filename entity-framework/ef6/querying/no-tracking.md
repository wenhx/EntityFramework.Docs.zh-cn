---
title: 无跟踪查询-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
ms.openlocfilehash: 44d58e14a2550bd08a8edd68b467237f6f5b5978
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414475"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="a75ed-102">非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="a75ed-102">No-Tracking Queries</span></span>
<span data-ttu-id="a75ed-103">有时，可能需要从查询中获取实体，但不能通过上下文跟踪这些实体。</span><span class="sxs-lookup"><span data-stu-id="a75ed-103">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="a75ed-104">在只读方案中查询大量实体时，这可能会导致更好的性能。</span><span class="sxs-lookup"><span data-stu-id="a75ed-104">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="a75ed-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="a75ed-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="a75ed-106">新的扩展方法 AsNoTracking 允许以这种方式运行任何查询。</span><span class="sxs-lookup"><span data-stu-id="a75ed-106">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="a75ed-107">例如：</span><span class="sxs-lookup"><span data-stu-id="a75ed-107">For example:</span></span>  

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
