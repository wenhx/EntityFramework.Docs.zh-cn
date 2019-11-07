---
title: 关系-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 1e59ce9e19c12aa5564bc8467dcfcb3be8ee8996
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655673"
---
# <a name="relationships"></a>关系

关系定义两个实体之间的关系。 在关系数据库中，这由外键约束表示。

> [!NOTE]  
> 本文中的大多数示例都使用一对多关系来演示概念。 有关一对一关系和多对多关系的示例，请参阅文章末尾的[其他关系模式](#other-relationship-patterns)部分。

## <a name="definition-of-terms"></a>术语定义

有许多术语用于描述关系

* **相关实体：** 这是包含外键属性的实体。 有时称为关系的 "子级"。

* **主体实体：** 这是包含主/备用键属性的实体。 有时称为关系的 "父项"。

* **外键：** 依赖实体中的属性，用于存储与实体相关的主体键属性的值。

* **主体密钥：** 唯一标识主体实体的属性。 这可能是主键或备用密钥。

* **导航属性：** 在主体和/或从属实体上定义的属性，该属性包含对相关实体的引用。

  * **集合导航属性：** 一个导航属性，其中包含对多个相关实体的引用。

  * **引用导航属性：** 保存对单个相关实体的引用的导航属性。

  * **反向导航属性：** 讨论特定导航属性时，此术语是指关系另一端的导航属性。

下面的代码列表显示 `Blog` 和 `Post` 之间的一对多关系

* `Post` 是依赖实体

* `Blog` 是主体实体

* `Post.BlogId` 为外键

* `Blog.BlogId` 是主体键（在本例中为主键而不是备用密钥）

* `Post.Blog` 是一个引用导航属性

* `Blog.Posts` 是集合导航属性

* `Post.Blog` 是 `Blog.Posts` 的反向导航属性（反之亦然）

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>约定

按照约定，当在某个类型上发现了导航属性时，将创建一个关系。 如果当前数据库提供程序无法将其指向的类型映射为标量类型，则该属性被视为导航属性。

> [!NOTE]  
> 按约定发现的关系将始终以主体实体的主键为目标。 若要以备用密钥为目标，则必须使用熟知的 API 执行其他配置。

### <a name="fully-defined-relationships"></a>完全定义的关系

关系最常见的模式是在关系两端定义导航属性，在依赖实体类中定义外键属性。

* 如果在两个类型之间找到一对导航属性，则这些属性将配置为同一关系的反向导航属性。

* 如果依赖实体包含名为 `<primary key property name>`、`<navigation property name><primary key property name>`或 `<principal entity name><primary key property name>` 的属性，则该属性将被配置为外键。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 如果有多个在两种类型之间定义的导航属性（即，多个指向彼此的不同类型的导航），则不会按约定创建任何关系，你将需要手动对其进行配置以确定导航属性配对。

### <a name="no-foreign-key-property"></a>无外键属性

尽管建议在依赖实体类中定义外键属性，但这并不是必需的。 如果找不到外键属性，则会引入 `<navigation property name><principal key property name>` 名称的阴影外键属性（有关详细信息，请参阅[影子属性](shadow-properties.md)）。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>单个导航属性

只包含一个导航属性（无反向导航，没有外键属性）就足以具有约定定义的关系。 还可以有一个导航属性和一个外键属性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>级联删除

按照约定，级联删除将对所需的关系和*ClientSetNull*设置为*cascade* ，以实现可选关系。 *Cascade*表示也会删除依赖实体。 *ClientSetNull*表示未加载到内存中的依赖实体将保持不变，必须手动删除，或将其更新为指向有效的主体实体。 对于加载到内存中的实体，EF Core 将尝试将外键属性设置为 null。

请参阅 required[和 optional](#required-and-optional-relationships)关系部分，了解必需和可选关系之间的差异。

有关不同的删除行为和约定使用的默认值的详细信息，请参阅[级联删除](../saving/cascade-delete.md)。

## <a name="data-annotations"></a>数据注释

可以使用两个数据批注来配置关系、`[ForeignKey]` 和 `[InverseProperty]`。 `System.ComponentModel.DataAnnotations.Schema` 命名空间中提供了这些功能。

### <a name="foreignkey"></a>ForeignKey

您可以使用数据批注来配置应用作给定关系的外键属性的属性。 通常，当不按约定发现外键属性时，会执行此操作。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> 可以将 `[ForeignKey]` 批注放置在关系中的任一导航属性上。 它不需要在依赖实体类中定位导航属性。

### <a name="inverseproperty"></a>[InverseProperty]

您可以使用数据批注来配置依赖项和主体实体上的导航属性如何配对。 这通常在两个实体类型之间存在多个导航属性对时执行。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a>Fluent API

若要在熟知的 API 中配置关系，请首先标识构成关系的导航属性。 `HasOne` 或 `HasMany` 标识正在开始配置的实体类型上的导航属性。 然后，将调用链接到 `WithOne` 或 `WithMany` 来标识反向导航。 `HasOne`/`WithOne` 用于引用导航属性，`HasMany`/用于集合导航属性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a>单个导航属性

如果只有一个导航属性，则 `WithOne` 和 `WithMany`有无参数重载。 这表示在概念上，关系的另一端有一个引用或集合，但实体类中不包含导航属性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a>外键

您可以使用 "熟知 API" 来配置应用作给定关系的外键属性的属性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

下面的代码列表演示如何配置复合外键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

您可以使用 `HasForeignKey(...)` 的字符串重载将影子属性配置为外键（有关详细信息，请参阅[影子属性](shadow-properties.md)）。 建议先将影子属性显式添加到模型，然后再将其用作外键（如下所示）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a>无导航属性

不一定需要提供导航属性。 您可以直接在关系的一端提供外键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a>主体密钥

如果你希望外键引用主键之外的属性，则可以使用熟知的 API 来配置关系的主体键属性。 配置为主体密钥的属性将自动设置为备用密钥（有关详细信息，请参阅[备用](alternate-keys.md)密钥）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

下面的代码列表演示如何配置复合主体键。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=Composite&highlight=11)]

