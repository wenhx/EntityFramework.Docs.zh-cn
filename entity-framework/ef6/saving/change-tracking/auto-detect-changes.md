---
title: 自动检测更改-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: 9af85fd7ca48a14432a1f33c59079fc438ef8810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414379"
---
# <a name="automatic-detect-changes"></a>自动检测更改
使用最多 POCO 实体时，会通过检测更改算法来处理实体如何更改（以及需要向数据库发送更新）。 检测更改的工作方式是检测实体的当前属性值与在查询或附加实体时存储在快照中的原始属性值之间的差异。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

默认情况下，在调用以下方法时，实体框架自动执行检测更改：  

- DbSet.Find  
- DbSet  
- DbSet  
- DbSet. AddRange
- DbSet  
- DbSet. RemoveRange
- DbSet  
- DbContext.SaveChanges  
- DbContext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker  

## <a name="disabling-automatic-detection-of-changes"></a>禁用更改的自动检测  

如果正在跟踪上下文中的大量实体，并在循环中多次调用其中一种方法，则可以通过在循环的持续时间内关闭更改检测来显著提高性能。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

不要忘记重新启用循环后的更改检测，我们使用了 try/finally 来确保始终重新启用该循环，即使循环中的代码引发异常也是如此。  

禁用和重新启用的替代方法是始终始终关闭更改的自动检测，并调用上下文。ChangeTracker 显式或使用更改跟踪代理。 这两个选项都是高级选项，可轻松地在应用程序中引入微妙 bug，因此请谨慎使用。  

如果需要在上下文中添加或删除多个对象，请考虑使用 AddRange 和 DbSet RemoveRange。 此方法在添加或删除操作完成后，只自动检测更改一次。 
