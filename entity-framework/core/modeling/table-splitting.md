---
title: 表拆分-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 4a0bfaf017106a0bfdff084b1c472bdc17459a89
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562581"
---
# <a name="table-splitting"></a>表拆分

>[!NOTE]
> 此功能是在 EF Core 2.0 中的新增功能。

EF Core，即可将两个或多个实体映射到单个行。 这称为_表拆分_或_表共享_。

## <a name="configuration"></a>配置

若要使用表拆分实体类型都必须映射到同一个表，具有主键映射到相同的列和至少一个实体类型的主键和同一个表中的另一个之间配置关系。

表拆分的常见方案获得更高的性能或封装在表中使用的列的子集。

在此示例中`Order`的子集`DetailedOrder`。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

除了所需的配置中，我们将调用`HasBaseType((string)null)`以免映射`DetailedOrder`相同的层次结构中`Order`。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)有关更多上下文。

## <a name="usage"></a>用法

保存和查询实体使用表拆分完成相同的方式与其他实体，唯一的区别是必须插入跟踪共享一个行的所有实体。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a>并发令牌

如果任何共享一个表的实体类型具有并发标记则在所有其他实体类型，以避免过时的并发令牌值，仅其中一个实体映射到同一个表更新时必须包含下列它。

若要避免将其公开到使用代码就可以创建一个卷影状态中的。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]