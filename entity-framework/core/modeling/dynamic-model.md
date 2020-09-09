---
title: 在具有相同 DbContext 类型的多个模型之间交替-EF Core
description: 使用 Entity Framework Core 在具有相同 DbContext 类型的多个模型之间交替
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 436cb46e002438f391c654b64efdfacf494d580d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617528"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>在具有相同 DbContext 类型的多个模型之间交替

内置的模型 `OnModelCreating` 可以使用上下文中的属性来更改模型的生成方式。 例如，假设你想要根据某些属性以不同的方式配置实体：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

遗憾的是，此代码不会按原样工作，因为 EF 只构建模型并运行 `OnModelCreating` 一次，因此性能原因会缓存结果。 但是，可以挂钩到模型缓存机制，使 EF 知道生成不同模型的属性。

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF 使用 `IModelCacheKeyFactory` 来生成模型的缓存键; 默认情况下，ef 假设对于任何给定的上下文类型，模型都是相同的，因此该服务的默认实现将返回只包含上下文类型的键。 若要从相同的上下文类型生成不同的模型，您需要将该服务替换为 `IModelCacheKeyFactory` 正确的实现; 使用方法将生成的键与其他模型键进行比较，并考虑 `Equals` 影响模型的所有变量。

以下实现在 `UseIntProperty` 生成模型缓存键时采用了 into 帐户：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

最后， `IModelCacheKeyFactory` 在上下文中注册新的 `OnConfiguring` ：

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

有关更多上下文，请参阅 [完整的示例项目](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) 。
