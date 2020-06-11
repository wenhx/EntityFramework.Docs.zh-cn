---
title: 实体属性-EF Core
description: 如何使用 Entity Framework Core 配置和映射实体属性
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: e4a1867a90df1fb277e7dd44b93d6c2d47895030
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664151"
---
# <a name="entity-properties"></a>实体属性

模型中的每个实体类型都具有一组属性，这些属性 EF Core 将从数据库中读取和写入数据。 如果使用关系数据库，实体属性将映射到表列。

## <a name="included-and-excluded-properties"></a>包含和排除的属性

按照约定，具有 getter 和 setter 的所有公共属性都将包括在模型中。

可以按如下所述排除特定属性：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>列名

按照约定，使用关系数据库时，实体属性映射到与属性同名的表列。

如果希望使用不同的名称配置列，可以执行以下操作：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>列数据类型

使用关系数据库时，数据库提供程序根据属性的 .NET 类型选择数据类型。 它还会考虑其他元数据，如配置的[最大长度](#maximum-length)、属性是否是主键的一部分等。

例如，SQL Server 将属性映射到列，将属性映射到 `DateTime` `datetime2(7)` `string` `nvarchar(max)` 列（对于用作键的属性，则为 `nvarchar(450)` ）。

您还可以配置列，以便为列指定精确的数据类型。 例如，以下代码将配置 `Url` 为一个非 unicode 字符串，其最大长度为 `200` ， `Rating` 小数位数为，小数 `5` 位数为，小数位数为 `2` ：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>最大长度

配置最大长度会向数据库提供程序提供有关要为给定属性选择的相应列数据类型的提示。 最大长度仅适用于数组数据类型，例如 `string` 和 `byte[]` 。

> [!NOTE]
> 在将数据传递给提供程序之前，实体框架不会验证最大长度。 提供程序或数据存储将根据需要进行验证。 例如，如果以 SQL Server 为目标，则超出最大长度将导致异常，因为基础列的数据类型将不允许存储过多的数据。

在下面的示例中，配置最大长度为500将导致 `nvarchar(500)` 在 SQL Server 上创建类型为的列：

#### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a>必需属性和可选属性

如果属性对于包含有效，则将其视为可选 `null` 。 如果 `null` 不是要分配给属性的有效值，则将其视为必需属性。 映射到关系数据库架构时，必需的属性将创建为不可为 null 的列，而可选属性则创建为可以为 null 的列。

### <a name="conventions"></a>约定

按照约定，.NET 类型可以包含 null 的属性将配置为可选，而 .NET 类型不能包含 null 的属性将根据需要进行配置。 例如，具有 .net 值类型（ `int` 、 `decimal` 、等）的所有属性 `bool` 都是必需的，并且具有可为 null 的 .net 值类型（、、等）的所有属性 `int?` `decimal?` `bool?` 都配置为可选。

C # 8 引入了一个名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，该功能允许对引用类型进行批注，以指示它是否可用于包含空值。 此功能在默认情况下处于禁用状态，如果启用，它将按以下方式修改 EF Core 的行为：

* 如果禁用可为 null 的引用类型（默认值），则所有具有 .NET 引用类型的属性都将按约定配置为可选（例如 `string` ）。
* 如果启用了可以为 null 的引用类型，则将根据 .NET 类型的 c # 为空性配置属性： `string?` 将配置为可选，而 `string` 将配置为 "必需"。

下面的示例显示了一个具有必需和可选属性的实体类型，禁用了可为 null 的引用功能（默认值），并启用了该功能：

#### <a name="without-nullable-reference-types-default"></a>[没有可为 null 的引用类型（默认值）](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[具有可以为 null 的引用类型](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

建议使用可以为 null 的引用类型，因为它会将 c # 代码中表示的可为 null 性传递到 EF Core 的模型和数据库，并免去使用熟知 API 或数据批注来两次表示相同的概念。

> [!NOTE]
> 在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。 管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。

有关可以为 null 的引用类型以及如何将其与 EF Core 一起使用的详细信息，[请参阅此功能的专用文档页](xref:core/miscellaneous/nullable-reference-types)。

### <a name="explicit-configuration"></a>显式配置

可以按如下所示将 "约定" 可以为 "可选" 的属性配置为 "必需"：

#### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>列排序规则

> [!NOTE]
> EF Core 5.0 中已引入了此功能。

可以在文本列上定义排序规则，以确定如何对它们进行比较和排序。 例如，以下示例将 SQL Server 列配置为不区分大小写：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

如果数据库中的所有列都需要使用特定的排序规则，请改为在数据库级别定义排序规则。

有关排序规则 EF Core 支持的常规信息，请参阅[排序规则文档页](xref:core/miscellaneous/collations-and-case-sensitivity.md)。