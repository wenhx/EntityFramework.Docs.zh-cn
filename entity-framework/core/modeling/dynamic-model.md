---
title: 使用相同的 DbContext 类型的 EF Core 的多个模型之间交替
author: AndriySvyryd
ms.date: 12/10/2017
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 1d87efb668c12a2862583fba16a6c444b3cda9de
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994981"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>具有相同 DbContext 类型的多个模型之间切换

`OnModelCreating` 中构建的模型可以使用上下文中的属性来更改模型的构建方式。例如，它可以用于排除某个属性：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory
但是，如果尝试执行上述操作而不进行其他更改，则为 `IgnoreIntProperty` 的任何值创建新上下文时都会获得相同的模型。这是由 EF 使用的模型缓存机制导致的，EF 使用此机制通过仅调用模型 `OnModelCreating` 一次并且缓存模型来提高性能。

默认情况下，EF 假定用于任何给定上下文类型的模型都是相同的。为实现此结果，`IModelCacheKeyFactory` 的默认实现返回一个仅包含上下文类型的键。若要更改此行为，需要更换 `IModelCacheKeyFactory` 服务。新的实现需要返回一个对象，该对象可使用 `Equals` 方法（该方法不考虑影响该模型的所有变量）与其他模型键进行比较：

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
