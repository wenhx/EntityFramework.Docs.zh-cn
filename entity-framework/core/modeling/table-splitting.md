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
# <a name="table-splitting"></a><span data-ttu-id="1d72b-102">表拆分</span><span class="sxs-lookup"><span data-stu-id="1d72b-102">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="1d72b-103">此功能是在 EF Core 2.0 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="1d72b-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="1d72b-104">EF Core，即可将两个或多个实体映射到单个行。</span><span class="sxs-lookup"><span data-stu-id="1d72b-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="1d72b-105">这称为_表拆分_或_表共享_。</span><span class="sxs-lookup"><span data-stu-id="1d72b-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="1d72b-106">配置</span><span class="sxs-lookup"><span data-stu-id="1d72b-106">Configuration</span></span>

<span data-ttu-id="1d72b-107">若要使用表拆分实体类型都必须映射到同一个表，具有主键映射到相同的列和至少一个实体类型的主键和同一个表中的另一个之间配置关系。</span><span class="sxs-lookup"><span data-stu-id="1d72b-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="1d72b-108">表拆分的常见方案获得更高的性能或封装在表中使用的列的子集。</span><span class="sxs-lookup"><span data-stu-id="1d72b-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="1d72b-109">在此示例中`Order`的子集`DetailedOrder`。</span><span class="sxs-lookup"><span data-stu-id="1d72b-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="1d72b-110">除了所需的配置中，我们将调用`HasBaseType((string)null)`以免映射`DetailedOrder`相同的层次结构中`Order`。</span><span class="sxs-lookup"><span data-stu-id="1d72b-110">In addition to the required configuration we call `HasBaseType((string)null)` to avoid mapping `DetailedOrder` in the same hierarchy as `Order`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

<span data-ttu-id="1d72b-111">请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)有关更多上下文。</span><span class="sxs-lookup"><span data-stu-id="1d72b-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="1d72b-112">用法</span><span class="sxs-lookup"><span data-stu-id="1d72b-112">Usage</span></span>

<span data-ttu-id="1d72b-113">保存和查询实体使用表拆分完成相同的方式与其他实体，唯一的区别是必须插入跟踪共享一个行的所有实体。</span><span class="sxs-lookup"><span data-stu-id="1d72b-113">Saving and querying entities using table splitting is done in the same way as other entities, the only difference is that all entities sharing a row must be tracked for the insert.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="1d72b-114">并发令牌</span><span class="sxs-lookup"><span data-stu-id="1d72b-114">Concurrency tokens</span></span>

<span data-ttu-id="1d72b-115">如果任何共享一个表的实体类型具有并发标记则在所有其他实体类型，以避免过时的并发令牌值，仅其中一个实体映射到同一个表更新时必须包含下列它。</span><span class="sxs-lookup"><span data-stu-id="1d72b-115">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="1d72b-116">若要避免将其公开到使用代码就可以创建一个卷影状态中的。</span><span class="sxs-lookup"><span data-stu-id="1d72b-116">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]