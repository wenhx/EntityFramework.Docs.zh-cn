---
title: 必需/可选属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 7200cd2eeeba2f22365ef09b1f50edd077240130
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149145"
---
# <a name="required-and-optional-properties"></a>必需属性和可选属性

如果属性对于包含`null`有效，则将其视为可选。 如果`null`不是要分配给属性的有效值，则将其视为必需属性。

## <a name="conventions"></a>约定

按照约定，.net 类型可以包含 null 值的属性将配置为可选（`string`、 `int?`、 `byte[]`等）。 将配置其 CLR 类型不能包含 null 的属性（`int`、 `decimal`、 `bool`等）。

> [!NOTE]  
> 不能将其 .NET 类型不能包含 null 的属性配置为可选。 实体框架，将始终认为属性是必需的。

## <a name="data-annotations"></a>数据注释

您可以使用数据批注来指示属性是必需的。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

您可以使用 "熟知 API" 来指示属性是必需的。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

