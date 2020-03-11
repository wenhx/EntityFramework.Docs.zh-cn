---
title: Load 方法-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: bcea8ab2477f44281cd5de824457a72a84ccc766
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414493"
---
# <a name="the-load-method"></a>加载方法
在某些情况下，你可能需要将实体从数据库加载到上下文中，而不会立即对这些实体执行任何操作。 这是一个很好的例子，就是加载用于数据绑定的实体，如[本地数据](~/ef6/querying/local-data.md)中所述。 实现此目的的一个常见方法是编写 LINQ 查询，然后对其调用 System.linq.enumerable.tolist，只是立即放弃创建的列表。 负载扩展方法的工作方式与 System.linq.enumerable.tolist 相同，只是它可以避免完全创建列表。  

本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

下面是两个使用 Load 的示例。 第一种方式是从 Windows 窗体数据绑定应用程序中获取，在将其绑定到本地集合之前，将使用 Load 查询实体，如[本地数据](~/ef6/querying/local-data.md)中所述：  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

第二个示例演示如何使用 Load 加载相关实体的筛选集合，如[加载相关实体](~/ef6/querying/related-data.md)中所述：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
