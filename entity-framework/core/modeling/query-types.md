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
# <a name="query-types"></a><span data-ttu-id="02849-102">查询类型</span><span class="sxs-lookup"><span data-stu-id="02849-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="02849-103">此功能是 EF Core 2.1 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="02849-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="02849-104">EF Core 模型可以包含实体类型，除了_查询类型_，这可以用于执行针对未映射到实体类型的数据的数据库查询。</span><span class="sxs-lookup"><span data-stu-id="02849-104">In addition to entity types, an EF Core model can contain _query types_, which can be used to carry out database queries against data that isn't mapped to entity types.</span></span>

## <a name="compare-query-types-to-entity-types"></a><span data-ttu-id="02849-105">比较查询类型与实体类型</span><span class="sxs-lookup"><span data-stu-id="02849-105">Compare query types to entity types</span></span>

<span data-ttu-id="02849-106">查询类型它们与实体类型中的一样：</span><span class="sxs-lookup"><span data-stu-id="02849-106">Query types are like entity types in that they:</span></span>

- <span data-ttu-id="02849-107">可以添加到模型或者在`OnModelCreating`或通过"设置"属性上派生_DbContext_。</span><span class="sxs-lookup"><span data-stu-id="02849-107">Can be added to the model either in `OnModelCreating` or via a "set" property on a derived _DbContext_.</span></span>
- <span data-ttu-id="02849-108">支持许多相同的映射功能，如继承映射和导航属性。</span><span class="sxs-lookup"><span data-stu-id="02849-108">Support many of the same mapping capabilities, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="02849-109">上关系存储，他们可以配置的目标数据库对象和列通过 fluent API 方法或数据注释。</span><span class="sxs-lookup"><span data-stu-id="02849-109">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="02849-110">但是，它们是不同于实体中的类型：</span><span class="sxs-lookup"><span data-stu-id="02849-110">However, they are different from entity types in that they:</span></span>

- <span data-ttu-id="02849-111">不需要定义的键。</span><span class="sxs-lookup"><span data-stu-id="02849-111">Do not require a key to be defined.</span></span>
- <span data-ttu-id="02849-112">永远不会跟踪的更改上_DbContext_并因此永远不会插入、 更新或删除数据库。</span><span class="sxs-lookup"><span data-stu-id="02849-112">Are never tracked for changes on the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="02849-113">永远不会由约定发现。</span><span class="sxs-lookup"><span data-stu-id="02849-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="02849-114">仅支持一部分导航映射功能-具体而言：</span><span class="sxs-lookup"><span data-stu-id="02849-114">Only support a subset of navigation mapping capabilities - Specifically:</span></span>
  - <span data-ttu-id="02849-115">它们可能永远不会作为关系的主体端。</span><span class="sxs-lookup"><span data-stu-id="02849-115">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="02849-116">它们仅可包含指向的实体的引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="02849-116">They can only contain reference navigation properties pointing to entities.</span></span>
  - <span data-ttu-id="02849-117">实体不能包含查询类型的导航属性。</span><span class="sxs-lookup"><span data-stu-id="02849-117">Entities cannot contain navigation properties to query types.</span></span>
- <span data-ttu-id="02849-118">得到解决_ModelBuilder_使用`Query`方法而不是`Entity`方法。</span><span class="sxs-lookup"><span data-stu-id="02849-118">Are addressed on the _ModelBuilder_ using the `Query` method rather than the `Entity` method.</span></span>
- <span data-ttu-id="02849-119">在映射_DbContext_类型的属性通过`DbQuery<T>`而非 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="02849-119">Are mapped on the _DbContext_ through properties of type `DbQuery<T>` rather than `DbSet<T>`</span></span>
- <span data-ttu-id="02849-120">映射到使用的数据库对象`ToView`方法，而非`ToTable`。</span><span class="sxs-lookup"><span data-stu-id="02849-120">Are mapped to database objects using the `ToView` method, rather than `ToTable`.</span></span>
- <span data-ttu-id="02849-121">可以映射到_定义查询_-定义查询是可查询类型的数据源在模型中声明的第二次查询。</span><span class="sxs-lookup"><span data-stu-id="02849-121">May be mapped to a _defining query_ - A defining query is a secondary query declared in the model that acts a data source for a query type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="02849-122">使用方案</span><span class="sxs-lookup"><span data-stu-id="02849-122">Usage scenarios</span></span>

