---
title: 继承-EF Core
description: 如何使用 Entity Framework Core 配置实体类型继承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 4d43a432174c92ab7f3f9d78a234aefb0a4a17e8
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824674"
---
# <a name="inheritance"></a>继承

EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。

## <a name="conventions"></a>约定

默认情况下，由数据库提供程序确定如何在数据库中表示继承。 有关如何使用关系数据库提供程序来处理此情况的详细说明，请参阅[继承（关系数据库）](relational/inheritance.md) 。

如果模型中显式包括两个或更多个继承类型，则 EF 仅会设置继承。 EF 不会扫描模型中未包含的基类型或派生类型。 可以通过为继承层次结构中的每个类型公开*DbSet\<TEntity >* ，在模型中包含类型。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

如果不想公开层次结构中一个或多个实体的*DbSet\<TEntity >* ，可以使用熟知的 API 来确保它们包含在模型中。
如果不依赖约定，可以使用 `HasBaseType`显式指定基类型。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> 您可以使用 `.HasBaseType((Type)null)` 从层次结构中删除实体类型。

## <a name="data-annotations"></a>数据注释

不能使用数据批注来配置继承。

## <a name="fluent-api"></a>Fluent API

用于继承的流畅 API 取决于所使用的数据库提供程序。 有关可为关系数据库提供程序执行的配置，请参阅[继承（关系数据库）](relational/inheritance.md) 。
