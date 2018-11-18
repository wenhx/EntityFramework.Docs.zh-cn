---
title: 保存关联数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
uid: core/saving/related-data
ms.openlocfilehash: 7349c57c0dccd3c911178641d3b34a478a4f6194
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994739"
---
# <a name="saving-related-data"></a>保存关联数据

除了独立实体以外，还可以使用模型中定义的关系。

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/)。

## <a name="adding-a-graph-of-new-entities"></a>添加新实体的关系图

如果创建多个互相关联的新实体，则将其中一个添加到上下文时也会添加其他实体。

在下面的示例中，博客及其三个关联文章会全部插入到数据库中。 这些文章可以被发现并添加，是因为它们可以通过 `Blog.Posts` 导航属性访问。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> 可以使用 EntityEntry.State 属性来设置单个实体的状态。 例如 `context.Entry(blog).State = EntityState.Modified`。

## <a name="adding-a-related-entity"></a>添加关联实体

如果从已由上下文跟踪的实体的导航属性中引用新实体，则将发现该实体并将其插入到数据库中。

在下面的示例中， `blog` 实体来自于数据库（已被跟踪）， `post` 实体通过其 `Posts` 导航属性添加， 所以 `post` 实体会被插入到数据库中。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>更改关系

如果更改实体的导航属性，则将对数据库中的外键列进行相应的更改。

在下面的示例中，`post` 实体更新为属于新的 `blog` 实体，因为其 `Blog` 导航属性设置为指向 `blog`。 请注意，`blog` 也会插入到数据库中，因为它是已由上下文跟踪的实体 (`post`) 的导航属性引用的新实体。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>删除关系

可以通过将引用导航设置为 `null` 或从集合导航中删除关联的实体来删除关系。

根据关系中配置的级联删除行为，删除关系可能会对依赖实体产生副作用。

默认情况下，对于必选关系，将配置级联删除行为，会从数据库中删除子实体/依赖实体。 对于可选关系，默认情况下不会配置级联删除，但会将外键属性设置为 null。

要了解有关如何配置关系必要性的信息，请参阅[必选关系和可选关系](../modeling/relationships.md#required-and-optional-relationships)。

有关级联删除行为的工作原理、如何显式配置这些行为以及如何按照约定选择这些行为的详细信息，请参阅[级联删除](cascade-delete.md)。

在下面的示例中， `Blog` 和 `Post` 之间的关系上配置了级联删除，所以 `post` 实体会被从数据库中删除。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#RemovingRelationships)]
