---
title: 备用键-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: e5bb0602adb465435c8e88d045395a9424b2d9a3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655775"
---
# <a name="alternate-keys"></a>备用键

除了主键外，备用键还可用作每个实体实例的替代唯一标识符。 备用键可用作关系的目标。 在使用关系数据库时，这会映射到替换键列上的唯一索引/约束的概念，以及引用列的一个或多个 foreign key 约束。

> [!TIP]  
> 如果只想强制列的唯一性，则需要唯一索引而不是备用键，请参阅[索引](indexes.md)。 在 EF 中，除唯一索引外，替代键提供的功能更多，因为它们可用作外键的目标。

通常会在需要时为您引入备用密钥，而不需要手动配置。 有关更多详细信息，请参阅[约定](#conventions)。

## <a name="conventions"></a>约定

按照约定，当你将不是主键的属性标识为关系的目标时，会为你引入备用密钥。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

## <a name="data-annotations"></a>数据注释

不能使用数据批注配置替代键。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 将单个属性配置为备用密钥。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=7,8)]

你还可以使用熟知的 API 将多个属性配置为备用密钥（称为复合备用键）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=7,8)]
