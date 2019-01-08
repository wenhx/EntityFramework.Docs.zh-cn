---
title: 拥有 EF Core 的实体类型-
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: b2d72b08de79939904bf4e726c695440c906a8aa
ms.sourcegitcommit: 7bde8e6ad3c4565a4638646ce04bcf5e66f7b5fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2019
ms.locfileid: "54069199"
---
# <a name="owned-entity-types"></a><span data-ttu-id="9bb34-102">固有的实体类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="9bb34-103">此功能是在 EF Core 2.0 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="9bb34-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="9bb34-104">EF Core 让你可以只显示对其他实体类型的导航属性的模型实体类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="9bb34-105">这些被称为_固有实体类型_。</span><span class="sxs-lookup"><span data-stu-id="9bb34-105">These are called _owned entity types_.</span></span> <span data-ttu-id="9bb34-106">包含固有的实体类型的实体是其_所有者_。</span><span class="sxs-lookup"><span data-stu-id="9bb34-106">The entity containing an owned entity type is its _owner_.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="9bb34-107">显式配置</span><span class="sxs-lookup"><span data-stu-id="9bb34-107">Explicit configuration</span></span>

<span data-ttu-id="9bb34-108">拥有永远不会包含类型由 EF Core 模型中按照约定的实体。</span><span class="sxs-lookup"><span data-stu-id="9bb34-108">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="9bb34-109">你可以使用`OwnsOne`中的方法`OnModelCreating`或批注与类型`OwnedAttribute`（新在 EF Core 2.1） 配置为拥有类型的类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-109">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="9bb34-110">在此示例中，`StreetAddress`是没有标识属性的类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-110">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="9bb34-111">它用作 Order 类型的属性来指定特定订单的发货地址。</span><span class="sxs-lookup"><span data-stu-id="9bb34-111">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="9bb34-112">我们可以使用`OwnedAttribute`会将其视为拥有实体时从另一个实体类型引用：</span><span class="sxs-lookup"><span data-stu-id="9bb34-112">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="9bb34-113">还有可能要使用`OwnsOne`中的方法`OnModelCreating`以指定`ShippingAddress`属性是所拥有的实体`Order`实体类型，并根据需要配置其他方面。</span><span class="sxs-lookup"><span data-stu-id="9bb34-113">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="9bb34-114">如果`ShippingAddress`属性为私有`Order`类型，则可以使用的字符串版本`OwnsOne`方法：</span><span class="sxs-lookup"><span data-stu-id="9bb34-114">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="9bb34-115">请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)有关更多上下文。</span><span class="sxs-lookup"><span data-stu-id="9bb34-115">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span> 

## <a name="implicit-keys"></a><span data-ttu-id="9bb34-116">隐式键</span><span class="sxs-lookup"><span data-stu-id="9bb34-116">Implicit keys</span></span>

<span data-ttu-id="9bb34-117">固有类型配置了`OwnsOne`或通过引用导航发现始终具有一对一的关系的所有者，因此他们不需要其自己的密钥值，如外键的值是唯一的。</span><span class="sxs-lookup"><span data-stu-id="9bb34-117">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="9bb34-118">在上一示例中，`StreetAddress`类型不需要定义键属性。</span><span class="sxs-lookup"><span data-stu-id="9bb34-118">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="9bb34-119">若要了解如何 EF Core 可跟踪这些对象，最好先认为，作为创建主键[阴影属性](xref:core/modeling/shadow-properties)对于固有类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-119">In order to understand how EF Core tracks these objects, it is useful to think that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="9bb34-120">固有类型的实例键的值将为所有者实例的键的值相同。</span><span class="sxs-lookup"><span data-stu-id="9bb34-120">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="9bb34-121">固有类型的集合</span><span class="sxs-lookup"><span data-stu-id="9bb34-121">Collections of owned types</span></span>

