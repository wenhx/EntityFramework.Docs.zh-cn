---
title: 生成的值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
uid: core/modeling/generated-properties
ms.openlocfilehash: 6643d3c5c9b3363e450e820793f449a41e2eba80
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655750"
---
# <a name="generated-values"></a>生成的值

## <a name="value-generation-patterns"></a>值生成模式

有三个可用于属性的值生成模式：

* 无值生成
* 添加时生成的值
* 添加或更新时生成的值

### <a name="no-value-generation"></a>无值生成

无值生成意味着您将始终提供要保存到数据库中的有效值。 必须先将此有效值分配给新实体，然后才能将其添加到上下文中。

### <a name="value-generated-on-add"></a>添加时生成的值

"添加时生成的值" 表示为新实体生成值。

根据所使用的数据库提供程序，值可能是由 EF 或数据库中的客户端生成的。 如果值是由数据库生成的，则在将实体添加到上下文时，EF 可能会分配临时值。 在 `SaveChanges()`期间，此临时值将替换为数据库生成的值。

如果向具有分配给属性的值的上下文添加实体，则 EF 将尝试插入该值，而不是生成新的值。 如果某个属性未分配 CLR 默认值（`null` 用于 `string`，`0` 用于 `int`，`Guid.Empty` `Guid`等），则该属性被视为已分配值。 有关详细信息，请参阅[生成的属性的显式值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]  
> 如何为添加的实体生成值将取决于所使用的数据库提供程序。 数据库提供程序可以为某些属性类型自动设置值生成，但其他属性可能要求您手动设置如何生成值。
>
> 例如，使用 SQL Server 时，将自动为 `GUID` 属性（使用 SQL Server 顺序 GUID 算法）生成值。 但是，如果您指定在添加时生成 `DateTime` 属性，则必须设置一个方法来生成值。 执行此操作的一种方法是配置默认值 `GETDATE()`，请参阅[默认值](relational/default-values.md)。

### <a name="value-generated-on-add-or-update"></a>添加或更新时生成的值

添加或更新时生成的值意味着每次保存记录（insert 或 update）时都会生成一个新值。

与 `value generated on add`一样，如果为新添加的实体实例的属性指定值，则将插入该值，而不是要生成的值。 还可以在更新时设置显式值。 有关详细信息，请参阅[生成的属性的显式值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 为添加的和更新的实体生成值的方式将取决于所使用的数据库提供程序。 数据库提供程序可以为某些属性类型自动设置值生成，而其他提供程序则要求您手动设置如何生成值。
>
> 例如，使用 SQL Server 时，将使用 `rowversion` 数据类型设置在添加或更新时生成的 `byte[]` 属性，并将其标记为并发标记，以便在数据库中生成值。 但是，如果您指定在添加或更新时生成 `DateTime` 属性，则必须设置一个方法来生成值。 实现此目的的一种方法是将默认值 `GETDATE()` （请参阅[默认](relational/default-values.md)值）配置为为新行生成值。 然后，你可以在更新期间使用数据库触发器来生成值（如下面的示例触发器）。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a>约定

按照约定，将设置 short、int、long 或 Guid 类型的非复合主键，使其在 add 时生成值。 所有其他属性都将设置，并且不生成值。

## <a name="data-annotations"></a>数据注释

### <a name="no-value-generation-data-annotations"></a>无值生成（数据批注）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a>添加时生成的值（数据批注）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> 这只是让 EF 知道为添加的实体生成的值并不保证 EF 将设置实际机制来生成值。 有关更多详细信息，请参阅[add 部分生成的值](#value-generated-on-add)。

### <a name="value-generated-on-add-or-update-data-annotations"></a>添加或更新时生成的值（数据批注）

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 这只是让 EF 知道为添加或更新的实体生成的值，它不保证 EF 将设置实际机制来生成值。 有关更多详细信息，请参阅[add or update 部分上生成的值](#value-generated-on-add-or-update)。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来更改给定属性的值生成模式。

### <a name="no-value-generation-fluent-api"></a>无值生成（流畅 API）

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a>添加时生成的值（流畅 API）

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> `ValueGeneratedOnAdd()` 只允许 EF 知道为添加的实体生成的值，它不保证 EF 将设置实际机制来生成值。  有关更多详细信息，请参阅[add 部分生成的值](#value-generated-on-add)。

### <a name="value-generated-on-add-or-update-fluent-api"></a>添加或更新时生成的值（熟知的 API）

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> 这只是让 EF 知道为添加或更新的实体生成的值，它不保证 EF 将设置实际机制来生成值。 有关更多详细信息，请参阅[add or update 部分上生成的值](#value-generated-on-add-or-update)。
