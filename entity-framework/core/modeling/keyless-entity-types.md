---
title: 无键实体类型-EF Core
description: 如何使用 Entity Framework Core 配置无键实体类型
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 00e8f17c88fd51e39df3c1e45c648c203bbbe324
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103121"
---
# <a name="keyless-entity-types"></a>无键实体类型

> [!NOTE]
> 此功能已添加到 EF Core 2.1 的查询类型的名称下。 在 EF Core 3.0 中，概念已重命名为无键实体类型。 `[Keyless]`数据批注在 EFCore 5.0 中变为可用。

除了常规实体类型外，EF Core 模型还可以包含_无键实体类型_，可用于对不包含键值的数据执行数据库查询。

## <a name="defining-keyless-entity-types"></a>定义无键实体类型

可以使用数据批注或熟知的 API 定义无键实体类型：

### <a name="data-annotations"></a>[数据批注](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>无键实体类型特征

无键实体类型支持与常规实体类型相同的多个映射功能，如继承映射和导航属性。 在关系存储上，它们可以通过 Fluent API 方法或数据批注来配置目标数据库对象和列。

但是，它们不同于常规实体类型，因为它们：

- 不能定义键。
- 永远不会对_DbContext_中的更改进行跟踪，因此不会在数据库中插入、更新或删除这些更改。
- 绝不会按约定发现。
- 仅支持导航映射功能的子集，具体如下：
  - 它们永远不能充当关系的主体端。
  - 它们可能没有到拥有的实体的导航
  - 它们只能包含指向常规实体的引用导航属性。
  - 实体不能包含无键实体类型的导航属性。
- 需要配置`[Keyless]`数据批注或`.HasNoKey()`方法调用。
- 可以映射到定义的_查询_。 定义查询是在模型中声明的查询，它充当无键实体类型的数据源。

## <a name="usage-scenarios"></a>使用方案

无键实体类型的一些主要使用方案包括：

- 作为[原始 SQL 查询](xref:core/querying/raw-sql)的返回类型。
- 映射到不包含主键的数据库视图。
- 映射到未定义主键的表。
- 映射到模型中定义的查询。

## <a name="mapping-to-database-objects"></a>映射到数据库对象

使用`ToTable`或`ToView` Fluent API 将无键实体类型映射到数据库对象。 从 EF Core 的角度来看，此方法中指定的数据库对象是一个_视图_，这意味着它将被视为只读查询源，并且不能作为更新、插入或删除操作的目标。 但是，这并不意味着数据库对象实际上必须是数据库视图。 它也可以是将被视为只读的数据库表。 相反，对于常规实体类型，EF Core 假设在`ToTable`方法中指定的数据库对象可以被视为_表_，这意味着它可用作查询源，但也可作为更新、删除和插入操作的目标。 事实上，您可以在中`ToTable`指定数据库视图的名称，只要该视图被配置为可在数据库上更新，一切都应正常工作。

> [!NOTE]
> `ToView`假定该对象已存在于数据库中，并且不是通过迁移创建的。

## <a name="example"></a>示例

下面的示例演示如何使用无键实体类型来查询数据库视图。

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)。

首先，我们定义一个简单的博客和发布模型：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

接下来，我们定义一个简单的数据库视图，该视图将允许我们查询与每个博客关联的帖子数：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

接下来，我们定义一个类来保存数据库视图的结果：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

接下来，使用`HasNoKey` API 在_OnModelCreating_中配置无键实体类型。
我们使用熟知的配置 API 来配置无键实体类型的映射：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

接下来，将配置`DbContext`为包括`DbSet<T>`：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最后，我们可以采用标准方式查询数据库视图：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> 请注意，我们还定义了一个上下文级查询属性（DbSet）作为此类型的查询的根。
