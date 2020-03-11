---
title: 使用相同的 DbContext 类型的 EF Core 的多个模型之间交替
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: a160f0d382ee2a3ac7130ce1ac98eb24b3f79394
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413911"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>在具有相同 DbContext 类型的多个模型之间交替

`OnModelCreating` 中生成的模型可以使用上下文中的属性来更改模型的生成方式。 例如，假设你想要根据某些属性以不同的方式配置实体：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

遗憾的是，此代码不会按原样工作，因为 EF 会构建模型并只运行一次 `OnModelCreating`，出于性能原因缓存结果。 但是，可以挂钩到模型缓存机制，使 EF 知道生成不同模型的属性。

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF 使用 `IModelCacheKeyFactory` 来生成模型的缓存键;默认情况下，EF 假设对于任何给定的上下文类型，模型都是相同的，因此该服务的默认实现将返回只包含上下文类型的键。 若要从相同的上下文类型生成不同的模型，需要将 `IModelCacheKeyFactory` 服务替换为正确的实现;将使用 `Equals` 方法将生成的键与其他模型键进行比较，并考虑影响模型的所有变量：

生成模型缓存密钥时，以下实现会考虑 `IgnoreIntProperty`：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

最后，在上下文的 `OnConfiguring`中注册新 `IModelCacheKeyFactory`：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

有关更多上下文，请参阅[完整的示例项目](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)。
