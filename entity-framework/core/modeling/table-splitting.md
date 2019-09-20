---
title: 表拆分-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 684fcfbb66debfd1b89e23c8aaf0a32909378c6b
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149190"
---
# <a name="table-splitting"></a>表拆分

>[!NOTE]
> 此功能是在 EF Core 2.0 中的新增功能。

EF Core 允许将两个或多个实体映射到单个行。 这称为 "_表拆分_" 或 "_表共享_"。

## <a name="configuration"></a>配置

若要使用表拆分，则需要将多个实体类型映射到同一个表中，将主键映射到相同的列，并且在同一个实体类型的主键与另一个实体类型的主键之间配置至少一个关系。

表拆分的常见方案是只使用表中的部分列，以获得更好的性能或封装。

在此示例`Order`中，表示的`DetailedOrder`子集。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

除了所需的配置之外，我们`Property(o => o.Status).HasColumnName("Status")`还调用`DetailedOrder.Status`将映射到与`Order.Status`相同的列。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> 有关更多上下文，请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。

## <a name="usage"></a>用法

使用表拆分来保存和查询实体的方式与处理其他实体的方式相同。 从 EF Core 3.0 开始，从属实体引用可以为`null`。 如果依赖实体使用的所有列都`NULL`是数据库，则查询时将不会创建该数据库的任何实例。 这也会导致所有属性都是可选的并且设置为`null`，这可能不是预期的。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a>并发令牌

如果共享表的任何实体类型都具有并发标记，则必须将其包含在所有其他实体类型中，以避免在仅更新映射到同一个表的一个实体时出现过时并发令牌值。

为避免向使用代码公开它，可以在影子状态中创建一个。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]