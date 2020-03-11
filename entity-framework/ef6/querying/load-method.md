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
# <a name="the-load-method"></a><span data-ttu-id="dd766-102">加载方法</span><span class="sxs-lookup"><span data-stu-id="dd766-102">The Load Method</span></span>
<span data-ttu-id="dd766-103">在某些情况下，你可能需要将实体从数据库加载到上下文中，而不会立即对这些实体执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="dd766-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="dd766-104">这是一个很好的例子，就是加载用于数据绑定的实体，如[本地数据](~/ef6/querying/local-data.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="dd766-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="dd766-105">实现此目的的一个常见方法是编写 LINQ 查询，然后对其调用 System.linq.enumerable.tolist，只是立即放弃创建的列表。</span><span class="sxs-lookup"><span data-stu-id="dd766-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="dd766-106">负载扩展方法的工作方式与 System.linq.enumerable.tolist 相同，只是它可以避免完全创建列表。</span><span class="sxs-lookup"><span data-stu-id="dd766-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="dd766-107">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="dd766-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="dd766-108">下面是两个使用 Load 的示例。</span><span class="sxs-lookup"><span data-stu-id="dd766-108">Here are two examples of using Load.</span></span> <span data-ttu-id="dd766-109">第一种方式是从 Windows 窗体数据绑定应用程序中获取，在将其绑定到本地集合之前，将使用 Load 查询实体，如[本地数据](~/ef6/querying/local-data.md)中所述：</span><span class="sxs-lookup"><span data-stu-id="dd766-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="dd766-110">第二个示例演示如何使用 Load 加载相关实体的筛选集合，如[加载相关实体](~/ef6/querying/related-data.md)中所述：</span><span class="sxs-lookup"><span data-stu-id="dd766-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

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
