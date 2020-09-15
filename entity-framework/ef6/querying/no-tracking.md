---
title: 无跟踪查询-EF6
description: 实体框架6中的无跟踪查询
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073894"
---
# <a name="no-tracking-queries"></a>非跟踪查询
有时，可能需要从查询中获取实体，但不能通过上下文跟踪这些实体。 在只读方案中查询大量实体时，这可能会导致更好的性能。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

新的扩展方法 AsNoTracking 允许以这种方式运行任何查询。 例如：  

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
