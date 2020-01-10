---
title: 拥有 EF Core 的实体类型-
description: 如何在使用时配置拥有的实体类型或聚合 Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 30b91b6e66b6c0f516d1ba12485304b52770cbef
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781230"
---
# <a name="owned-entity-types"></a>从属实体类型

EF Core 让你可以只显示对其他实体类型的导航属性的模型实体类型。 它们称为_拥有的实体类型_。 包含拥有的实体类型的实体是其_所有者_。

拥有的实体实质上是所有者的一部分，并且在没有它的情况下不存在，它们在概念上类似于[聚合](https://martinfowler.com/bliki/DDD_Aggregate.html)。 这意味着，拥有的类型是根据与所有者的关系的依赖端上的定义。

## <a name="explicit-configuration"></a>显式配置

拥有永远不会包含类型由 EF Core 模型中按照约定的实体。 你可以使用`OwnsOne`中的方法`OnModelCreating`或批注与类型`OwnedAttribute`（新在 EF Core 2.1） 配置为拥有类型的类型。

在此示例中，`StreetAddress` 是无标识属性的类型。 它用作 Order 类型的属性来指定特定订单的发货地址。

在从另一个实体类型引用时，可以使用 `OwnedAttribute` 将其视为拥有的实体：

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

还可以使用 `OnModelCreating` 中的 `OwnsOne` 方法来指定 `ShippingAddress` 属性是 `Order` 实体类型的拥有实体，并根据需要配置其他方面。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

如果 `ShippingAddress` 属性在 `Order` 类型中是私有的，则可以使用 `OwnsOne` 方法的字符串版本：

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

有关更多上下文，请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)。

## <a name="implicit-keys"></a>隐式键

使用 `OwnsOne` 配置的或通过引用导航发现的拥有的类型与所有者始终具有一对一的关系，因此，它们不需要其自己的键值，因为外键值是唯一的。 在上面的示例中，`StreetAddress` 类型不需要定义键属性。  

为了理解 EF Core 如何跟踪这些对象，知道主键是作为所属类型的[影子属性](xref:core/modeling/shadow-properties)创建的，这会很有用。 所拥有类型的实例的键值将与所有者实例的键的值相同。

## <a name="collections-of-owned-types"></a>拥有的类型的集合

> [!NOTE]
> 此为 EF Core 2.2 中的新增功能。

若要配置拥有类型的集合，请使用 `OnModelCreating`中 `OwnsMany`。

拥有的类型需要主键。 如果 .NET 类型上没有合适的候选属性，EF Core 可以尝试创建一个。 但是，当所有类型都通过集合进行定义时，只需创建一个影子属性以同时充当所有者的外键和该拥有实例的主键，就像在 `OwnsOne`中一样：对于每个所有者，可以有多个拥有的类型实例，因此，所有者的密钥不足以为每个拥有的实例提供唯一标识。

这两个最直接的解决方案是：

- 在独立于指向所有者的外键的新属性上定义代理项主键。 所有所有者都需要具有唯一的值（例如，如果父 {1} 具有子 {1}，则父 {2} 不能具有子 {1}），因此该值没有任何固有含义。 由于外键不是主键的一部分，因此可以更改其值，因此，您可以将子级从一个父级移到另一个父级，但这通常会针对聚合语义进行。
- 使用外键和附加属性作为组合键。 现在，附加属性值只需对于给定父级是唯一的（因此，如果父 {1} 具有子 {1,1}，则父 {2} 仍可以具有子 {2,1}）。 通过创建主键的外键部分，所有者和拥有的实体之间的关系将变为不可变的，并且更好地反映了聚合语义。 这是 EF Core 默认情况下执行的操作。

在此示例中，我们将使用 `Distributor` 类：

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

默认情况下，通过 `ShippingCenters` 导航属性引用的所属类型使用的主键将 `("DistributorId", "Id")` 其中 `"DistributorId"` 为 FK，`"Id"` 为唯一的 `int` 值。

若要配置不同的 PK 调用 `HasKey`：

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> EF Core 3.0 `WithOwner()` 方法不存在，因此应删除此调用。 此外，不会自动发现主键，因此始终指定了它。

## <a name="mapping-owned-types-with-table-splitting"></a>将拥有的类型映射到表拆分

使用关系数据库时，默认情况下，引用拥有的类型将映射到与所有者相同的表。 这需要将表拆分为两个：某些列将用于存储所有者的数据，某些列将用于存储拥有实体的数据。 这是一种称为[表拆分](table-splitting.md)的常见功能。

