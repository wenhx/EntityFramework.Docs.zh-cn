---
title: 无钥匙实体类型 - EF 核心
description: 如何使用实体框架核心配置无钥匙实体类型
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434209"
---
# <a name="keyless-entity-types"></a>无键实体类型

> [!NOTE]
> 此功能在 EF Core 2.1 中以查询类型的名义添加。 在 EF Core 3.0 中，概念重命名为无钥匙实体类型。

除了常规实体类型外，EF Core 模型还可以包含_无键实体类型_，可用于对不包含键值的数据执行数据库查询。

## <a name="defining-keyless-entity-types"></a>定义无键实体类型

可以使用数据注释或 Fluent API 定义无键实体类型：

### <a name="data-annotations"></a>[数据注释](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>无键实体类型特征

无键实体类型支持许多与常规实体类型相同的映射功能，如继承映射和导航属性。 在关系存储上，他们可以通过流畅的 API 方法或数据注释配置目标数据库对象和列。

但是，它们不同于常规实体类型，因为它们：

- 无法定义密钥。
- 永远不会跟踪_DbContext_中的更改，因此永远不会在数据库中插入、更新或删除。
- 从来都没有被惯例发现。
- 仅支持导航映射功能的子集，特别是：
  - 他们可能永远不会作为关系的主要目的。
  - 它们可能没有指向拥有的实体的导航
  - 它们只能包含指向常规实体的引用导航属性。
  - 实体不能包含无键实体类型的导航属性。
- 需要使用`[Keyless]`数据注释或方法调用进行`.HasNoKey()`配置。
- 可以映射到_定义查询_。 定义查询是在模型中声明的查询，它充当无键实体类型的数据源。

## <a name="usage-scenarios"></a>使用方案

无键实体类型的一些主要使用方案是：

- 用作[原始 SQL 查询的](xref:core/querying/raw-sql)返回类型。
- 映射到不包含主键的数据库视图。
- 映射到未定义主键的表。
- 映射到模型中定义的查询。

## <a name="mapping-to-database-objects"></a>映射到数据库对象

使用`ToTable`或`ToView`fluent API 将无键实体类型映射到数据库对象。 从 EF Core 的角度来看，此方法中指定的数据库对象是一个_视图_，这意味着它被视为只读查询源，不能成为更新、插入或删除操作的目标。 但是，这并不意味着数据库对象实际上需要是数据库视图。 它也可以是数据库表，将被视为只读。 相反，对于常规实体类型，EF Core 假定`ToTable`方法中指定的数据库对象可以视为_表_，这意味着它可以用作查询源，但也可以通过更新、删除和插入操作作为目标。 事实上，您可以指定 中的`ToTable`数据库视图的名称，只要视图配置为在数据库上可升算，所有内容都应该正常工作。

> [!NOTE]
> `ToView`假定该对象已存在于数据库中，并且不会由迁移创建。

## <a name="example"></a>示例

下面的示例演示如何使用无键实体类型来查询数据库视图。

> [!TIP]
> 您可以在 GitHub 上查看本文[的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)。

首先，我们定义了一个简单的博客和帖子模型：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

接下来，我们定义一个简单的数据库视图，该视图将使我们能够查询与每个博客关联的帖子数：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

接下来，我们定义一个类来保存数据库视图的结果：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

接下来，我们使用`HasNoKey`API 在_OnModel 创建_中配置无钥匙实体类型。
我们使用流畅的配置 API 配置无钥匙实体类型的映射：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

接下来，我们将 配置为`DbContext`包括`DbSet<T>`：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最后，我们可以以标准方式查询数据库视图：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> 请注意，我们还定义了上下文级别查询属性 （DbSet）， 以充当针对此类型的查询的根。
