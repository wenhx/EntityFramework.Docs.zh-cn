---
title: 并发标记的 EF Core
author: rowanmiller
ms.date: 03/03/2018
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: db768c1de99000be91d33764ccd3c3924237f8bb
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197452"
---
# <a name="concurrency-tokens"></a>并发标记

> [!NOTE]
> 此页介绍如何配置并发标记。 请参阅[处理并发冲突](../saving/concurrency.md)并发控制 EF Core 以及如何处理应用程序中的并发冲突的示例中的工作原理的详细说明。

配置为并发标记的属性用于实现乐观并发控制。

## <a name="conventions"></a>约定

按照约定，属性永远不会配置为并发标记。

## <a name="data-annotations"></a>数据注释

您可以使用数据批注将属性配置为并发标记。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs#ConfigureConcurrencyAnnotations)]

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 将属性配置为并发标记。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs#ConfigureConcurrencyFluent)]

## <a name="timestamprow-version"></a>时间戳/行版本

时间戳是一个属性，在每次插入或更新行时，数据库都会生成一个新值。 此属性也被视为并发标记。 这可以确保在其他人修改了您尝试在查询数据后尝试更新的行时，您将收到异常。

实现此目的的方式取决于所使用的数据库提供程序。 对于 SQL Server，时间戳通常用于*byte []* 属性，该属性将设置为数据库中的*ROWVERSION*列。

### <a name="conventions"></a>约定

按照约定，属性永远不会配置为时间戳。

### <a name="data-annotations"></a>数据注释

您可以使用数据批注将属性配置为时间戳。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs#ConfigureTimestampAnnotations)]

### <a name="fluent-api"></a>Fluent API

你可以使用熟知的 API 将属性配置为时间戳。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs#ConfigureTimestampFluent)]
