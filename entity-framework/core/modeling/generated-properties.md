---
title: 生成的值的 EF Core
description: 如何在使用时配置属性的值生成 Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: 7fa3eae5e2edb7b4c40ed4f99ce4a29f367e622a
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824698"
---
# <a name="generated-values"></a>生成的值

## <a name="value-generation-patterns"></a>值生成模式

有三个可用于属性的值生成模式：

* 无值生成
* 在添加时生成值
* 在添加或更新时生成值

### <a name="no-value-generation"></a>无值生成

没有值生成意味着，需始终提供要保存到数据库的有效值。 必须先将有效的值赋予新的实体，再将这些新的实体添加到上下文中。

### <a name="value-generated-on-add"></a>在添加时生成值

在添加时生成值，意思是为新实体生成值。

根据所用数据库提供程序的不同，值可能会通过 EF 在客户端生成或者由数据库生成。 如果由数据库生成值，则当你将实体添加到上下文时，EF 可能会赋予一个临时值。 而后，由数据库生成的值会在 `SaveChanges()` 过程中替换这个临时值。

如果将一个实体添加到已经为属性赋予值的上下文，则 EF 会尝试插入该值而不是生成新值。 属性被认为已赋值的前提是该属性未被赋予 CLR 默认值（`string` 的默认值为 `null`，`int` 的默认值为 `0`，`Guid.Empty` 的默认值为 `Guid`，等等)。 有关详细信息，请参阅[已生成属性的显式值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]  
> 如何为添加的实体生成值取决于所用数据库提供程序。 数据库提供程序可能会为某些属性类型自动设置值的生成，但其他的属性类型可能要求你手动设置值的生成方式。
>
> 例如，使用 SQL Server 时，会自动生成 `GUID` 属性的值（使用 SQL Server 的顺序 GUID 算法）。 但是，如果指定在添加时生成 `DateTime` 属性，则必须设置生成值的方法。 若要执行此操作，一种方法是配置 `GETDATE()` 的默认值，请参阅[默认值](relational/default-values.md)。

### <a name="value-generated-on-add-or-update"></a>在添加或更新时生成值

在添加或更新时生成值，意味着在每次保存该记录（插入或更新）时生成新值。

就像`value generated on add`一样，如果为一个实体的新增实例上的属性指定了一个值，则 EF 会尝试插入该值而不是生成新值。 还可以在更新时设置显式值。 有关详细信息，请参阅[已生成属性的显式值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 如何在添加和更新实体时生成值取决于所用数据库提供程序。 数据库提供程序可能会为某些属性类型自动设置值的生成，但其他的属性类型会要求你手动设置值的生成方式。
>
> 例如，在使用 SQL Server 时，设置为在添加和更新时生成值并标注为并发标记的 `byte[]` 属性会被设置为 `rowversion` 数据类型，以便在数据库中生成值。 但是，如果指定在添加或更新时生成 `DateTime` 属性，则必须设置生成值的方法。 若要执行此操作，一种方法是配置 `GETDATE()` 的默认值（请参阅[默认值](relational/default-values.md)）以生成新行的值。 然后即可使用数据库触发器在更新过程中生成值（如下面的示例触发器所示）。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>约定

默认情况下，将设置 short、int、long 或 Guid 类型的非复合主键，使其在 add 时生成值。 所有其他属性会被设置为不生成值。

## <a name="data-annotations"></a>数据注释

### <a name="no-value-generation-data-annotations"></a>无值生成（数据批注）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>在添加时生成值（数据注释）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> 这只是让 EF 知道为已添加的实体生成值，并不保证 EF 会设置实际机制来生成值。 请参阅[在添加时生成值](#value-generated-on-add)部分，了解更多详细信息。

### <a name="value-generated-on-add-or-update-data-annotations"></a>在添加或更新时生成值（数据注释）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 这只是让 EF 知道为添加或更新的实体生成值，并不保证 EF 会设置实际机制来生成值。 请参阅[在添加或更新时生成值](#value-generated-on-add-or-update)部分，了解更多详细信息。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来更改给定属性的值生成模式。

### <a name="no-value-generation-fluent-api"></a>无值生成（流畅 API）

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>在添加时生成值 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` 只是让 EF 知道为添加的实体生成值，并不保证 EF 会设置实际机制来生成值。  请参阅[在添加时生成值](#value-generated-on-add)部分，了解更多详细信息。

### <a name="value-generated-on-add-or-update-fluent-api"></a>在添加或更新时生成值 (Fluent API)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 这只是让 EF 知道为添加或更新的实体生成值，并不保证 EF 会设置实际机制来生成值。 请参阅[在添加或更新时生成值](#value-generated-on-add-or-update)部分，了解更多详细信息。
