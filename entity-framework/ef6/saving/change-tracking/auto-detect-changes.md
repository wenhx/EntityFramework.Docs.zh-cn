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
# <a name="automatic-detect-changes"></a><span data-ttu-id="a61e6-102">自动检测更改</span><span class="sxs-lookup"><span data-stu-id="a61e6-102">Automatic detect changes</span></span>
<span data-ttu-id="a61e6-103">使用最多 POCO 实体时，会通过检测更改算法来处理实体如何更改（以及需要向数据库发送更新）。</span><span class="sxs-lookup"><span data-stu-id="a61e6-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="a61e6-104">检测更改的工作方式是检测实体的当前属性值与在查询或附加实体时存储在快照中的原始属性值之间的差异。</span><span class="sxs-lookup"><span data-stu-id="a61e6-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="a61e6-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="a61e6-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="a61e6-106">默认情况下，在调用以下方法时，实体框架自动执行检测更改：</span><span class="sxs-lookup"><span data-stu-id="a61e6-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="a61e6-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="a61e6-107">DbSet.Find</span></span>  
- <span data-ttu-id="a61e6-108">DbSet</span><span class="sxs-lookup"><span data-stu-id="a61e6-108">DbSet.Local</span></span>  
- <span data-ttu-id="a61e6-109">DbSet</span><span class="sxs-lookup"><span data-stu-id="a61e6-109">DbSet.Add</span></span>  
- <span data-ttu-id="a61e6-110">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="a61e6-110">DbSet.AddRange</span></span>
- <span data-ttu-id="a61e6-111">DbSet</span><span class="sxs-lookup"><span data-stu-id="a61e6-111">DbSet.Remove</span></span>  
- <span data-ttu-id="a61e6-112">DbSet. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="a61e6-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="a61e6-113">DbSet</span><span class="sxs-lookup"><span data-stu-id="a61e6-113">DbSet.Attach</span></span>  
- <span data-ttu-id="a61e6-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="a61e6-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="a61e6-115">DbContext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="a61e6-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="a61e6-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="a61e6-116">DbContext.Entry</span></span>  
- <span data-ttu-id="a61e6-117">DbChangeTracker</span><span class="sxs-lookup"><span data-stu-id="a61e6-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="a61e6-118">禁用更改的自动检测</span><span class="sxs-lookup"><span data-stu-id="a61e6-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="a61e6-119">如果正在跟踪上下文中的大量实体，并在循环中多次调用其中一种方法，则可以通过在循环的持续时间内关闭更改检测来显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="a61e6-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="a61e6-120">例如：</span><span class="sxs-lookup"><span data-stu-id="a61e6-120">For example:</span></span>  

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

<span data-ttu-id="a61e6-121">不要忘记重新启用循环后的更改检测，我们使用了 try/finally 来确保始终重新启用该循环，即使循环中的代码引发异常也是如此。</span><span class="sxs-lookup"><span data-stu-id="a61e6-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="a61e6-122">禁用和重新启用的替代方法是始终始终关闭更改的自动检测，并调用上下文。ChangeTracker 显式或使用更改跟踪代理。</span><span class="sxs-lookup"><span data-stu-id="a61e6-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="a61e6-123">这两个选项都是高级选项，可轻松地在应用程序中引入微妙 bug，因此请谨慎使用。</span><span class="sxs-lookup"><span data-stu-id="a61e6-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="a61e6-124">如果需要在上下文中添加或删除多个对象，请考虑使用 AddRange 和 DbSet RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="a61e6-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="a61e6-125">此方法在添加或删除操作完成后，只自动检测更改一次。</span><span class="sxs-lookup"><span data-stu-id="a61e6-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
