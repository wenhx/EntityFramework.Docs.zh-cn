---
title: 支持字段-EF Core
description: 为 Entity Framework Core 模型中的属性配置支持字段
author: ajcvickers
ms.date: 10/27/2016
uid: core/modeling/backing-field
ms.openlocfilehash: 0df54192da7365451e5213739df8117e66c5cd0f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429658"
---
# <a name="backing-fields"></a>支持字段

支持字段允许 EF 读取和/或写入字段，而不是属性。 当使用类中的封装来限制和/或通过应用程序代码对数据访问进行限制时，这可能很有用，但在不使用这些限制/增强功能的情况下，应从数据库中读取和/或写入值。

## <a name="basic-configuration"></a>基本配置

按照约定，将发现以下字段作为给定属性的支持字段 (按优先级顺序) 列出。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

在下面的示例中，将 `Url` 属性配置为具有 `_url` 其支持字段：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

请注意，仅为模型中包含的属性发现支持字段。 有关模型中包含哪些属性的详细信息，请参阅 [包括 & 排除属性](xref:core/modeling/entity-properties)。

你还可以通过使用 EFCore 5.0) 或流畅 API 中可用的数据批注 (来配置支持字段，例如，如果该字段名称与上述约定不对应：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

***

## <a name="field-and-property-access"></a>字段和属性访问

默认情况下，EF 将始终读取并写入到支持字段-假设已正确配置了一个字段，并且永远不会使用属性。 但是，EF 还支持其他访问模式。 例如，下面的示例指示 EF 仅在具体化时写入支持字段，并在所有其他情况下使用属性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

有关完整的支持选项集，请参阅 [PropertyAccessMode 枚举](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) 。

> [!NOTE]
> 使用 EF Core 3.0，默认属性访问模式从更改 `PreferFieldDuringConstruction` 为 `PreferField` 。

## <a name="field-only-properties"></a>仅限字段的属性

您还可以在您的模型中创建一个概念属性，该属性在实体类中不具有相应的 CLR 属性，而是使用字段来存储实体中的数据。 这不同于 [阴影属性](xref:core/modeling/shadow-properties)，其中的数据存储在更改跟踪器中，而不是存储在实体的 CLR 类型中。 仅字段属性在实体类使用方法而不是属性来获取/设置值时使用，或者在字段不应在域 (模型中公开的情况下使用，例如主键) 。

可以通过在 API 中提供名称来配置仅限字段的属性 `Property(...)` ：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF 将尝试查找具有给定名称的 CLR 属性，如果找不到属性，则尝试查找一个字段。 如果属性和字段均未找到，则将改为设置影子属性。

您可能需要从 LINQ 查询中引用仅限字段的属性，但此类字段通常是私有的。 可以 `EF.Property(...)` 在 LINQ 查询中使用方法来引用字段：

```csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
