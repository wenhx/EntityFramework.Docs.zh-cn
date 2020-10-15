---
title: 自动检测更改-EF6
description: 自动检测实体框架6中的更改
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 159143a9ade64a65e857a30117e577b21e0b9f98
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064491"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="350c6-103">自动检测更改</span><span class="sxs-lookup"><span data-stu-id="350c6-103">Automatic detect changes</span></span>
<span data-ttu-id="350c6-104">使用大多数 POCO 实体时，确定实体的更改方式 (，因此需要将更新发送到数据库) 由检测更改算法处理。</span><span class="sxs-lookup"><span data-stu-id="350c6-104">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="350c6-105">检测更改的工作方式是检测实体的当前属性值与在查询或附加实体时存储在快照中的原始属性值之间的差异。</span><span class="sxs-lookup"><span data-stu-id="350c6-105">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="350c6-106">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="350c6-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="350c6-107">默认情况下，在调用以下方法时，实体框架自动执行检测更改：</span><span class="sxs-lookup"><span data-stu-id="350c6-107">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="350c6-108">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="350c6-108">DbSet.Find</span></span>  
- <span data-ttu-id="350c6-109">DbSet</span><span class="sxs-lookup"><span data-stu-id="350c6-109">DbSet.Local</span></span>  
- <span data-ttu-id="350c6-110">DbSet</span><span class="sxs-lookup"><span data-stu-id="350c6-110">DbSet.Add</span></span>  
- <span data-ttu-id="350c6-111">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="350c6-111">DbSet.AddRange</span></span>
- <span data-ttu-id="350c6-112">DbSet</span><span class="sxs-lookup"><span data-stu-id="350c6-112">DbSet.Remove</span></span>  
- <span data-ttu-id="350c6-113">DbSet. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="350c6-113">DbSet.RemoveRange</span></span>
- <span data-ttu-id="350c6-114">DbSet</span><span class="sxs-lookup"><span data-stu-id="350c6-114">DbSet.Attach</span></span>  
- <span data-ttu-id="350c6-115">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="350c6-115">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="350c6-116">DbContext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="350c6-116">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="350c6-117">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="350c6-117">DbContext.Entry</span></span>  
- <span data-ttu-id="350c6-118">DbChangeTracker</span><span class="sxs-lookup"><span data-stu-id="350c6-118">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="350c6-119">禁用更改的自动检测</span><span class="sxs-lookup"><span data-stu-id="350c6-119">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="350c6-120">如果正在跟踪上下文中的大量实体，并在循环中多次调用其中一种方法，则可以通过在循环的持续时间内关闭更改检测来显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="350c6-120">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="350c6-121">例如：</span><span class="sxs-lookup"><span data-stu-id="350c6-121">For example:</span></span>  

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

<span data-ttu-id="350c6-122">不要忘记重新启用循环后的更改检测，我们使用了 try/finally 来确保始终重新启用该循环，即使循环中的代码引发异常也是如此。</span><span class="sxs-lookup"><span data-stu-id="350c6-122">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="350c6-123">禁用和重新启用的替代方法是始终始终关闭更改的自动检测，并调用上下文。ChangeTracker 显式或使用更改跟踪代理。</span><span class="sxs-lookup"><span data-stu-id="350c6-123">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="350c6-124">这两个选项都是高级选项，可轻松地在应用程序中引入微妙 bug，因此请谨慎使用。</span><span class="sxs-lookup"><span data-stu-id="350c6-124">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="350c6-125">如果需要在上下文中添加或删除多个对象，请考虑使用 AddRange 和 DbSet RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="350c6-125">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="350c6-126">此方法在添加或删除操作完成后，只自动检测更改一次。</span><span class="sxs-lookup"><span data-stu-id="350c6-126">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
