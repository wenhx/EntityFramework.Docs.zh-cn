---
title: 无键实体类型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: b968ac9602b9aa1f1c1e3181b6b76a64394d70f0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150787"
---
# <a name="keyless-entity-types"></a>无键实体类型
> [!NOTE]
> 此功能是 EF Core 2.1 中的新增功能。 在3.0 之前，它们被称为查询类型

除了常规实体类型外，EF Core 模型还可以包含_无键实体类型_，可用于对不包含键值的数据执行数据库查询。

## <a name="keyless-entity-types-characteristics"></a>无键实体类型特征

无键实体类型支持与常规实体类型相同的多个映射功能，如继承映射和导航属性。 上关系存储，他们可以配置的目标数据库对象和列通过 fluent API 方法或数据注释。

但是，它们不同于常规实体类型，因为它们：

- 不能定义键。
- 永远不会对_DbContext_中的更改进行跟踪，因此不会在数据库中插入、更新或删除这些更改。
- 永远不会由约定发现。
- 仅支持导航映射功能的子集，具体如下：
  - 它们可能永远不会作为关系的主体端。
  - 它们可能没有到拥有的实体的导航
  - 它们只能包含指向常规实体的引用导航属性。
  - 实体不能包含无键实体类型的导航属性。
- 需要配置`.HasNoKey()`方法调用。
- 可以映射到定义的_查询_。 定义查询是在模型中声明的查询，它充当无键实体类型的数据源。

## <a name="usage-scenarios"></a>使用方案

无键实体类型的一些主要使用方案包括：

- 作为[原始 SQL 查询](xref:core/querying/raw-sql)的返回类型。
- 映射到不包含主键的数据库视图。
- 映射到不具有定义的主键的表。
- 映射到模型中定义的查询。

## <a name="mapping-to-database-objects"></a>映射到数据库对象

使用`ToTable` 或`ToView` Fluent API 将无键实体类型映射到数据库对象。 从 EF Core 角度来看，此方法中指定的数据库对象是视图，这意味着它将被系统视为只读查询源，不能作为更新、插入或删除操作的目标。 但是，这并不意味着数据库对象实际上必须是数据库视图。 它也可以是将被视为只读的数据库表。 相反，对于常规实体类型，EF Core 假设在`ToTable`方法中指定的数据库对象可以被视为_表_，这意味着它可用作查询源，但也可作为更新、删除和插入操作的目标。 事实上，你可以在 `ToTable` 中指定数据库视图的名称，所有内容应该都能够正常运行，只要在数据库上将视图配置为可更新即可。

> [!NOTE]
> `ToView`假定该对象已存在于数据库中，并且不是通过迁移创建的。

## <a name="example"></a>示例

下面的示例演示如何使用无键实体类型来查询数据库视图。

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes)。

首先，我们定义一个简单的博客和文章模型：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

接下来，我们定义一个简单的数据库视图，这样就可以查询与每个博客帖子数：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

接下来，我们定义一个类来保存数据库视图的结果：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

接下来，使用`HasNoKey` API 在_OnModelCreating_中配置无键实体类型。
我们使用熟知的配置 API 来配置无键实体类型的映射：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

接下来，将配置`DbContext`为`DbSet<T>`包括：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

最后，我们可以采用标准方式来查询数据库视图：

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> 请注意，我们还定义了一个上下文级查询属性（DbSet）作为此类型的查询的根。
