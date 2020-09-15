---
title: 自动检测更改-EF6
description: 自动检测实体框架6中的更改
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: aa8b94d843d99a8f040bdb065297d2e19b4770d7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073777"
---
# <a name="automatic-detect-changes"></a>自动检测更改
使用大多数 POCO 实体时，确定实体的更改方式 (，因此需要将更新发送到数据库) 由检测更改算法处理。 检测更改的工作方式是检测实体的当前属性值与在查询或附加实体时存储在快照中的原始属性值之间的差异。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

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
