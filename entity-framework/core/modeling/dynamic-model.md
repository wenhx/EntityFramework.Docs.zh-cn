---
title: 使用相同的 DbContext 类型的 EF Core 的多个模型之间交替
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 156d5666cbd9352b274ddc70c99704ca62aeb1fd
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781126"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="77bc5-102">在具有相同 DbContext 类型的多个模型之间交替</span><span class="sxs-lookup"><span data-stu-id="77bc5-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="77bc5-103">`OnModelCreating` 中生成的模型可以使用上下文中的属性来更改模型的生成方式。</span><span class="sxs-lookup"><span data-stu-id="77bc5-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="77bc5-104">例如，假设你想要根据某些属性以不同的方式配置实体：</span><span class="sxs-lookup"><span data-stu-id="77bc5-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="77bc5-105">遗憾的是，此代码不会按原样工作，因为 EF 会构建模型并只运行一次 `OnModelCreating`，出于性能原因缓存结果。</span><span class="sxs-lookup"><span data-stu-id="77bc5-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="77bc5-106">但是，可以挂钩到模型缓存机制，使 EF 知道生成不同模型的属性。</span><span class="sxs-lookup"><span data-stu-id="77bc5-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="77bc5-107">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="77bc5-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="77bc5-108">EF 使用 `IModelCacheKeyFactory` 来生成模型的缓存键;默认情况下，EF 假设对于任何给定的上下文类型，模型都是相同的，因此该服务的默认实现将返回只包含上下文类型的键。</span><span class="sxs-lookup"><span data-stu-id="77bc5-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="77bc5-109">若要从相同的上下文类型生成不同的模型，需要将 `IModelCacheKeyFactory` 服务替换为正确的实现;将使用 `Equals` 方法将生成的键与其他模型键进行比较，并考虑影响模型的所有变量：</span><span class="sxs-lookup"><span data-stu-id="77bc5-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct  implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model:</span></span>

<span data-ttu-id="77bc5-110">生成模型缓存密钥时，以下实现会考虑 `IgnoreIntProperty`：</span><span class="sxs-lookup"><span data-stu-id="77bc5-110">The following implementation takes the `IgnoreIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="77bc5-111">最后，在上下文的 `OnConfiguring`中注册新 `IModelCacheKeyFactory`：</span><span class="sxs-lookup"><span data-stu-id="77bc5-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="77bc5-112">有关更多上下文，请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)。</span><span class="sxs-lookup"><span data-stu-id="77bc5-112">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