>[!NOTE]
> <span data-ttu-id="9bb34-122">此为 EF Core 2.2 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="9bb34-122">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="9bb34-123">若要配置的固有类型的集合`OwnsMany`应在`OnModelCreating`。</span><span class="sxs-lookup"><span data-stu-id="9bb34-123">To configure a collection of owned types `OwnsMany` should be used in `OnModelCreating`.</span></span> <span data-ttu-id="9bb34-124">但是主要密钥将不会配置按照约定，因此，需要显式指定。</span><span class="sxs-lookup"><span data-stu-id="9bb34-124">However the primary key will not be configured by convention, so it needs to be specified explicitly.</span></span> <span data-ttu-id="9bb34-125">它是通常会为这些类型的合并的所有者和其他唯一属性，也可以在卷影状态中的外键的实体使用复杂的密钥：</span><span class="sxs-lookup"><span data-stu-id="9bb34-125">It is common to use a complex key for these type of entities incorporating the foreign key to the owner and an additional unique property that can also be in shadow state:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="9bb34-126">映射固有类型与表拆分</span><span class="sxs-lookup"><span data-stu-id="9bb34-126">Mapping owned types with table splitting</span></span>

<span data-ttu-id="9bb34-127">使用关系时数据库，按约定引用所有类型映射到与所有者相同的表。</span><span class="sxs-lookup"><span data-stu-id="9bb34-127">When using relational databases, by convention reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="9bb34-128">这要求将在两个表拆分： 某些列将用于存储数据的所有者，并且某些列将用于存储数据的固有实体。</span><span class="sxs-lookup"><span data-stu-id="9bb34-128">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="9bb34-129">这是一项称为表拆分的常见功能。</span><span class="sxs-lookup"><span data-stu-id="9bb34-129">This is a common feature known as table splitting.</span></span>

> [!TIP]
> <span data-ttu-id="9bb34-130">拥有表拆分与存储在一起的类型可以是使用类似于复杂类型的用法在 EF6 中。</span><span class="sxs-lookup"><span data-stu-id="9bb34-130">Owned types stored with table splitting can be used similarly to how complex types are used in EF6.</span></span>

<span data-ttu-id="9bb34-131">按照约定，EF Core 将遵循模式的固有的实体类型的属性命名的数据库列_Navigation_OwnedEntityProperty_。</span><span class="sxs-lookup"><span data-stu-id="9bb34-131">By convention, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="9bb34-132">因此`StreetAddress`属性将显示在具有名称 ShippingAddress_Street 和 ShippingAddress_City 的订单表。</span><span class="sxs-lookup"><span data-stu-id="9bb34-132">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="9bb34-133">您可以将附加`HasColumnName`方法来重命名这些列：</span><span class="sxs-lookup"><span data-stu-id="9bb34-133">You can append the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="9bb34-134">共享多个固有类型之间的相同.NET 类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-134">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="9bb34-135">固有的实体类型可以是相同的.NET 类型，作为另一个固有的实体类型，因此.NET 类型可能都不足以识别固有的类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-135">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="9bb34-136">在这些情况下，所有者从指向固有实体的属性将成为_定义导航_的固有的实体类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-136">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="9bb34-137">从 EF Core 的角度来看，定义导航是标识的旁边的.NET 类型的类型的一部分。</span><span class="sxs-lookup"><span data-stu-id="9bb34-137">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="9bb34-138">例如，在下面的类`ShippingAddress`并`BillingAddress`均为相同的.NET 类型， `StreetAddress`:</span><span class="sxs-lookup"><span data-stu-id="9bb34-138">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="9bb34-139">若要了解如何 EF Core 将区分跟踪这些对象的实例，可能会有用考虑定义导航已旁边的值的键的所有者的实例键的一部分，拥有类型的.NET 类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-139">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="9bb34-140">嵌套固有的类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-140">Nested owned types</span></span>

