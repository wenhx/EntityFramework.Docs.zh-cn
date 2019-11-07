---
title: 在具有相同 DbContext 类型的多个模型之间交替-EF Core
author: AndriySvyryd
ms.date: 12/10/2017
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 034076b1595894e80b98467354f6c9f139bd7426
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655730"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="8da92-102">在具有相同 DbContext 类型的多个模型之间交替</span><span class="sxs-lookup"><span data-stu-id="8da92-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="8da92-103">`OnModelCreating` 中生成的模型可以使用上下文中的属性来更改模型的生成方式。</span><span class="sxs-lookup"><span data-stu-id="8da92-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="8da92-104">例如，它可用于排除某个属性：</span><span class="sxs-lookup"><span data-stu-id="8da92-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="8da92-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="8da92-105">IModelCacheKeyFactory</span></span>

<span data-ttu-id="8da92-106">但是，如果尝试执行上述操作而不进行其他更改，则每次为 `IgnoreIntProperty`的任何值创建新上下文时，都将获得相同的模型。</span><span class="sxs-lookup"><span data-stu-id="8da92-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="8da92-107">这是因为模型缓存机制 EF 使用来通过只调用一次 `OnModelCreating` 并缓存模型来提高性能。</span><span class="sxs-lookup"><span data-stu-id="8da92-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="8da92-108">默认情况下，EF 假设对于任何给定的上下文类型，模型都是相同的。</span><span class="sxs-lookup"><span data-stu-id="8da92-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="8da92-109">为实现此目的，`IModelCacheKeyFactory` 的默认实现将返回只包含上下文类型的键。</span><span class="sxs-lookup"><span data-stu-id="8da92-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="8da92-110">若要更改此更改，需要替换 `IModelCacheKeyFactory` 服务。</span><span class="sxs-lookup"><span data-stu-id="8da92-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="8da92-111">新实现需要返回一个对象，该对象可使用 `Equals` 方法进行比较，该方法将影响模型的所有变量：</span><span class="sxs-lookup"><span data-stu-id="8da92-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
