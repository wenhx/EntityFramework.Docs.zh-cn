---
title: 影子和索引器属性-EF Core
description: 在 Entity Framework Core 模型中配置影子和索引器属性
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: f03dc000bb111253ae74c05a668703f2e6237a57
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430412"
---
# <a name="shadow-and-indexer-properties"></a>影子和索引器属性

影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。 这些属性的值和状态纯粹在更改跟踪器中进行维护。 当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。

## <a name="foreign-key-shadow-properties"></a>外键阴影属性

影子属性最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体类型之间的导航属性在实体类型上管理关系。 按照约定，当发现关系但在依赖实体类中找不到外键属性时，EF 会引入一个影子属性。

该属性将被命名 `<navigation property name><principal key property name>` (在依赖实体上导航（指向主体实体）用于命名) 。 如果主体键属性名称包含导航属性的名称，则该名称将只是 `<principal key property name>` 。 如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。

例如，下面的代码列表将导致将 `BlogId` 影子属性引入到 `Post` 实体：

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>配置阴影属性

你可以使用 "熟知 API" 配置阴影属性。 调用的字符串重载后 `Property` ，可以链接到其他属性的任何配置调用。 在下面的示例中，由于 `Blog` 没有名为的 CLR 属性，因此将 `LastUpdated` 创建一个影子属性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

如果提供给方法的名称 `Property` 与现有属性的名称相匹配 (影子属性或在实体类) 上定义的属性，则代码将配置该现有属性，而不是引入新的阴影属性。

## <a name="accessing-shadow-properties"></a>访问阴影属性

可以通过 API 获取和更改影子属性值 `ChangeTracker` ：

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

可以通过静态方法在 LINQ 查询中引用影子属性 `EF.Property` ：

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

在无跟踪查询后，不能访问阴影属性，因为更改跟踪器不跟踪返回的实体。

## <a name="property-bag-entity-types"></a>属性包实体类型

> [!NOTE]
> EF Core 5.0 中添加了对属性包实体类型的支持。

仅包含索引器属性的实体类型称为 "属性包实体类型"。 这些实体类型没有阴影属性。 目前仅 `Dictionary<string, object>` 支持作为属性包实体类型。 这意味着，必须将其配置为具有唯一名称的共享实体类型，并且 `DbSet` 必须使用调用来实现相应的属性 `Set` 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
