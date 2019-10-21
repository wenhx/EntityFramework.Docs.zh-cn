---
title: 跟踪与非跟踪查询 - EF Core
author: smitpatel
ms.date: 10/10/2019
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 66988f936ab75e17620398c8f21e4a32bbc950bd
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445948"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="d565b-102">跟踪与非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="d565b-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="d565b-103">跟踪行为决定了 Entity Framework Core 是否将有关实体实例的信息保留在其更改跟踪器中。</span><span class="sxs-lookup"><span data-stu-id="d565b-103">Tracking behavior controls if Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="d565b-104">如果已跟踪某个实体，则该实体中检测到的任何更改都会在 `SaveChanges()` 期间永久保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="d565b-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="d565b-105">EF Core 还将修复跟踪查询结果中的实体与更改跟踪器中的实体之间的导航属性。</span><span class="sxs-lookup"><span data-stu-id="d565b-105">EF Core will also fix up navigation properties between the entities in a tracking query result and the entities that are in the change tracker.</span></span>

> [!NOTE]
> <span data-ttu-id="d565b-106">从不跟踪[无键实体类型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="d565b-106">[Keyless entity types](xref:core/modeling/keyless-entity-types) are never tracked.</span></span> <span data-ttu-id="d565b-107">无论在何处提到实体类型，它都是指定义了键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="d565b-107">Wherever this article mentions entity types, it refers to entity types which have a key defined.</span></span>

> [!TIP]  
> <span data-ttu-id="d565b-108">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="d565b-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="d565b-109">跟踪查询</span><span class="sxs-lookup"><span data-stu-id="d565b-109">Tracking queries</span></span>

<span data-ttu-id="d565b-110">返回实体类型的查询是默认会被跟踪的。</span><span class="sxs-lookup"><span data-stu-id="d565b-110">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="d565b-111">这表示可以更改这些实体实例，然后通过 `SaveChanges()` 持久化这些更改。</span><span class="sxs-lookup"><span data-stu-id="d565b-111">Which means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span> <span data-ttu-id="d565b-112">在以下示例中，将检测到对博客评分所做的更改，并在 `SaveChanges()` 期间将这些更改持久化到数据库中。</span><span class="sxs-lookup"><span data-stu-id="d565b-112">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a><span data-ttu-id="d565b-113">非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="d565b-113">No-tracking queries</span></span>

<span data-ttu-id="d565b-114">在只读方案中使用结果时，非跟踪查询十分有用。</span><span class="sxs-lookup"><span data-stu-id="d565b-114">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="d565b-115">可以更快速地执行非跟踪查询，因为无需设置更改跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="d565b-115">They're quicker to execute because there's no need to set up the change tracking information.</span></span> <span data-ttu-id="d565b-116">如果不需要更新从数据库中检索到的实体，则应使用非跟踪查询。</span><span class="sxs-lookup"><span data-stu-id="d565b-116">If you don't need to update the entities retrieved from the database, then a no-tracking query should be used.</span></span> <span data-ttu-id="d565b-117">可以将单个查询替换为非跟踪查询。</span><span class="sxs-lookup"><span data-stu-id="d565b-117">You can swap an individual query to be no-tracking.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

<span data-ttu-id="d565b-118">还可以在上下文实例级别更改默认跟踪行为：</span><span class="sxs-lookup"><span data-stu-id="d565b-118">You can also change the default tracking behavior at the context instance level:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a><span data-ttu-id="d565b-119">标识解析</span><span class="sxs-lookup"><span data-stu-id="d565b-119">Identity resolution</span></span>

<span data-ttu-id="d565b-120">由于跟踪查询使用更改跟踪器，因此 EF Core 将在跟踪查询中执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="d565b-120">Since a tracking query uses the change tracker, EF Core will do identity resolution in a tracking query.</span></span> <span data-ttu-id="d565b-121">当具体化实体时，如果 EF Core 已被跟踪，则会从更改跟踪器返回相同的实体实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-121">When materializing an entity, EF Core will return the same entity instance from the change tracker if it's already being tracked.</span></span> <span data-ttu-id="d565b-122">如果结果中多次包含相同的实体，则每次会返回相同的实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-122">If the result contains same entity multiple times, you get back same instance for each occurrence.</span></span> <span data-ttu-id="d565b-123">非跟踪查询不会使用更改跟踪器，也不会执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="d565b-123">No-tracking queries don't use the change tracker and don't do identity resolution.</span></span> <span data-ttu-id="d565b-124">因此会返回实体的新实例，即使结果中多次包含相同的实体也是如此。</span><span class="sxs-lookup"><span data-stu-id="d565b-124">So you get back new instance of entity even when the same entity is contained in the result multiple times.</span></span> <span data-ttu-id="d565b-125">此行为与 EF Core 3.0 之前的版本中的行为有所不同，请参阅[早期版本](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="d565b-125">This behavior was different in versions before EF Core 3.0, see [previous versions](#previous-versions).</span></span>

## <a name="tracking-and-custom-projections"></a><span data-ttu-id="d565b-126">跟踪和自定义投影</span><span class="sxs-lookup"><span data-stu-id="d565b-126">Tracking and custom projections</span></span>

<span data-ttu-id="d565b-127">即使查询的结果类型不是实体类型，默认情况下 EF Core 也会跟踪结果中包含的实体类型。</span><span class="sxs-lookup"><span data-stu-id="d565b-127">Even if the result type of the query isn't an entity type, EF Core will still track entity types contained in the result by default.</span></span> <span data-ttu-id="d565b-128">在以下返回匿名类型的查询中，结果集中的 `Blog` 实例会被跟踪。</span><span class="sxs-lookup"><span data-stu-id="d565b-128">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

<span data-ttu-id="d565b-129">如果结果集包含来自 LINQ 组合的实体类型，EF Core 将跟踪它们。</span><span class="sxs-lookup"><span data-stu-id="d565b-129">If the result set contains entity types coming out from LINQ composition, EF Core will track them.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

<span data-ttu-id="d565b-130">如果结果集不包含任何实体类型，则不会执行跟踪。</span><span class="sxs-lookup"><span data-stu-id="d565b-130">If the result set doesn't contain any entity types, then no tracking is done.</span></span> <span data-ttu-id="d565b-131">在以下查询中，我们返回匿名类型（具有实体中的某些值，但没有实际实体类型的实例）。</span><span class="sxs-lookup"><span data-stu-id="d565b-131">In the following query, we return an anonymous type with some of the values from the entity (but no instances of the actual entity type).</span></span> <span data-ttu-id="d565b-132">查询中没有任何被跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="d565b-132">There are no tracked entities coming out of the query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 <span data-ttu-id="d565b-133">EF Core 支持执行顶级投影中的客户端评估。</span><span class="sxs-lookup"><span data-stu-id="d565b-133">EF Core supports doing client evaluation in the top-level projection.</span></span> <span data-ttu-id="d565b-134">如果 EF Core 具体化实体实例以进行客户端评估，则会跟踪该实体实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-134">If EF Core materializes an entity instance for client evaluation, it will be tracked.</span></span> <span data-ttu-id="d565b-135">此处，由于我们要将 `blog` 实体传递到客户端方法 `StandardizeURL`，因此 EF Core 也会跟踪博客实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-135">Here, since we're passing `blog` entities to the client method `StandardizeURL`, EF Core will track the blog instances too.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

<span data-ttu-id="d565b-136">EF Core 不会跟踪结果中包含的无键实体实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-136">EF Core doesn't track the keyless entity instances contained in the result.</span></span> <span data-ttu-id="d565b-137">但 EF Core 会根据上述规则跟踪带有键的实体类型的所有其他实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-137">But EF Core tracks all the other instances of entity types with key according to rules above.</span></span>

<span data-ttu-id="d565b-138">在 EF Core 3.0 之前，某些上述规则的工作方式有所不同。</span><span class="sxs-lookup"><span data-stu-id="d565b-138">Some of the above rules worked differently before EF Core 3.0.</span></span> <span data-ttu-id="d565b-139">有关详细信息，请参阅[早期版本](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="d565b-139">For more information, see [previous versions](#previous-versions).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="d565b-140">早期版本</span><span class="sxs-lookup"><span data-stu-id="d565b-140">Previous versions</span></span>

<span data-ttu-id="d565b-141">在 3.0 版之前，EF Core 执行跟踪的方式有一些差异。</span><span class="sxs-lookup"><span data-stu-id="d565b-141">Before version 3.0, EF Core had some differences in how tracking was done.</span></span> <span data-ttu-id="d565b-142">显著的差异如下：</span><span class="sxs-lookup"><span data-stu-id="d565b-142">Notable differences are as follows:</span></span>

- <span data-ttu-id="d565b-143">如[客户端与服务器评估](xref:core/querying/client-eval)页中所述，在 3.0 版之前，EF Core 支持在查询的任何部分中执行客户端评估。</span><span class="sxs-lookup"><span data-stu-id="d565b-143">As explained in [Client vs Server Evaluation](xref:core/querying/client-eval) page, EF Core supported client evaluation in any part of the query before version 3.0.</span></span> <span data-ttu-id="d565b-144">客户端评估导致了实体的具体化，这不是结果的一部分。</span><span class="sxs-lookup"><span data-stu-id="d565b-144">Client evaluation caused materialization of entities, which weren't part of the result.</span></span> <span data-ttu-id="d565b-145">因此 EF Core 分析了结果以检测要跟踪的内容。此设计有一些不同之处，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d565b-145">So EF Core analyzed the result to detect what to track. This design had certain differences as follows:</span></span>
  - <span data-ttu-id="d565b-146">投影中的客户端评估（导致具体化，但未返回具体化的实体实例）未被跟踪。</span><span class="sxs-lookup"><span data-stu-id="d565b-146">Client evaluation in the projection, which caused materialization but didn't return the materialized entity instance wasn't tracked.</span></span> <span data-ttu-id="d565b-147">以下示例未跟踪 `blog` 实体。</span><span class="sxs-lookup"><span data-stu-id="d565b-147">The following example didn't track `blog` entities.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - <span data-ttu-id="d565b-148">在某些情况下，EF Core 未跟踪来自 LINQ 组合的对象。</span><span class="sxs-lookup"><span data-stu-id="d565b-148">EF Core didn't track the objects coming out of LINQ composition in certain cases.</span></span> <span data-ttu-id="d565b-149">以下示例未跟踪 `Post`。</span><span class="sxs-lookup"><span data-stu-id="d565b-149">The following example didn't track `Post`.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- <span data-ttu-id="d565b-150">只要查询结果中包含无键实体类型，整个查询就会进行非跟踪。</span><span class="sxs-lookup"><span data-stu-id="d565b-150">Whenever query results contained keyless entity types, the whole query was made non-tracking.</span></span> <span data-ttu-id="d565b-151">这表示不会跟踪结果中包含的带有键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="d565b-151">That means that entity types with keys, which are in result weren't being tracked either.</span></span>
- <span data-ttu-id="d565b-152">EF Core 在非跟踪查询中执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="d565b-152">EF Core did identity resolution in no-tracking query.</span></span> <span data-ttu-id="d565b-153">它使用了弱引用来跟踪已返回的实体。</span><span class="sxs-lookup"><span data-stu-id="d565b-153">It used weak references to keep track of entities that had already been returned.</span></span> <span data-ttu-id="d565b-154">因此，如果结果集多次包含相同的实体，则每次会返回相同的实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-154">So if a result set contained the same entity multiples times, you would get the same instance for each occurrence.</span></span> <span data-ttu-id="d565b-155">尽管具有相同标识的上一个结果超出了范围并进行了垃圾回收，EF Core 也会返回新实例。</span><span class="sxs-lookup"><span data-stu-id="d565b-155">Though if a previous result with the same identity went out of scope and got garbage collected, EF Core returned a new instance.</span></span>
