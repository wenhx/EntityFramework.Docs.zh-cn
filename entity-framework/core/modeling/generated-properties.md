---
title: 生成的值-EF Core
description: 如何在使用时配置属性的值生成 Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: 347cedbf5fdebc985d75c6cad3c28f17d1344993
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429619"
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

根据所使用的数据库提供程序，值可能是由 EF 或数据库中的客户端生成的。 如果值是由数据库生成的，则在将实体添加到上下文时，EF 可能会分配临时值。 在期间，此临时值将替换为数据库生成的值 `SaveChanges()` 。

如果向具有分配给属性的值的上下文添加实体，则 EF 将尝试插入该值，而不是生成新的值。 如果某个属性未分配 (的 CLR 默认值、for、 `null` `string` `0` `int` `Guid.Empty` `Guid` 等等 ) ，则该属性被视为已分配值。 有关详细信息，请参阅 [生成的属性的显式值](xref:core/saving/explicit-values-generated-properties)。

> [!WARNING]
> 如何为添加的实体生成值将取决于所使用的数据库提供程序。 数据库提供程序可以为某些属性类型自动设置值生成，但其他提供程序可能要求您手动设置如何生成值。
>
> 例如，使用 SQL Server 时，将 `GUID` 使用 SQL Server 顺序 GUID 算法) 为 (的属性自动生成值。 但是，如果您指定 `DateTime` 在添加时生成属性，则必须为生成值设置一种方法。 实现此目的的一种方法是配置的默认值 `GETDATE()` ，请参阅 [默认值](#default-values)。

### <a name="value-generated-on-add-or-update"></a>添加或更新时生成的值

添加或更新时生成的值意味着每次保存记录时都会生成一个新值 (插入或更新) 。

例如 `value generated on add` ，如果为实体的新添加实例的属性指定值，则将插入该值，而不是要生成的值。 还可以在更新时设置显式值。 有关详细信息，请参阅 [生成的属性的显式值](xref:core/saving/explicit-values-generated-properties)。

> [!WARNING]
> 为添加的和更新的实体生成值的方式将取决于所使用的数据库提供程序。 数据库提供程序可以为某些属性类型自动设置值生成，而其他提供程序则要求您手动设置如何生成值。
>
> 例如，使用 SQL Server 时，将设置 `byte[]` 为在添加或更新时生成并标记为并发标记的属性， `rowversion` 以便在数据库中生成值。 但是，如果您指定 `DateTime` 在添加或更新时生成属性，则必须为生成值设置一种方法。 执行此操作的一种方法是将默认值配置 `GETDATE()` (查看 [默认值](#default-values)) 以便为新行生成值。 然后，您可以使用数据库触发器来生成更新期间的值 (例如以下示例触发器) 。
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>添加时生成的值

按照约定，如果应用程序不提供值，则将 "short"、"int"、"long" 或 "Guid" 类型的非复合主键设置为为插入的实体生成值。 您的数据库提供程序通常会负责必要的配置;例如，SQL Server 中的数字主键将自动设置为标识列。

可以将任何属性配置为为插入的实体生成其值，如下所示：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> 这只是让 EF 知道为添加的实体生成的值并不保证 EF 将设置用于生成值的实际机制。 有关更多详细信息，请参阅 [add 部分生成的值](#value-generated-on-add) 。

### <a name="default-values"></a>默认值

在关系数据库中，可以使用默认值来配置列;如果插入的行没有该列的值，将使用默认值。

可以在属性上配置默认值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

还可以指定用于计算默认值的 SQL 片段：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

指定默认值会隐式将属性配置为在添加时生成的值。

## <a name="value-generated-on-add-or-update"></a>添加或更新时生成的值

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

**_

> [!WARNING]
> 这只是让 EF 知道，将为添加或更新的实体生成值，而不保证 EF 设置用于生成值的实际机制。 有关更多详细信息，请参阅 [add or update 部分上生成的值](#value-generated-on-add-or-update) 。

### <a name="computed-columns"></a>计算列

在大多数关系数据库上，列可以配置为在数据库中计算其值，通常具有引用其他列的表达式：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn)]

上面创建了一个 _virtual * 计算列，其值在每次从数据库中提取时都会进行计算。 您还可以指定将计算列 *存储* (有时称为 *持久化* ) ，这意味着在每次更新行时计算，并将磁盘与常规列一起存储：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn)]

> [!NOTE]
> EF Core 5.0 中增加了对创建存储计算列的支持。

## <a name="no-value-generation"></a>无值生成

如果对属性的值生成进行了配置，则通常需要对其进行值生成。 例如，如果你有一个 int 类型的主键，则它将被隐式设置配置为在 add 时生成的值;可以通过以下方式禁用此操作：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
