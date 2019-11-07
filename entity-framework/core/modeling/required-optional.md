---
title: 必需属性和可选属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: 62b2b3f5a761c0aacece986ecd0b2dd2f958d048
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655663"
---
# <a name="required-and-optional-properties"></a>必需和可选属性

如果属性对于包含 `null`是有效的，则认为该属性是可选的。 如果 `null` 不是要分配给属性的有效值，则将其视为必需属性。

映射到关系数据库架构时，必需的属性将创建为不可为 null 的列，而可选属性则创建为可以为 null 的列。

## <a name="conventions"></a>约定

按照约定，.NET 类型可以包含 null 的属性将配置为可选，而 .NET 类型不能包含 null 的属性将根据需要进行配置。 例如，具有 .NET 值类型（`int`、`decimal`、`bool`等）的所有属性都是必需的，并且具有可为 null 的 .NET 值类型（`int?`、`decimal?`、`bool?`等）的所有属性都配置为可选。

C#8引入了一个名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，该功能允许对引用类型进行批注，以指示它是否可用于包含空值。 此功能在默认情况下处于禁用状态，如果启用，它将按以下方式修改 EF Core 的行为：

* 如果禁用了可为 null 的引用类型（默认值），则所有具有 .NET 引用类型的属性都将按约定（例如 `string`）配置为可选。
* 如果启用了可以为 null 的引用类型，则将根据 .NET C#类型的为 null 性配置属性： `string?` 将配置为可选，而 `string` 将配置为 "必需"。

下面的示例显示了一个具有必需和可选属性的实体类型，禁用了可为 null 的引用功能（默认值），并启用了该功能：

# <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[没有可为 null 的引用类型（默认值）](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

# <a name="with-nullable-reference-typestabwith-nrt"></a>[具有可以为 null 的引用类型](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

建议使用可以为 null 的引用类型，因为它会将C#代码中表示的可为 null 性传递到 EF Core 的模型和数据库，并免去使用熟知 API 或数据批注来两次表示相同的概念。

> [!NOTE]
> 在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。 管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。

有关可以为 null 的引用类型以及如何将其与 EF Core 一起使用的详细信息，[请参阅此功能的专用文档页](xref:core/miscellaneous/nullable-reference-types)。

## <a name="configuration"></a>配置

可以按如下所示将 "约定" 可以为 "可选" 的属性配置为 "必需"：

# <a name="data-annotationstabdata-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]

# <a name="fluent-apitabfluent-api"></a>[熟知 API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

***
