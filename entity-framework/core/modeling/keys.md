---
title: EF Core 中的主键
description: 如何在使用 Entity Framework Core 时配置实体类型的密钥
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: fdaccb42259ba9dad97a05c626edd0291ca96cb0
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824620"
---
# <a name="keys-primary"></a>键（主要）

键用作每个实体实例的主要唯一标识符。 使用关系数据库时，这会映射到主键的概念。 您还可以配置不是主键的唯一标识符（有关详细信息，请参阅[备用键](alternate-keys.md)

可以使用以下方法之一来设置/创建主键。

## <a name="conventions"></a>约定

默认情况下，将名为 `Id` 或 `<type name>Id` 的属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3)]

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyTypeNameId.cs?name=KeyId&highlight=3)]

> [!NOTE]
> [拥有的实体类型](xref:core/modeling/owned-entities)使用不同的规则来定义密钥。

## <a name="data-annotations"></a>数据注释

可以使用数据注释将单个属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

可以使用 Fluent API 将单个属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

Fluent API 还可用于将多个属性配置为实体的键（称为复合键）。 只能使用 Fluent API 配置复合键 - 不能使用约定来设置复合键，也不能使用数据注释来配置复合键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]

## <a name="key-types-and-values"></a>键类型和值

EF Core 支持使用任何基元类型的属性作为主键，包括 `string`、`Guid`、`byte[]` 和其他项。 但并非所有数据库都支持它们。 在某些情况下，可以自动将键值转换为支持的类型，否则应[手动指定](xref:core/modeling/value-conversions)转换。

在将新实体添加到上下文时，键属性必须始终具有非默认值，但某些类型将[由数据库生成](xref:core/modeling/generated-properties)。 在这种情况下，当添加实体进行跟踪时，EF 会尝试生成一个临时值。 在调用[SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)后，临时值将替换为数据库生成的值。

> [!Important]
> 如果某个键属性具有数据库生成的值，并且在添加实体时指定了一个非默认值，则 EF 将假定该实体在数据库中已存在，并且将尝试进行更新，而不是插入一个新的值。 若要避免这种情况，请禁用值生成或了解[如何为生成的属性指定显式值](../saving/explicit-values-generated-properties.md)。