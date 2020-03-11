---
title: 生成的值的 EF Core
description: 如何在使用时配置属性的值生成 Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: 9c616e157ff1bdb9700f436a7ae2788330fe5d45
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413899"
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

根据所用数据库提供程序的不同，值可能会通过 EF 在客户端生成或者由数据库生成。 如果由数据库生成值，则当你将实体添加到上下文时，EF 可能会赋予一个临时值。 在 `SaveChanges()`期间，此临时值将替换为数据库生成的值。

如果将一个实体添加到已经为属性赋予值的上下文，则 EF 会尝试插入该值而不是生成新值。 如果某个属性未分配 CLR 默认值（`null` 用于 `string`，`0` 用于 `int`，`Guid.Empty` `Guid`等），则该属性被视为已分配值。 有关详细信息，请参阅[生成的属性的显式值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 如何为添加的实体生成值取决于所用数据库提供程序。 数据库提供程序可能会为某些属性类型自动设置值的生成，但其他的属性类型可能要求你手动设置值的生成方式。
>
> 例如，使用 SQL Server 时，将自动为 `GUID` 属性（使用 SQL Server 顺序 GUID 算法）生成值。 但是，如果您指定在添加时生成 `DateTime` 属性，则必须设置一个方法来生成值。 执行此操作的一种方法是配置默认值 `GETDATE()`，请参阅[默认值](relational/default-values.md)。

### <a name="value-generated-on-add-or-update"></a>在添加或更新时生成值

在添加或更新时生成值，意味着在每次保存该记录（插入或更新）时生成新值。

与 `value generated on add`一样，如果为新添加的实体实例的属性指定值，则将插入该值，而不是要生成的值。 还可以在更新时设置显式值。 有关详细信息，请参阅[生成的属性的显式值](../saving/explicit-values-generated-properties.md)。

> [!WARNING]
> 如何在添加和更新实体时生成值取决于所用数据库提供程序。 数据库提供程序可能会为某些属性类型自动设置值的生成，但其他的属性类型会要求你手动设置值的生成方式。
>
> 例如，使用 SQL Server 时，将使用 `rowversion` 数据类型设置在添加或更新时生成的 `byte[]` 属性，并将其标记为并发标记，以便在数据库中生成值。 但是，如果您指定在添加或更新时生成 `DateTime` 属性，则必须设置一个方法来生成值。 实现此目的的一种方法是将默认值 `GETDATE()` （请参阅[默认](relational/default-values.md)值）配置为为新行生成值。 然后即可使用数据库触发器在更新过程中生成值（如下面的示例触发器所示）。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>在添加时生成值

按照约定，如果应用程序不提供值，则将 "short"、"int"、"long" 或 "Guid" 类型的非复合主键设置为为插入的实体生成值。 您的数据库提供程序通常会负责必要的配置;例如，SQL Server 中的数字主键将自动设置为标识列。

可以将任何属性配置为为插入的实体生成其值，如下所示：

### <a name="data-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[熟知 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> 这只是让 EF 知道为已添加的实体生成值，并不保证 EF 会设置实际机制来生成值。 有关更多详细信息，请参阅[add 部分生成的值](#value-generated-on-add)。

### <a name="default-values"></a>默认值

在关系数据库中，可以使用默认值来配置列;如果插入的行没有该列的值，将使用默认值。

可以在属性上配置默认值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

还可以指定用于计算默认值的 SQL 片段：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

指定默认值会隐式将属性配置为在添加时生成的值。

## <a name="value-generated-on-add-or-update"></a>在添加或更新时生成值

### <a name="data-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[熟知 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> 这只是让 EF 知道为添加或更新的实体生成值，并不保证 EF 会设置实际机制来生成值。 有关更多详细信息，请参阅[add or update 部分上生成的值](#value-generated-on-add-or-update)。

### <a name="computed-columns"></a>计算列

在某些关系数据库上，列可以配置为在数据库中计算其值，通常具有引用其他列的表达式：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=ComputedColumn&highlight=5)]

> [!NOTE]
> 在某些情况下，每次提取列的值（有时称为*虚拟*列）时，都将计算该列的值，而在其他情况下，会在每次更新行时计算该列的值并存储（有时称为*存储*列或*持久化*列）。 这不同于数据库提供程序。

## <a name="no-value-generation"></a>无值生成

如果对属性的值生成进行了配置，则通常需要对其进行值生成。 例如，如果你有一个 int 类型的主键，则它将被隐式设置配置为在 add 时生成的值;可以通过以下方式禁用此操作：

### <a name="data-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[熟知 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