默认情况下，EF Core 会按照模式_Navigation_OwnedEntityProperty_为拥有的实体类型的属性命名数据库列。 因此，`StreetAddress` 属性将显示在 "Orders" 表中，名称为 "ShippingAddress_Street" 和 "ShippingAddress_City"。

您可以使用 `HasColumnName` 方法重命名这些列：

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> 大多数正常的实体类型配置方法（如[Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) ）都可以通过相同的方式进行调用。

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>在多个所拥有的类型之间共享相同的 .NET 类型

一个拥有的实体类型可以是与另一个拥有的实体类型相同的 .NET 类型，因此，.NET 类型可能不足以标识某个所有者的类型。

在这些情况下，从所有者指向拥有的实体的属性将成为所拥有实体类型的_定义导航_。 从 EF Core 的角度来看，定义导航是标识的旁边的.NET 类型的类型的一部分。

例如，在下面的类中 `ShippingAddress` 和 `BillingAddress` 均为同一 .NET 类型，`StreetAddress`：

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

若要了解如何 EF Core 将区分跟踪这些对象的实例，可能会有用考虑定义导航已旁边的值的键的所有者的实例键的一部分，拥有类型的.NET 类型。

## <a name="nested-owned-types"></a>嵌套的所属类型

在此示例中 `OrderDetails` 拥有 `BillingAddress` 和 `ShippingAddress`，它们都是 `StreetAddress` 类型。 然后 `OrderDetails` 归 `DetailedOrder` 类型所有。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

每个指向所拥有的类型的导航都定义一个具有完全独立配置的单独实体类型。

除了嵌套的类型外，拥有的类型还可以引用常规实体，只要拥有的实体在依赖方，就可以是所有者或其他实体。 此功能在 EF6 中除复杂类型外，会设置拥有的实体类型。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

可以通过将 `OwnsOne` 方法链接到一个流畅的调用来配置此模型：

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

请注意用于配置指向所有者的导航属性的 `WithOwner` 调用。 若要配置不包含在所有权关系中的 owner 实体类型的导航 `WithOwner()` 应在不使用任何参数的情况下调用。

使用 `OrderDetails` 和 `StreetAddress`上的 `OwnedAttribute` 可以获得结果。

## <a name="storing-owned-types-in-separate-tables"></a>将拥有的类型存储在单独的表中

与 EF6 复杂类型不同的是，拥有的类型可以存储在所有者的单独表中。 若要重写将拥有的类型映射到与所有者相同的表的约定，只需调用 `ToTable` 并提供不同的表名即可。 下面的示例将 `OrderDetails` 及其两个地址映射到不同 `DetailedOrder`的表中：

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

还可以使用 `TableAttribute` 来实现此目的，但请注意，如果有多个导航到拥有的类型，则这会失败，因为在这种情况下，多个实体类型会映射到同一个表。

## <a name="querying-owned-types"></a>查询拥有的类型

查询所有者时，固有类型将默认包含在内。 不需要使用 `Include` 方法，即使所有类型都存储在单独的表中也是如此。 根据前面所述的模型，以下查询将从数据库中获取 `Order`、`OrderDetails` 和两个所拥有的 `StreetAddresses`：

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>限制

其中一些限制对于拥有的实体类型的工作方式很重要，但其他一些限制是我们可以在未来版本中删除的限制：

### <a name="by-design-restrictions"></a>按设计限制

- 不能为拥有的类型创建 `DbSet<T>`
- 无法在 `ModelBuilder` 上使用拥有的类型调用 `Entity<T>()`

### <a name="current-shortcomings"></a>当前缺陷

- 拥有的实体类型不能具有继承层次结构
- 引用导航到拥有的实体类型不能为 null，除非它们显式映射到与所有者不同的表
- 拥有的实体类型的实例不能由多个所有者共享（这是一个已知的值对象方案，不能使用拥有的实体类型来实现）

### <a name="shortcomings-in-previous-versions"></a>以前版本中的缺点

- 在 EF Core 2.0 中，在派生的实体类型中不能声明导航到拥有的实体类型，除非将拥有的实体显式映射到所有者层次结构中的单独表。 此限制已在 EF Core 2.1 中被删除
- 在 EF Core 仅支持2.0 和2.1 的引用导航到拥有的类型。 此限制已在 EF Core 2.2 中被删除
