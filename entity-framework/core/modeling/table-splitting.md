---
title: 表拆分-EF Core
description: 如何使用 Entity Framework Core 配置表拆分
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: de24f8903af79ebd7f68e6b74288257883c1fa8d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414697"
---
# <a name="table-splitting"></a>表拆分

EF Core 允许将两个或多个实体映射到单个行。 这称为 "_表拆分_" 或 "_表共享_"。

## <a name="configuration"></a>配置

若要使用表拆分，则需要将多个实体类型映射到同一个表中，将主键映射到相同的列，并且在同一个实体类型的主键与另一个实体类型的主键之间配置至少一个关系。

表拆分的常见方案是只使用表中的部分列，以获得更好的性能或封装。

在此示例中 `Order` 表示 `DetailedOrder`的子集。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

除了所需的配置之外，我们还 `Property(o => o.Status).HasColumnName("Status")` 将 `DetailedOrder.Status` 映射到与 `Order.Status`相同的列。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> 有关更多上下文，请参阅[完整的示例项目](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。

## <a name="usage"></a>用法

使用表拆分来保存和查询实体的方式与其他实体相同：

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>可选依赖实体

> [!NOTE]
> EF Core 3.0 中引入了此功能。

如果依赖实体使用的所有列都 `NULL` 在数据库中，则查询时将不会创建该实体的任何实例。 这允许对一个可选的依赖实体建模，其中主体的关系属性将为 null。 请注意，这也会导致所有依赖属性均为可选，并设置为 `null`，这可能不是预期的。

## <a name="concurrency-tokens"></a>并发令牌

如果共享表的任何实体类型都有并发标记，则还必须将其包含在所有其他实体类型中。 当只更新映射到同一个表中的一个实体时，必须使用此值来避免陈旧并发令牌值。

若要避免向使用代码公开并发标记，可以将其创建为[影子属性](xref:core/modeling/shadow-properties)：

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
