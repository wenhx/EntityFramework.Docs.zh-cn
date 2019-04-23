---
title: 必需/可选属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 564d9e62e2ed4f1a52b569630ed4994529e31dc1
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929805"
---
# <a name="required-and-optional-properties"></a>必需和可选属性

属性被认为是如果它才能包含有效`null`。 如果`null`不是有效的值分配给属性中，则它被视为是必需的属性。

## <a name="conventions"></a>约定

按照约定，其 CLR 类型可以包含 null 的属性将配置为可选 (`string`， `int?`， `byte[]`，等等。)。 将配置的属性的 CLR 类型不能包含 null 所需的方式 (`int`， `decimal`， `bool`，等等。)。

> [!NOTE]  
> 其 CLR 类型不能包含 null 的属性不能配置为可选。 始终将所需的实体框架视为属性。

## <a name="data-annotations"></a>数据注释

可以使用数据批注以指示属性是必需。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Required.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

可以使用 Fluent API 以指示属性是必需。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Required.cs?highlight=11-13)]

