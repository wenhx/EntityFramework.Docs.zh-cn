---
title: 无键实体类型-EF Core
description: 如何使用 Entity Framework Core 配置无键实体类型
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 520c9ed93240c05deee36fa527a3757490fd7082
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414643"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="eb5f2-103">无键实体类型</span><span class="sxs-lookup"><span data-stu-id="eb5f2-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="eb5f2-104">此功能已添加到 EF Core 2.1 的查询类型的名称下。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="eb5f2-105">在 EF Core 3.0 中，概念已重命名为无键实体类型。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="eb5f2-106">除了常规实体类型外，EF Core 模型还可以包含_无键实体类型_，可用于对不包含键值的数据执行数据库查询。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="eb5f2-107">无键实体类型特征</span><span class="sxs-lookup"><span data-stu-id="eb5f2-107">Keyless entity types characteristics</span></span>

<span data-ttu-id="eb5f2-108">无键实体类型支持与常规实体类型相同的多个映射功能，如继承映射和导航属性。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-108">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="eb5f2-109">上关系存储，他们可以配置的目标数据库对象和列通过 fluent API 方法或数据注释。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-109">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="eb5f2-110">但是，它们不同于常规实体类型，因为它们：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-110">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="eb5f2-111">不能定义键。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-111">Cannot have a key defined.</span></span>
- <span data-ttu-id="eb5f2-112">永远不会对_DbContext_中的更改进行跟踪，因此不会在数据库中插入、更新或删除这些更改。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-112">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="eb5f2-113">永远不会由约定发现。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="eb5f2-114">仅支持导航映射功能的子集，具体如下：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-114">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="eb5f2-115">它们可能永远不会作为关系的主体端。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-115">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="eb5f2-116">它们可能没有到拥有的实体的导航</span><span class="sxs-lookup"><span data-stu-id="eb5f2-116">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="eb5f2-117">它们只能包含指向常规实体的引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-117">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="eb5f2-118">实体不能包含无键实体类型的导航属性。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-118">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="eb5f2-119">需要配置 `.HasNoKey()` 方法调用。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-119">Need to be configured with `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="eb5f2-120">可以映射到定义的_查询_。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-120">May be mapped to a _defining query_.</span></span> <span data-ttu-id="eb5f2-121">定义查询是在模型中声明的查询，它充当无键实体类型的数据源。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-121">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="eb5f2-122">使用方案</span><span class="sxs-lookup"><span data-stu-id="eb5f2-122">Usage scenarios</span></span>

<span data-ttu-id="eb5f2-123">无键实体类型的一些主要使用方案包括：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-123">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="eb5f2-124">作为[原始 SQL 查询](xref:core/querying/raw-sql)的返回类型。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-124">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="eb5f2-125">映射到不包含主键的数据库视图。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-125">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="eb5f2-126">映射到不具有定义的主键的表。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-126">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="eb5f2-127">映射到模型中定义的查询。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-127">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="eb5f2-128">映射到数据库对象</span><span class="sxs-lookup"><span data-stu-id="eb5f2-128">Mapping to database objects</span></span>

<span data-ttu-id="eb5f2-129">将无键实体类型映射到数据库对象是使用 `ToTable` 或 `ToView` Fluent API 实现的。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-129">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="eb5f2-130">从 EF Core 的角度来看，此方法中指定的数据库对象是一个_视图_，这意味着它将被视为只读查询源，并且不能作为更新、插入或删除操作的目标。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-130">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="eb5f2-131">但是，这并不意味着数据库对象实际上必须是数据库视图。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-131">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="eb5f2-132">它也可以是将被视为只读的数据库表。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-132">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="eb5f2-133">相反，对于常规实体类型，EF Core 假设在 `ToTable` 方法中指定的数据库对象可以视为_表_，这意味着它可用作查询源，但也可作为更新、删除和插入操作的目标。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-133">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="eb5f2-134">事实上，您可以在 `ToTable` 中指定数据库视图的名称，只要该视图配置为可在数据库上更新，一切都应正常运行。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-134">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="eb5f2-135">`ToView` 假设对象已存在于数据库中，并且不是由迁移创建的。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-135">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="eb5f2-136">示例</span><span class="sxs-lookup"><span data-stu-id="eb5f2-136">Example</span></span>

<span data-ttu-id="eb5f2-137">下面的示例演示如何使用无键实体类型来查询数据库视图。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-137">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="eb5f2-138">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-138">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="eb5f2-139">首先，我们定义一个简单的博客和文章模型：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-139">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="eb5f2-140">接下来，我们定义一个简单的数据库视图，这样就可以查询与每个博客帖子数：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-140">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="eb5f2-141">接下来，我们定义一个类来保存数据库视图的结果：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-141">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="eb5f2-142">接下来，使用 `HasNoKey` API 在_OnModelCreating_中配置无键实体类型。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-142">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="eb5f2-143">我们使用熟知的配置 API 来配置无键实体类型的映射：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-143">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="eb5f2-144">接下来，将 `DbContext` 配置为包括 `DbSet<T>`：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-144">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="eb5f2-145">最后，我们可以采用标准方式来查询数据库视图：</span><span class="sxs-lookup"><span data-stu-id="eb5f2-145">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="eb5f2-146">请注意，我们还定义了一个上下文级查询属性（DbSet）作为此类型的查询的根。</span><span class="sxs-lookup"><span data-stu-id="eb5f2-146">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