<span data-ttu-id="9bb34-141">在此示例中`OrderDetails`拥有`BillingAddress`并`ShippingAddress`，它们均`StreetAddress`类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-141">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="9bb34-142">然后 `OrderDetails` 归 `DetailedOrder` 类型所有。</span><span class="sxs-lookup"><span data-stu-id="9bb34-142">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="9bb34-143">嵌套固有类型，除了固有的类型可以引用常规实体，只要固有的实体的依赖端上，它可以是所有者或不同的实体。</span><span class="sxs-lookup"><span data-stu-id="9bb34-143">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="9bb34-144">此功能在 EF6 中设置除了复杂类型的固有的实体类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-144">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="9bb34-145">它是链接到`OwnsOne`中配置此模型的 fluent 调用的方法：</span><span class="sxs-lookup"><span data-stu-id="9bb34-145">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="9bb34-146">还有可能使用相同的操作实现目的`OwnedAttribute`上都`OrderDetails`和`StreetAdress`。</span><span class="sxs-lookup"><span data-stu-id="9bb34-146">It is also possible to achieve the same thing using `OwnedAttribute` on both `OrderDetails` and `StreetAdress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="9bb34-147">存储拥有单独的表中的类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-147">Storing owned types in separate tables</span></span>

<span data-ttu-id="9bb34-148">此外固有的类型不同于 EF6 的复杂类型，它可以存储在单独的表所有者。</span><span class="sxs-lookup"><span data-stu-id="9bb34-148">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="9bb34-149">若要重写将固有的类型映射到与所有者相同的表的约定，您可以简单地调用`ToTable`并提供一个不同的表名称。</span><span class="sxs-lookup"><span data-stu-id="9bb34-149">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="9bb34-150">下面的示例将映射`OrderDetails`到从单独的表及其两个址`DetailedOrder`:</span><span class="sxs-lookup"><span data-stu-id="9bb34-150">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a><span data-ttu-id="9bb34-151">查询固有的类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-151">Querying owned types</span></span>

<span data-ttu-id="9bb34-152">查询所有者时，固有类型将默认包含在内。</span><span class="sxs-lookup"><span data-stu-id="9bb34-152">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="9bb34-153">不需要使用`Include`方法，即使固有的类型存储在一个单独的表中。</span><span class="sxs-lookup"><span data-stu-id="9bb34-153">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="9bb34-154">根据之前所述的模型，以下查询将获取`Order`，`OrderDetails`和两个拥有`StreetAddresses`从数据库：</span><span class="sxs-lookup"><span data-stu-id="9bb34-154">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="9bb34-155">限制</span><span class="sxs-lookup"><span data-stu-id="9bb34-155">Limitations</span></span>

<span data-ttu-id="9bb34-156">其中某些限制是基础如何固有实体类型的工作，但另一些限制，我们可能无法将其删除在将来的版本：</span><span class="sxs-lookup"><span data-stu-id="9bb34-156">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="9bb34-157">按设计限制</span><span class="sxs-lookup"><span data-stu-id="9bb34-157">By-design restrictions</span></span>
- <span data-ttu-id="9bb34-158">无法创建`DbSet<T>`对于固有类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-158">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="9bb34-159">不能调用`Entity<T>()`固有类型上使用 `ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="9bb34-159">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="9bb34-160">当前的不足之处</span><span class="sxs-lookup"><span data-stu-id="9bb34-160">Current shortcomings</span></span>
- <span data-ttu-id="9bb34-161">包含的继承层次结构拥有不支持实体类型</span><span class="sxs-lookup"><span data-stu-id="9bb34-161">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="9bb34-162">引用导航到所拥有的实体类型不能为 null，除非它们显式映射到单独的表中的所有者</span><span class="sxs-lookup"><span data-stu-id="9bb34-162">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="9bb34-163">固有的实体类型的实例无法共享的多个所有者 （这是一个已知的值对象不能使用固有的实体类型实现的方案）</span><span class="sxs-lookup"><span data-stu-id="9bb34-163">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="9bb34-164">在早期版本中的不足之处</span><span class="sxs-lookup"><span data-stu-id="9bb34-164">Shortcomings in previous versions</span></span>
- <span data-ttu-id="9bb34-165">在 EF Core 2.0 中，导航到拥有不能在派生的实体类型中声明实体类型，除非自有的实体被显式映射到一个单独的表所有者层次结构中。</span><span class="sxs-lookup"><span data-stu-id="9bb34-165">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="9bb34-166">EF Core 2.1 中已删除此限制</span><span class="sxs-lookup"><span data-stu-id="9bb34-166">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="9bb34-167">在 EF Core 2.0 和 2.1 仅引用中支持导航到固有类型。</span><span class="sxs-lookup"><span data-stu-id="9bb34-167">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="9bb34-168">在 EF Core 2.2 中消除这一限制</span><span class="sxs-lookup"><span data-stu-id="9bb34-168">This limitation has been removed in EF Core 2.2</span></span>
