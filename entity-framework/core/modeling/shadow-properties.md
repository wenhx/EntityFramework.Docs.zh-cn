---
title: 阴影属性-EF Core
description: 在 Entity Framework Core 模型中配置阴影属性
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 735659a1a8523e63afa908d4fe3904e62f46cbd0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071376"
---
# <a name="shadow-properties"></a>阴影属性

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

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

可以通过静态方法在 LINQ 查询中引用影子属性 `EF.Property` ：

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

在无跟踪查询后，不能访问阴影属性，因为更改跟踪器不跟踪返回的实体。
