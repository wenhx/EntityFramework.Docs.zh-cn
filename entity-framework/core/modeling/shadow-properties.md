---
title: 阴影属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: ab57358dd247e32c4ca0f57d07b4cb98f2b85d29
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655956"
---
# <a name="shadow-properties"></a>阴影属性

影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。 这些属性的值和状态纯粹在更改跟踪器中进行维护。

当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。 它们最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体类型之间的导航属性在实体类型上管理关系。

可以通过 `ChangeTracker` API 获取和更改影子属性值。

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

可以通过 `EF.Property` 静态方法在 LINQ 查询中引用影子属性。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a>约定

如果发现了关系，但在依赖实体类中找不到外键属性，则可以按约定创建阴影属性。 在这种情况下，将引入阴影外键属性。 影子外键属性将命名为 `<navigation property name><principal key property name>` （指向主体实体的依赖实体上的导航用于命名）。 如果主体键属性名称包含导航属性的名称，则该名称将只 `<principal key property name>`。 如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。

例如，下面的代码列表将导致向 `Post` 实体引入 `BlogId` 影子属性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions)]

## <a name="data-annotations"></a>数据注释

不能通过数据批注创建阴影属性。

## <a name="fluent-api"></a>Fluent API

你可以使用 "熟知 API" 配置阴影属性。 在您调用了的字符串重载之后 `Property` 您可以将对其他属性的任何配置调用链接在一起。

如果提供给 `Property` 方法的名称与现有属性（在实体类中定义）的名称相匹配，则代码将配置该现有属性，而不是引入新的阴影属性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]
