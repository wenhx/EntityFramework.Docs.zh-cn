---
title: 支持字段-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 5c1b2e8036a8556d69cac2ec22722fc72d6da4aa
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103147"
---
# <a name="backing-fields"></a>支持字段

支持字段允许 EF 读取和/或写入字段，而不是属性。 当使用类中的封装来限制和/或通过应用程序代码对数据访问进行限制时，这可能很有用，但在不使用这些限制/增强功能的情况下，应从数据库中读取和/或写入值。

## <a name="basic-configuration"></a>基本配置

按照约定，将发现以下字段作为给定属性的支持字段（按优先级顺序列出）。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

在下面的示例中， `Url`将属性配置为具有`_url`其支持字段：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

请注意，仅为模型中包含的属性发现支持字段。 有关模型中包含哪些属性的详细信息，请参阅[包括 & 排除属性](included-properties.md)。

你还可以使用数据批注（在 EFCore 5.0 中提供）或流畅的 API （例如，如果该字段名称与上述约定不对应）来配置支持字段：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>字段和属性访问

默认情况下，EF 将始终读取并写入到支持字段-假设已正确配置了一个字段，并且永远不会使用属性。 但是，EF 还支持其他访问模式。 例如，下面的示例指示 EF 仅在具体化时写入支持字段，并在所有其他情况下使用属性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

有关完整的支持选项集，请参阅[PropertyAccessMode 枚举](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。

> [!NOTE]
> 使用 EF Core 3.0，默认属性访问模式从`PreferFieldDuringConstruction`更改为。 `PreferField`

## <a name="field-only-properties"></a>仅限字段的属性

您还可以在您的模型中创建一个概念属性，该属性在实体类中不具有相应的 CLR 属性，而是使用字段来存储实体中的数据。 这不同于[阴影属性](shadow-properties.md)，其中的数据存储在更改跟踪器中，而不是存储在实体的 CLR 类型中。 仅字段属性在实体类使用方法而不是属性来获取/设置值时使用，或者在字段不应在域模型中公开（例如主键）的情况下使用。

可以通过在`Property(...)` API 中提供名称来配置仅限字段的属性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF 将尝试查找具有给定名称的 CLR 属性，如果找不到属性，则尝试查找一个字段。 如果属性和字段均未找到，则将改为设置影子属性。

您可能需要从 LINQ 查询中引用仅限字段的属性，但此类字段通常是私有的。 可以在 LINQ 查询`EF.Property(...)`中使用方法来引用字段：

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
