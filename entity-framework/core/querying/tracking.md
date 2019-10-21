---
title: 跟踪与非跟踪查询 - EF Core
author: smitpatel
ms.date: 10/10/2019
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 66988f936ab75e17620398c8f21e4a32bbc950bd
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445948"
---
# <a name="tracking-vs-no-tracking-queries"></a>跟踪与非跟踪查询

跟踪行为决定了 Entity Framework Core 是否将有关实体实例的信息保留在其更改跟踪器中。 如果已跟踪某个实体，则该实体中检测到的任何更改都会在 `SaveChanges()` 期间永久保存到数据库。 EF Core 还将修复跟踪查询结果中的实体与更改跟踪器中的实体之间的导航属性。

> [!NOTE]
> 从不跟踪[无键实体类型](xref:core/modeling/keyless-entity-types)。 无论在何处提到实体类型，它都是指定义了键的实体类型。

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

## <a name="tracking-queries"></a>跟踪查询

返回实体类型的查询是默认会被跟踪的。 这表示可以更改这些实体实例，然后通过 `SaveChanges()` 持久化这些更改。 在以下示例中，将检测到对博客评分所做的更改，并在 `SaveChanges()` 期间将这些更改持久化到数据库中。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a>非跟踪查询

在只读方案中使用结果时，非跟踪查询十分有用。 可以更快速地执行非跟踪查询，因为无需设置更改跟踪信息。 如果不需要更新从数据库中检索到的实体，则应使用非跟踪查询。 可以将单个查询替换为非跟踪查询。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

还可以在上下文实例级别更改默认跟踪行为：

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>标识解析

由于跟踪查询使用更改跟踪器，因此 EF Core 将在跟踪查询中执行标识解析。 当具体化实体时，如果 EF Core 已被跟踪，则会从更改跟踪器返回相同的实体实例。 如果结果中多次包含相同的实体，则每次会返回相同的实例。 非跟踪查询不会使用更改跟踪器，也不会执行标识解析。 因此会返回实体的新实例，即使结果中多次包含相同的实体也是如此。 此行为与 EF Core 3.0 之前的版本中的行为有所不同，请参阅[早期版本](#previous-versions)。

## <a name="tracking-and-custom-projections"></a>跟踪和自定义投影

即使查询的结果类型不是实体类型，默认情况下 EF Core 也会跟踪结果中包含的实体类型。 在以下返回匿名类型的查询中，结果集中的 `Blog` 实例会被跟踪。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

如果结果集包含来自 LINQ 组合的实体类型，EF Core 将跟踪它们。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

如果结果集不包含任何实体类型，则不会执行跟踪。 在以下查询中，我们返回匿名类型（具有实体中的某些值，但没有实际实体类型的实例）。 查询中没有任何被跟踪的实体。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 EF Core 支持执行顶级投影中的客户端评估。 如果 EF Core 具体化实体实例以进行客户端评估，则会跟踪该实体实例。 此处，由于我们要将 `blog` 实体传递到客户端方法 `StandardizeURL`，因此 EF Core 也会跟踪博客实例。

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

EF Core 不会跟踪结果中包含的无键实体实例。 但 EF Core 会根据上述规则跟踪带有键的实体类型的所有其他实例。

在 EF Core 3.0 之前，某些上述规则的工作方式有所不同。 有关详细信息，请参阅[早期版本](#previous-versions)。

## <a name="previous-versions"></a>早期版本

在 3.0 版之前，EF Core 执行跟踪的方式有一些差异。 显著的差异如下：

- 如[客户端与服务器评估](xref:core/querying/client-eval)页中所述，在 3.0 版之前，EF Core 支持在查询的任何部分中执行客户端评估。 客户端评估导致了实体的具体化，这不是结果的一部分。 因此 EF Core 分析了结果以检测要跟踪的内容。此设计有一些不同之处，如下所示：
  - 投影中的客户端评估（导致具体化，但未返回具体化的实体实例）未被跟踪。 以下示例未跟踪 `blog` 实体。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - 在某些情况下，EF Core 未跟踪来自 LINQ 组合的对象。 以下示例未跟踪 `Post`。
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- 只要查询结果中包含无键实体类型，整个查询就会进行非跟踪。 这表示不会跟踪结果中包含的带有键的实体类型。
- EF Core 在非跟踪查询中执行标识解析。 它使用了弱引用来跟踪已返回的实体。 因此，如果结果集多次包含相同的实体，则每次会返回相同的实例。 尽管具有相同标识的上一个结果超出了范围并进行了垃圾回收，EF Core 也会返回新实例。
