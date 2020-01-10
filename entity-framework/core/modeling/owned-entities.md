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
# <a name="owned-entity-types"></a><span data-ttu-id="10598-103">从属实体类型</span><span class="sxs-lookup"><span data-stu-id="10598-103">Owned Entity Types</span></span>

<span data-ttu-id="10598-104">EF Core 让你可以只显示对其他实体类型的导航属性的模型实体类型。</span><span class="sxs-lookup"><span data-stu-id="10598-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="10598-105">它们称为_拥有的实体类型_。</span><span class="sxs-lookup"><span data-stu-id="10598-105">These are called _owned entity types_.</span></span> <span data-ttu-id="10598-106">包含拥有的实体类型的实体是其_所有者_。</span><span class="sxs-lookup"><span data-stu-id="10598-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="10598-107">拥有的实体实质上是所有者的一部分，并且在没有它的情况下不存在，它们在概念上类似于[聚合](https://martinfowler.com/bliki/DDD_Aggregate.html)。</span><span class="sxs-lookup"><span data-stu-id="10598-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="10598-108">这意味着，拥有的类型是根据与所有者的关系的依赖端上的定义。</span><span class="sxs-lookup"><span data-stu-id="10598-108">This means that the owned type is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="10598-109">显式配置</span><span class="sxs-lookup"><span data-stu-id="10598-109">Explicit configuration</span></span>

<span data-ttu-id="10598-110">拥有永远不会包含类型由 EF Core 模型中按照约定的实体。</span><span class="sxs-lookup"><span data-stu-id="10598-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="10598-111">你可以使用`OwnsOne`中的方法`OnModelCreating`或批注与类型`OwnedAttribute`（新在 EF Core 2.1） 配置为拥有类型的类型。</span><span class="sxs-lookup"><span data-stu-id="10598-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="10598-112">在此示例中，`StreetAddress` 是无标识属性的类型。</span><span class="sxs-lookup"><span data-stu-id="10598-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="10598-113">它用作 Order 类型的属性来指定特定订单的发货地址。</span><span class="sxs-lookup"><span data-stu-id="10598-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="10598-114">在从另一个实体类型引用时，可以使用 `OwnedAttribute` 将其视为拥有的实体：</span><span class="sxs-lookup"><span data-stu-id="10598-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="10598-115">还可以使用 `OnModelCreating` 中的 `OwnsOne` 方法来指定 `ShippingAddress` 属性是 `Order` 实体类型的拥有实体，并根据需要配置其他方面。</span><span class="sxs-lookup"><span data-stu-id="10598-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="10598-116">如果 `ShippingAddress` 属性在 `Order` 类型中是私有的，则可以使用 `OwnsOne` 方法的字符串版本：</span><span class="sxs-lookup"><span data-stu-id="10598-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="10598-117">有关更多上下文，请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)。</span><span class="sxs-lookup"><span data-stu-id="10598-117">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="10598-118">隐式键</span><span class="sxs-lookup"><span data-stu-id="10598-118">Implicit keys</span></span>

<span data-ttu-id="10598-119">使用 `OwnsOne` 配置的或通过引用导航发现的拥有的类型与所有者始终具有一对一的关系，因此，它们不需要其自己的键值，因为外键值是唯一的。</span><span class="sxs-lookup"><span data-stu-id="10598-119">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="10598-120">在上面的示例中，`StreetAddress` 类型不需要定义键属性。</span><span class="sxs-lookup"><span data-stu-id="10598-120">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="10598-121">为了理解 EF Core 如何跟踪这些对象，知道主键是作为所属类型的[影子属性](xref:core/modeling/shadow-properties)创建的，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="10598-121">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="10598-122">所拥有类型的实例的键值将与所有者实例的键的值相同。</span><span class="sxs-lookup"><span data-stu-id="10598-122">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="10598-123">拥有的类型的集合</span><span class="sxs-lookup"><span data-stu-id="10598-123">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="10598-124">此为 EF Core 2.2 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="10598-124">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="10598-125">若要配置拥有类型的集合，请使用 `OnModelCreating`中 `OwnsMany`。</span><span class="sxs-lookup"><span data-stu-id="10598-125">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="10598-126">拥有的类型需要主键。</span><span class="sxs-lookup"><span data-stu-id="10598-126">Owned types need a primary key.</span></span> <span data-ttu-id="10598-127">如果 .NET 类型上没有合适的候选属性，EF Core 可以尝试创建一个。</span><span class="sxs-lookup"><span data-stu-id="10598-127">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="10598-128">但是，当所有类型都通过集合进行定义时，只需创建一个影子属性以同时充当所有者的外键和该拥有实例的主键，就像在 `OwnsOne`中一样：对于每个所有者，可以有多个拥有的类型实例，因此，所有者的密钥不足以为每个拥有的实例提供唯一标识。</span><span class="sxs-lookup"><span data-stu-id="10598-128">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="10598-129">这两个最直接的解决方案是：</span><span class="sxs-lookup"><span data-stu-id="10598-129">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="10598-130">在独立于指向所有者的外键的新属性上定义代理项主键。</span><span class="sxs-lookup"><span data-stu-id="10598-130">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="10598-131">所有所有者都需要具有唯一的值（例如，如果父 {1} 具有子 {1}，则父 {2} 不能具有子 {1}），因此该值没有任何固有含义。</span><span class="sxs-lookup"><span data-stu-id="10598-131">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="10598-132">由于外键不是主键的一部分，因此可以更改其值，因此，您可以将子级从一个父级移到另一个父级，但这通常会针对聚合语义进行。</span><span class="sxs-lookup"><span data-stu-id="10598-132">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="10598-133">使用外键和附加属性作为组合键。</span><span class="sxs-lookup"><span data-stu-id="10598-133">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="10598-134">现在，附加属性值只需对于给定父级是唯一的（因此，如果父 {1} 具有子 {1,1}，则父 {2} 仍可以具有子 {2,1}）。</span><span class="sxs-lookup"><span data-stu-id="10598-134">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="10598-135">通过创建主键的外键部分，所有者和拥有的实体之间的关系将变为不可变的，并且更好地反映了聚合语义。</span><span class="sxs-lookup"><span data-stu-id="10598-135">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="10598-136">这是 EF Core 默认情况下执行的操作。</span><span class="sxs-lookup"><span data-stu-id="10598-136">This is what EF Core does by default.</span></span>

<span data-ttu-id="10598-137">在此示例中，我们将使用 `Distributor` 类：</span><span class="sxs-lookup"><span data-stu-id="10598-137">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="10598-138">默认情况下，通过 `ShippingCenters` 导航属性引用的所属类型使用的主键将 `("DistributorId", "Id")` 其中 `"DistributorId"` 为 FK，`"Id"` 为唯一的 `int` 值。</span><span class="sxs-lookup"><span data-stu-id="10598-138">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="10598-139">若要配置不同的 PK 调用 `HasKey`：</span><span class="sxs-lookup"><span data-stu-id="10598-139">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="10598-140">EF Core 3.0 `WithOwner()` 方法不存在，因此应删除此调用。</span><span class="sxs-lookup"><span data-stu-id="10598-140">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span> <span data-ttu-id="10598-141">此外，不会自动发现主键，因此始终指定了它。</span><span class="sxs-lookup"><span data-stu-id="10598-141">Also the primary key was not discovered automatically so it always had be specified.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="10598-142">将拥有的类型映射到表拆分</span><span class="sxs-lookup"><span data-stu-id="10598-142">Mapping owned types with table splitting</span></span>

<span data-ttu-id="10598-143">使用关系数据库时，默认情况下，引用拥有的类型将映射到与所有者相同的表。</span><span class="sxs-lookup"><span data-stu-id="10598-143">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="10598-144">这需要将表拆分为两个：某些列将用于存储所有者的数据，某些列将用于存储拥有实体的数据。</span><span class="sxs-lookup"><span data-stu-id="10598-144">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="10598-145">这是一种称为[表拆分](table-splitting.md)的常见功能。</span><span class="sxs-lookup"><span data-stu-id="10598-145">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="10598-146">默认情况下，EF Core 会按照模式_Navigation_OwnedEntityProperty_为拥有的实体类型的属性命名数据库列。</span><span class="sxs-lookup"><span data-stu-id="10598-146">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="10598-147">因此，`StreetAddress` 属性将显示在 "Orders" 表中，名称为 "ShippingAddress_Street" 和 "ShippingAddress_City"。</span><span class="sxs-lookup"><span data-stu-id="10598-147">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="10598-148">您可以使用 `HasColumnName` 方法重命名这些列：</span><span class="sxs-lookup"><span data-stu-id="10598-148">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="10598-149">大多数正常的实体类型配置方法（如[Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) ）都可以通过相同的方式进行调用。</span><span class="sxs-lookup"><span data-stu-id="10598-149">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="10598-150">在多个所拥有的类型之间共享相同的 .NET 类型</span><span class="sxs-lookup"><span data-stu-id="10598-150">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="10598-151">一个拥有的实体类型可以是与另一个拥有的实体类型相同的 .NET 类型，因此，.NET 类型可能不足以标识某个所有者的类型。</span><span class="sxs-lookup"><span data-stu-id="10598-151">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="10598-152">在这些情况下，从所有者指向拥有的实体的属性将成为所拥有实体类型的_定义导航_。</span><span class="sxs-lookup"><span data-stu-id="10598-152">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="10598-153">从 EF Core 的角度来看，定义导航是标识的旁边的.NET 类型的类型的一部分。</span><span class="sxs-lookup"><span data-stu-id="10598-153">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="10598-154">例如，在下面的类中 `ShippingAddress` 和 `BillingAddress` 均为同一 .NET 类型，`StreetAddress`：</span><span class="sxs-lookup"><span data-stu-id="10598-154">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="10598-155">若要了解如何 EF Core 将区分跟踪这些对象的实例，可能会有用考虑定义导航已旁边的值的键的所有者的实例键的一部分，拥有类型的.NET 类型。</span><span class="sxs-lookup"><span data-stu-id="10598-155">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="10598-156">嵌套的所属类型</span><span class="sxs-lookup"><span data-stu-id="10598-156">Nested owned types</span></span>

<span data-ttu-id="10598-157">在此示例中 `OrderDetails` 拥有 `BillingAddress` 和 `ShippingAddress`，它们都是 `StreetAddress` 类型。</span><span class="sxs-lookup"><span data-stu-id="10598-157">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="10598-158">然后 `OrderDetails` 归 `DetailedOrder` 类型所有。</span><span class="sxs-lookup"><span data-stu-id="10598-158">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="10598-159">每个指向所拥有的类型的导航都定义一个具有完全独立配置的单独实体类型。</span><span class="sxs-lookup"><span data-stu-id="10598-159">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="10598-160">除了嵌套的类型外，拥有的类型还可以引用常规实体，只要拥有的实体在依赖方，就可以是所有者或其他实体。</span><span class="sxs-lookup"><span data-stu-id="10598-160">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="10598-161">此功能在 EF6 中除复杂类型外，会设置拥有的实体类型。</span><span class="sxs-lookup"><span data-stu-id="10598-161">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="10598-162">可以通过将 `OwnsOne` 方法链接到一个流畅的调用来配置此模型：</span><span class="sxs-lookup"><span data-stu-id="10598-162">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="10598-163">请注意用于配置指向所有者的导航属性的 `WithOwner` 调用。</span><span class="sxs-lookup"><span data-stu-id="10598-163">Notice the `WithOwner` call used to configure the navigation property pointing back at the owner.</span></span> <span data-ttu-id="10598-164">若要配置不包含在所有权关系中的 owner 实体类型的导航 `WithOwner()` 应在不使用任何参数的情况下调用。</span><span class="sxs-lookup"><span data-stu-id="10598-164">To configure a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="10598-165">使用 `OrderDetails` 和 `StreetAddress`上的 `OwnedAttribute` 可以获得结果。</span><span class="sxs-lookup"><span data-stu-id="10598-165">It is possible to achieve the result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="10598-166">将拥有的类型存储在单独的表中</span><span class="sxs-lookup"><span data-stu-id="10598-166">Storing owned types in separate tables</span></span>

<span data-ttu-id="10598-167">与 EF6 复杂类型不同的是，拥有的类型可以存储在所有者的单独表中。</span><span class="sxs-lookup"><span data-stu-id="10598-167">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="10598-168">若要重写将拥有的类型映射到与所有者相同的表的约定，只需调用 `ToTable` 并提供不同的表名即可。</span><span class="sxs-lookup"><span data-stu-id="10598-168">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="10598-169">下面的示例将 `OrderDetails` 及其两个地址映射到不同 `DetailedOrder`的表中：</span><span class="sxs-lookup"><span data-stu-id="10598-169">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="10598-170">还可以使用 `TableAttribute` 来实现此目的，但请注意，如果有多个导航到拥有的类型，则这会失败，因为在这种情况下，多个实体类型会映射到同一个表。</span><span class="sxs-lookup"><span data-stu-id="10598-170">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="10598-171">查询拥有的类型</span><span class="sxs-lookup"><span data-stu-id="10598-171">Querying owned types</span></span>

<span data-ttu-id="10598-172">查询所有者时，固有类型将默认包含在内。</span><span class="sxs-lookup"><span data-stu-id="10598-172">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="10598-173">不需要使用 `Include` 方法，即使所有类型都存储在单独的表中也是如此。</span><span class="sxs-lookup"><span data-stu-id="10598-173">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="10598-174">根据前面所述的模型，以下查询将从数据库中获取 `Order`、`OrderDetails` 和两个所拥有的 `StreetAddresses`：</span><span class="sxs-lookup"><span data-stu-id="10598-174">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="10598-175">限制</span><span class="sxs-lookup"><span data-stu-id="10598-175">Limitations</span></span>

<span data-ttu-id="10598-176">其中一些限制对于拥有的实体类型的工作方式很重要，但其他一些限制是我们可以在未来版本中删除的限制：</span><span class="sxs-lookup"><span data-stu-id="10598-176">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="10598-177">按设计限制</span><span class="sxs-lookup"><span data-stu-id="10598-177">By-design restrictions</span></span>

- <span data-ttu-id="10598-178">不能为拥有的类型创建 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="10598-178">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="10598-179">无法在 `ModelBuilder` 上使用拥有的类型调用 `Entity<T>()`</span><span class="sxs-lookup"><span data-stu-id="10598-179">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="10598-180">当前缺陷</span><span class="sxs-lookup"><span data-stu-id="10598-180">Current shortcomings</span></span>

- <span data-ttu-id="10598-181">拥有的实体类型不能具有继承层次结构</span><span class="sxs-lookup"><span data-stu-id="10598-181">Owned entity types cannot have inheritance hierarchies</span></span>
- <span data-ttu-id="10598-182">引用导航到拥有的实体类型不能为 null，除非它们显式映射到与所有者不同的表</span><span class="sxs-lookup"><span data-stu-id="10598-182">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="10598-183">拥有的实体类型的实例不能由多个所有者共享（这是一个已知的值对象方案，不能使用拥有的实体类型来实现）</span><span class="sxs-lookup"><span data-stu-id="10598-183">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="10598-184">以前版本中的缺点</span><span class="sxs-lookup"><span data-stu-id="10598-184">Shortcomings in previous versions</span></span>

- <span data-ttu-id="10598-185">在 EF Core 2.0 中，在派生的实体类型中不能声明导航到拥有的实体类型，除非将拥有的实体显式映射到所有者层次结构中的单独表。</span><span class="sxs-lookup"><span data-stu-id="10598-185">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="10598-186">此限制已在 EF Core 2.1 中被删除</span><span class="sxs-lookup"><span data-stu-id="10598-186">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="10598-187">在 EF Core 仅支持2.0 和2.1 的引用导航到拥有的类型。</span><span class="sxs-lookup"><span data-stu-id="10598-187">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="10598-188">此限制已在 EF Core 2.2 中被删除</span><span class="sxs-lookup"><span data-stu-id="10598-188">This limitation has been removed in EF Core 2.2</span></span>
