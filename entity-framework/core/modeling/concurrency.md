---
title: 并发令牌-EF Core
description: 在 Entity Framework Core 模型中配置乐观并发控制的并发令牌
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/concurrency
ms.openlocfilehash: fab9ad99609cd8e724b550000f6f06ef8c172c81
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071610"
---
# <a name="concurrency-tokens"></a>并发标记

> [!NOTE]
> 此页介绍如何配置并发标记。 有关并发控制的工作原理的详细说明，请参阅 [处理并发冲突](xref:core/saving/concurrency) EF Core 和如何处理应用程序中的并发冲突的示例。

配置为并发标记的属性用于实现乐观并发控制。

## <a name="configuration"></a>配置

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Timestamp/rowversion 是一个属性，在每次插入或更新行时，数据库会自动为其生成新值。 此属性也被视为并发标记，这确保了在你查询行后，如果正在更新的行发生了更改，则会出现异常。 确切的详细信息取决于所使用的数据库提供程序;对于 SQL Server，通常使用 *byte []* 属性，该属性将设置为数据库中的 *ROWVERSION* 列。

可以按如下所示将属性配置为 timestamp/rowversion：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
