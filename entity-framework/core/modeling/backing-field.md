---
title: 支持字段-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 288440a4494117fe59d27187e24424c4d2fd44ab
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811875"
---
# <a name="backing-fields"></a>支持字段

> [!NOTE]  
> 此功能是 EF Core 1.1 中新增的功能。

支持字段允许 EF 读取和/或写入字段，而不是属性。 当使用类中的封装来限制和/或通过应用程序代码对数据访问进行限制时，这可能很有用，但在不使用这些限制的情况下，应从数据库中读取和/或写入值。举措.

## <a name="conventions"></a>约定

按照约定，将发现以下字段作为给定属性的支持字段（按优先级顺序列出）。 仅为模型中包含的属性发现字段。 有关模型中包含哪些属性的详细信息，请参阅[包括 & 排除属性](included-properties.md)。

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

配置了支持字段后，当从数据库具体化实体实例（而不是使用属性资源库）时，EF 将直接写入该字段。 如果 EF 需要在其他时间读取或写入值，则它将使用属性（如果可能）。 例如，如果 EF 需要更新某个属性的值，则它将使用属性 setter （如果已定义）。 如果该属性为只读，则它将写入字段。

## <a name="data-annotations"></a>数据注释

不能通过数据批注配置支持字段。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来配置属性的支持字段。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a>控制何时使用字段

可以配置 EF 何时使用字段或属性。 有关支持的选项，请参阅[PropertyAccessMode 枚举](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a>没有属性的字段

您还可以在您的模型中创建一个概念属性，该属性在实体类中不具有相应的 CLR 属性，而是使用字段来存储实体中的数据。 这不同于[阴影属性](shadow-properties.md)，其中的数据存储在更改跟踪器中。 如果实体类使用方法获取/设置值，通常会使用此方法。

可以为 EF 指定 `Property(...)` API 中的字段的名称。 如果没有具有给定名称的属性，则 EF 将查找字段。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

如果实体类中没有属性，则可以在 LINQ 查询中使用 `EF.Property(...)` 方法来引用概念上是模型的一部分的属性。

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
