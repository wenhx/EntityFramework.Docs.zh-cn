---
title: 包括和排除属性 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/included-properties
ms.openlocfilehash: 022534091bb48e491c8808791a401216a339d7b0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929818"
---
# <a name="including--excluding-properties"></a>包括和排除属性

模型中包含属性意味着 EF 拥有该属性的元数据，并将尝试从数据库读取值或者向数据库写入值。

## <a name="conventions"></a>约定

按照约定，模型所含的那些公共属性都拥有一个 getter 和一个 setter。

## <a name="data-annotations"></a>数据注释

您可以使用数据注释方式来从模型中排除某个属性。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreProperty.cs?highlight=17)]

## <a name="fluent-api"></a>Fluent API

您也可以用Fluent API 从模型中排除某个属性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreProperty.cs?highlight=12,13)]
