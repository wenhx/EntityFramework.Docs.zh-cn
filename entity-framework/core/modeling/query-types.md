---
title: 查询类型的 EF Core
author: anpete
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/query-types
ms.openlocfilehash: cb391343e6f24092ae0874003c0ef2935dd4e03f
ms.sourcegitcommit: 8dd71a57a01c439431164c163a0722877d0e5cd8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53028175"
---
# <a name="query-types"></a>查询类型
> [!NOTE]
> 此功能是 EF Core 2.1 中的新增功能

除了实体类型，EF Core 模型也可以包含查询类型  ，这些查询类型可以用于执行针对未映射到实体类型的数据的数据库查询。

## <a name="compare-query-types-to-entity-types"></a>比较查询类型与实体类型

查询类型中以下几点和实体类型是相似的：

- 可以在 `OnModelCreating` 中将其添加到模型，或者通过派生的 _DbContext_ 上的“set”属性来这样做。
- 支持许多相同的映射功能，如继承映射和导航属性。 在关系型存储上，它们可以通过 Fluent API 方法或数据注释配置目标数据库对象和列。

但是，查询类型和实体类型又有一些不同：

- 不需要定义键。
- 永远不会被 _DbContext_ 跟踪更改，因此在数据库中永远不会被插入、 更新或删除。
- 永远不会由约定发现。
- 仅支持一部分导航映射功能-具体而言：
  - 它们可能永远不会作为关系的主体端。
  - 它们仅可包含指向的实体的引用导航属性。
  - 实体不能包含查询类型的导航属性。
- 在 _ModelBuilder_ 上通过 `Query` 方法而非 `Entity` 方法进行解决。
- 通过类型 `DbQuery<T>` 而非 `DbSet<T>` 的属性在 _DbContext_ 上进行映射。
- 通过 `ToView` 而非 `ToTable` 方法映射到数据库对象。
- 可以映射到定义查询  - 定义查询是在模型（充当查询类型的数据源）中声明的辅助查询。

## <a name="usage-scenarios"></a>使用方案

下面是查询类型的一些主要使用场景：

- 充当即席 `FromSql()` 查询的返回类型。
- 映射到数据库视图。
- 映射到不具有定义的主键的表。
- 映射到模型中定义的查询。

## <a name="mapping-to-database-objects"></a>映射到数据库对象

可以使用 `ToView` Fluent API 将一个查询类型映射到数据库对象。 从 EF Core 角度来看，此方法中指定的数据库对象是视图  ，这意味着它将被系统视为只读查询源，不能作为更新、插入或删除操作的目标。 但是，这并不意味着该数据库对象实际上必须是一个数据库视图 - 它也可以是一个被视为只读对象的数据库表。 与之相反，对于实体类型，EF Core 假定在 `ToTable` 方法中指定的数据库对象可以被视为表  ，这意味着可以将它用作查询源，同时也可以对其进行更新、删除和插入操作。 事实上，你可以在 `ToTable` 中指定数据库视图的名称，所有内容应该都能够正常运行，只要在数据库上将视图配置为可更新即可。

## <a name="example"></a>示例

下面的示例演示如何使用查询类型来查询数据库视图。

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes)。

首先，我们定义一个简单的博客和文章模型：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Entities)]

接下来，我们定义一个简单的数据库视图，这样就可以查询与每个博客帖子数：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#View)]

接下来，我们定义一个类来保存数据库视图的结果：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#QueryType)]

接下来，我们使用 `modelBuilder.Query<T>` API 在 _OnModelCreating_ 中配置查询类型。
我们使用标准的 Fluent API 来配置查询类型的映射：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Configuration)]

最后，我们可以采用标准方式来查询数据库视图：

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Query)]

> [!TIP]
> 请注意我们还定义了上下文级别查询属性 (DbQuery) 作为针对此类型的查询的根。
