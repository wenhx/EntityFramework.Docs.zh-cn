---
title: 密钥（主要）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 66c64c389294e8e109a614a2bea8311932660dea
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655946"
---
# <a name="keys-primary"></a>键（主要）

键充当每个实体实例的主唯一标识符。 使用关系数据库时，这会映射到*主键*的概念。 你还可以配置不是主键的唯一标识符（有关详细信息，请参阅[备用键](alternate-keys.md)）。

可以使用以下方法之一来设置/创建主键。

## <a name="conventions"></a>约定

按照约定，将名为 `Id` 或 `<type name>Id` 的属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyIdhighlight=3)]

## <a name="data-annotations"></a>数据注释

您可以使用数据批注将单个属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 将单个属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

你还可以使用熟知的 API 将多个属性配置为实体的密钥（称为组合键）。 只能使用熟知的 API 约定来配置复合密钥，而不会设置组合键，也不能使用数据批注来配置复合密钥。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]