> [!WARNING]  
> 指定主体键属性的顺序必须与为外键指定这些属性的顺序一致。

### <a name="required-and-optional-relationships"></a>必需和可选的关系

您可以使用熟知的 API 来配置关系是必需的还是可选的。 最终，这会控制外键属性是必需的还是可选的。 当使用阴影状态外键时，这非常有用。 如果实体类中具有外键属性，则关系的 requiredness 取决于外键属性是必需还是可选（有关详细信息，请参阅[必需和可选属性](required-optional.md)）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=11)]

### <a name="cascade-delete"></a>级联删除

您可以使用熟知的 API 显式配置给定关系的级联删除行为。

有关每个选项的详细讨论，请参阅保存数据部分的[级联删除](../saving/cascade-delete.md)。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=11)]

## <a name="other-relationship-patterns"></a>其他关系模式

### <a name="one-to-one"></a>一对一

一对多关系在两侧都有一个引用导航属性。 它们遵循与一对多关系相同的约定，但在外键属性上引入了唯一索引，以确保只有一个依赖项与每个主体相关。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=Property&highlight=6,15,16)]

> [!NOTE]  
> EF 会根据其检测外键属性的能力，选择其中一个实体作为依赖项。 如果选择了错误的实体作为依赖项，则可以使用熟知的 API 来更正此问题。

使用熟知 API 配置关系时，可使用 `HasOne` 和 `WithOne` 方法。

配置外键时，需要指定依赖实体类型-请注意以下列表中 `HasForeignKey` 提供的泛型参数。 在一对多关系中，可以清楚地表明具有引用导航的实体是依赖项，并且具有集合的实体是主体。 但这并不是一对一的关系，因此需要显式定义它。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a>多对多

目前尚不支持多对多关系，没有实体类来表示联接表。 但是，您可以通过包含联接表的实体类并映射两个不同的一对多关系，来表示多对多关系。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)]
