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
# <a name="keyless-entity-types"></a><span data-ttu-id="c1d15-103">无键实体类型</span><span class="sxs-lookup"><span data-stu-id="c1d15-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="c1d15-104">此功能在 EF Core 2.1 中以查询类型的名义添加。</span><span class="sxs-lookup"><span data-stu-id="c1d15-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="c1d15-105">在 EF Core 3.0 中，概念重命名为无钥匙实体类型。</span><span class="sxs-lookup"><span data-stu-id="c1d15-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="c1d15-106">除了常规实体类型外，EF Core 模型还可以包含_无键实体类型_，可用于对不包含键值的数据执行数据库查询。</span><span class="sxs-lookup"><span data-stu-id="c1d15-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="c1d15-107">定义无键实体类型</span><span class="sxs-lookup"><span data-stu-id="c1d15-107">Defining Keyless entity types</span></span>

<span data-ttu-id="c1d15-108">可以使用数据注释或 Fluent API 定义无键实体类型：</span><span class="sxs-lookup"><span data-stu-id="c1d15-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c1d15-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="c1d15-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="c1d15-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c1d15-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="c1d15-111">无键实体类型特征</span><span class="sxs-lookup"><span data-stu-id="c1d15-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="c1d15-112">无键实体类型支持许多与常规实体类型相同的映射功能，如继承映射和导航属性。</span><span class="sxs-lookup"><span data-stu-id="c1d15-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="c1d15-113">在关系存储上，他们可以通过流畅的 API 方法或数据注释配置目标数据库对象和列。</span><span class="sxs-lookup"><span data-stu-id="c1d15-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="c1d15-114">但是，它们不同于常规实体类型，因为它们：</span><span class="sxs-lookup"><span data-stu-id="c1d15-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="c1d15-115">无法定义密钥。</span><span class="sxs-lookup"><span data-stu-id="c1d15-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="c1d15-116">永远不会跟踪_DbContext_中的更改，因此永远不会在数据库中插入、更新或删除。</span><span class="sxs-lookup"><span data-stu-id="c1d15-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="c1d15-117">从来都没有被惯例发现。</span><span class="sxs-lookup"><span data-stu-id="c1d15-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="c1d15-118">仅支持导航映射功能的子集，特别是：</span><span class="sxs-lookup"><span data-stu-id="c1d15-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="c1d15-119">他们可能永远不会作为关系的主要目的。</span><span class="sxs-lookup"><span data-stu-id="c1d15-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="c1d15-120">它们可能没有指向拥有的实体的导航</span><span class="sxs-lookup"><span data-stu-id="c1d15-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="c1d15-121">它们只能包含指向常规实体的引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="c1d15-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="c1d15-122">实体不能包含无键实体类型的导航属性。</span><span class="sxs-lookup"><span data-stu-id="c1d15-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="c1d15-123">需要使用`[Keyless]`数据注释或方法调用进行`.HasNoKey()`配置。</span><span class="sxs-lookup"><span data-stu-id="c1d15-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="c1d15-124">可以映射到_定义查询_。</span><span class="sxs-lookup"><span data-stu-id="c1d15-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="c1d15-125">定义查询是在模型中声明的查询，它充当无键实体类型的数据源。</span><span class="sxs-lookup"><span data-stu-id="c1d15-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="c1d15-126">使用方案</span><span class="sxs-lookup"><span data-stu-id="c1d15-126">Usage scenarios</span></span>

<span data-ttu-id="c1d15-127">无键实体类型的一些主要使用方案是：</span><span class="sxs-lookup"><span data-stu-id="c1d15-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="c1d15-128">用作[原始 SQL 查询的](xref:core/querying/raw-sql)返回类型。</span><span class="sxs-lookup"><span data-stu-id="c1d15-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="c1d15-129">映射到不包含主键的数据库视图。</span><span class="sxs-lookup"><span data-stu-id="c1d15-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="c1d15-130">映射到未定义主键的表。</span><span class="sxs-lookup"><span data-stu-id="c1d15-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="c1d15-131">映射到模型中定义的查询。</span><span class="sxs-lookup"><span data-stu-id="c1d15-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="c1d15-132">映射到数据库对象</span><span class="sxs-lookup"><span data-stu-id="c1d15-132">Mapping to database objects</span></span>

<span data-ttu-id="c1d15-133">使用`ToTable`或`ToView`fluent API 将无键实体类型映射到数据库对象。</span><span class="sxs-lookup"><span data-stu-id="c1d15-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="c1d15-134">从 EF Core 的角度来看，此方法中指定的数据库对象是一个_视图_，这意味着它被视为只读查询源，不能成为更新、插入或删除操作的目标。</span><span class="sxs-lookup"><span data-stu-id="c1d15-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="c1d15-135">但是，这并不意味着数据库对象实际上需要是数据库视图。</span><span class="sxs-lookup"><span data-stu-id="c1d15-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="c1d15-136">它也可以是数据库表，将被视为只读。</span><span class="sxs-lookup"><span data-stu-id="c1d15-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="c1d15-137">相反，对于常规实体类型，EF Core 假定`ToTable`方法中指定的数据库对象可以视为_表_，这意味着它可以用作查询源，但也可以通过更新、删除和插入操作作为目标。</span><span class="sxs-lookup"><span data-stu-id="c1d15-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="c1d15-138">事实上，您可以指定 中的`ToTable`数据库视图的名称，只要视图配置为在数据库上可升算，所有内容都应该正常工作。</span><span class="sxs-lookup"><span data-stu-id="c1d15-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="c1d15-139">`ToView`假定该对象已存在于数据库中，并且不会由迁移创建。</span><span class="sxs-lookup"><span data-stu-id="c1d15-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="c1d15-140">示例</span><span class="sxs-lookup"><span data-stu-id="c1d15-140">Example</span></span>

<span data-ttu-id="c1d15-141">下面的示例演示如何使用无键实体类型来查询数据库视图。</span><span class="sxs-lookup"><span data-stu-id="c1d15-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="c1d15-142">您可以在 GitHub 上查看本文[的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)。</span><span class="sxs-lookup"><span data-stu-id="c1d15-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="c1d15-143">首先，我们定义了一个简单的博客和帖子模型：</span><span class="sxs-lookup"><span data-stu-id="c1d15-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="c1d15-144">接下来，我们定义一个简单的数据库视图，该视图将使我们能够查询与每个博客关联的帖子数：</span><span class="sxs-lookup"><span data-stu-id="c1d15-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="c1d15-145">接下来，我们定义一个类来保存数据库视图的结果：</span><span class="sxs-lookup"><span data-stu-id="c1d15-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="c1d15-146">接下来，我们使用`HasNoKey`API 在_OnModel 创建_中配置无钥匙实体类型。</span><span class="sxs-lookup"><span data-stu-id="c1d15-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="c1d15-147">我们使用流畅的配置 API 配置无钥匙实体类型的映射：</span><span class="sxs-lookup"><span data-stu-id="c1d15-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="c1d15-148">接下来，我们将 配置为`DbContext`包括`DbSet<T>`：</span><span class="sxs-lookup"><span data-stu-id="c1d15-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="c1d15-149">最后，我们可以以标准方式查询数据库视图：</span><span class="sxs-lookup"><span data-stu-id="c1d15-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="c1d15-150">请注意，我们还定义了上下文级别查询属性 （DbSet）， 以充当针对此类型的查询的根。</span><span class="sxs-lookup"><span data-stu-id="c1d15-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
