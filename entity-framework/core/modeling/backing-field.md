---
title: 支持字段 - EF 核心
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434170"
---
# <a name="backing-fields"></a>支持字段

备份字段允许 EF 读取和/或写入字段而不是属性。 当类中的封装用于限制和/或增强应用程序代码访问数据的语义时，这非常有用，但该值应从数据库读取和/或写入数据库，而无需使用这些限制/增强功能。

## <a name="basic-configuration"></a>基本配置

按照惯例，以下字段将发现为给定属性的后备字段（按优先级顺序列出）。 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

在下面的示例中，`Url`属性配置为`_url`其备份字段：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

请注意，仅对模型中包含的属性发现支持字段。 有关模型中包含哪些属性的详细信息，请参阅[包括&排除属性](included-properties.md)。

您还可以使用数据注释或 Fluent API 配置备份字段，例如，如果字段名称与上述约定不对应：

### <a name="data-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>字段和属性访问

默认情况下，EF 将始终读取和写入备份字段（假设一个字段已正确配置）并且永远不会使用该属性。 但是，EF 还支持其他访问模式。 例如，以下示例指示 EF 仅在具体化时写入备份字段，并在所有其他情况下使用 该属性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

有关支持的完整选项集，请参阅[属性访问模式枚举](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。

> [!NOTE]
> 使用 EF Core 3.0 时，默认属性`PreferFieldDuringConstruction`访问`PreferField`模式从 更改为 。

## <a name="field-only-properties"></a>仅字段属性

您还可以在模型中创建一个概念属性，该属性在实体类中没有相应的 CLR 属性，而是使用字段将数据存储在实体中。 这与[阴影属性](shadow-properties.md)不同，其中数据存储在更改跟踪器中，而不是存储在实体的 CLR 类型中。 当实体类使用方法而不是属性来获取/设置值时，或者在域模型中根本不不应公开字段（例如主键）的情况下，通常使用仅字段属性。

通过在`Property(...)`API 中提供名称来配置仅字段属性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

如果找不到属性，EF 将尝试查找具有给定名称的 CLR 属性，或者查找字段。 如果未找到属性，也找不到字段，则将改为设置阴影属性。

您可能需要从 LINQ 查询中引用仅字段属性，但此类字段通常是私有的。 可以使用 LINQ`EF.Property(...)`查询中的方法引用该字段：

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