<span data-ttu-id="02849-123">下面是一些查询类型主要使用方案：</span><span class="sxs-lookup"><span data-stu-id="02849-123">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="02849-124">作为返回类型的即席`FromSql()`查询。</span><span class="sxs-lookup"><span data-stu-id="02849-124">Serving as the return type for ad hoc `FromSql()` queries.</span></span>
- <span data-ttu-id="02849-125">映射到数据库视图。</span><span class="sxs-lookup"><span data-stu-id="02849-125">Mapping to database views.</span></span>
- <span data-ttu-id="02849-126">映射到不具有定义的主键的表。</span><span class="sxs-lookup"><span data-stu-id="02849-126">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="02849-127">映射到模型中定义的查询。</span><span class="sxs-lookup"><span data-stu-id="02849-127">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="02849-128">映射到数据库对象</span><span class="sxs-lookup"><span data-stu-id="02849-128">Mapping to database objects</span></span>

<span data-ttu-id="02849-129">查询类型映射到的数据库对象通过实现`ToView`fluent API。</span><span class="sxs-lookup"><span data-stu-id="02849-129">Mapping a query type to a database object is achieved using the `ToView` fluent API.</span></span> <span data-ttu-id="02849-130">此方法中指定的数据库对象是从 EF Core 的角度来看，_视图_，这意味着它将被视为只读查询源和不能作为目标的更新、 插入或删除操作。</span><span class="sxs-lookup"><span data-stu-id="02849-130">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="02849-131">但是，这并不意味着该数据库对象是实际需要的数据库视图-它或者可以是以只读方式将被视为一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="02849-131">However, this does not mean that the database object is actually required to be a database view - It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="02849-132">相反，实体类型，EF Core 假定数据库对象中指定`ToTable`方法可以视为_表_、 意味着它可以用作查询源，但是还针对由更新、 删除和插入操作。</span><span class="sxs-lookup"><span data-stu-id="02849-132">Conversely, for entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="02849-133">事实上，可以指定数据库视图中的名称`ToTable`和所有内容应该能够正常工作，只要视图被配置为可在数据库上更新。</span><span class="sxs-lookup"><span data-stu-id="02849-133">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

## <a name="example"></a><span data-ttu-id="02849-134">示例</span><span class="sxs-lookup"><span data-stu-id="02849-134">Example</span></span>

<span data-ttu-id="02849-135">下面的示例演示如何使用查询类型来查询数据库视图。</span><span class="sxs-lookup"><span data-stu-id="02849-135">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="02849-136">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes)。</span><span class="sxs-lookup"><span data-stu-id="02849-136">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="02849-137">首先，我们定义一个简单的博客和文章模型：</span><span class="sxs-lookup"><span data-stu-id="02849-137">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="02849-138">接下来，我们定义一个简单的数据库视图，这样就可以查询与每个博客帖子数：</span><span class="sxs-lookup"><span data-stu-id="02849-138">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#View)]

<span data-ttu-id="02849-139">接下来，我们定义一个类来保存数据库视图的结果：</span><span class="sxs-lookup"><span data-stu-id="02849-139">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="02849-140">接下来，我们配置中的查询类型_OnModelCreating_使用`modelBuilder.Query<T>`API。</span><span class="sxs-lookup"><span data-stu-id="02849-140">Next, we configure the query type in _OnModelCreating_ using the `modelBuilder.Query<T>` API.</span></span>
<span data-ttu-id="02849-141">我们使用标准的 fluent 配置 Api 来配置查询类型的映射：</span><span class="sxs-lookup"><span data-stu-id="02849-141">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="02849-142">最后，我们可以采用标准方式来查询数据库视图：</span><span class="sxs-lookup"><span data-stu-id="02849-142">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="02849-143">请注意我们还定义了上下文级别查询属性 (DbQuery) 作为针对此类型的查询的根。</span><span class="sxs-lookup"><span data-stu-id="02849-143">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
