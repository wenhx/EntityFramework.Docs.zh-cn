---
title: EF Core 中的主键
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 8b32bf6417890a954c933a5973a2c90c609beeca
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197280"
---
# <a name="keys-primary"></a>主键

键用作每个实体实例的主要唯一标识符。 使用关系数据库时，这会映射到主键的概念。 您还可以配置不是主键的唯一标识符（有关详细信息，请参阅[备用键](alternate-keys.md) 

可以使用以下方法之一来设置/创建主键。

## <a name="conventions"></a>约定

按照约定，会将名为 `Id` 或 `<type name>Id` 的属性配置为一个实体的键。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a>数据注释

可以使用数据注释将单个属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?highlight=13)]

## <a name="fluent-api"></a>Fluent API

可以使用 Fluent API 将单个属性配置为实体的键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?highlight=11,12)]

Fluent API 还可用于将多个属性配置为实体的键（称为复合键）。 只能使用 Fluent API 配置复合键 - 不能使用约定来设置复合键，也不能使用数据注释来配置复合键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?highlight=11,12)]
