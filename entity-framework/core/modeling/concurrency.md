---
title: 并发标记的 EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 8a5f3aa09c2a83d5be0998a11ef2ee8100437514
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781139"
---
# <a name="concurrency-tokens"></a>并发标记

> [!NOTE]
> 此页介绍如何配置并发标记。 请参阅[处理并发冲突](../saving/concurrency.md)并发控制 EF Core 以及如何处理应用程序中的并发冲突的示例中的工作原理的详细说明。

配置为并发标记的属性用于实现乐观并发控制。

## <a name="configuration"></a>配置

### <a name="data-annotationstabdata-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-apitabfluent-api"></a>[熟知 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Timestamp/rowversion 是一个属性，在每次插入或更新行时，数据库会自动为其生成新值。 此属性也被视为并发标记，这确保了在你查询行后，如果正在更新的行发生了更改，则会出现异常。 确切的详细信息取决于所使用的数据库提供程序;对于 SQL Server，通常使用*byte []* 属性，该属性将设置为数据库中的*ROWVERSION*列。

可以按如下所示将属性配置为 timestamp/rowversion：

### <a name="data-annotationstabdata-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-apitabfluent-api"></a>[熟知 API](#tab/fluent-api)

[！ code-csharp [Main] （.。/../../samples/core/Modeling/FluentAPI/Timestamp.cs？ name = Timestamp & 高光 = 9，17]

***
