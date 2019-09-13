---
title: 跟踪与非跟踪查询 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: d93be5c2b727d8fbaddd103f8f367c699ae80a7c
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921652"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="9a4ff-102">跟踪与非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="9a4ff-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="9a4ff-103">跟踪行为可控制 Entity Framework Core 是否将有关实体实例的信息保留在其更改跟踪器中。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-103">Tracking behavior controls whether or not Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="9a4ff-104">如果已跟踪某个实体，则该实体中检测到的任何更改都会在 `SaveChanges()` 期间永久保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="9a4ff-105">Entity Framework Core 还会修正从跟踪查询中获取的实体与先前已加载到 DbContext 实例中的实体两者之间的导航属性。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-105">Entity Framework Core will also fix-up navigation properties between entities that are obtained from a tracking query and entities that were previously loaded into the DbContext instance.</span></span>

> [!TIP]  
> <span data-ttu-id="9a4ff-106">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="9a4ff-107">跟踪查询</span><span class="sxs-lookup"><span data-stu-id="9a4ff-107">Tracking queries</span></span>

<span data-ttu-id="9a4ff-108">默认情况下，跟踪返回实体类型的查询。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-108">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="9a4ff-109">这表示可以更改这些实体实例，然后通过 `SaveChanges()` 持久化这些更改。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-109">This means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span>

<span data-ttu-id="9a4ff-110">在以下示例中，将检测到对博客评分所做的更改，并在 `SaveChanges()` 期间将这些更改持久化到数据库中。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-110">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a><span data-ttu-id="9a4ff-111">非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="9a4ff-111">No-tracking queries</span></span>

<span data-ttu-id="9a4ff-112">在只读方案中使用结果时，非跟踪查询十分有用。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-112">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="9a4ff-113">可以更快速地执行非跟踪查询，因为无需设置更改跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-113">They are quicker to execute because there is no need to setup change tracking information.</span></span>

<span data-ttu-id="9a4ff-114">可以交换单个非跟踪查询：</span><span class="sxs-lookup"><span data-stu-id="9a4ff-114">You can swap an individual query to be no-tracking:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

<span data-ttu-id="9a4ff-115">还可以在上下文实例级别更改默认跟踪行为：</span><span class="sxs-lookup"><span data-stu-id="9a4ff-115">You can also change the default tracking behavior at the context instance level:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> <span data-ttu-id="9a4ff-116">非跟踪查询仍在查询过程中执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-116">No tracking queries still perform identity resolution within the executing query.</span></span> <span data-ttu-id="9a4ff-117">如果结果集多次包含相同的实体，则每次会在结果集中返回实体类的相同实例。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-117">If the result set contains the same entity multiple times, the same instance of the entity class will be returned for each occurrence in the result set.</span></span> <span data-ttu-id="9a4ff-118">但是，弱引用用于跟踪已返回的实体。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-118">However, weak references are used to keep track of entities that have already been returned.</span></span> <span data-ttu-id="9a4ff-119">如果具有相同标识的上一个结果超出范围，并运行垃圾回收，则可能会获得新的实体实例。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-119">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span> <span data-ttu-id="9a4ff-120">有关详细信息，请参阅[查询的工作原理](overview.md)。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-120">For more information, see [How Query Works](overview.md).</span></span>

## <a name="tracking-and-projections"></a><span data-ttu-id="9a4ff-121">跟踪和投影</span><span class="sxs-lookup"><span data-stu-id="9a4ff-121">Tracking and projections</span></span>

<span data-ttu-id="9a4ff-122">即使查询的结果类型不是实体类型，但如果结果包含实体类型，则默认情况下也会跟踪这些实体类型。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-122">Even if the result type of the query isn't an entity type, if the result contains entity types they will still be tracked by default.</span></span> <span data-ttu-id="9a4ff-123">在以下返回匿名类型的查询中，会跟踪结果集中 `Blog` 的实例。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-123">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs?highlight=7)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Blog = b,
                Posts = b.Posts.Count()
            });
}
```

<span data-ttu-id="9a4ff-124">如果结果集不包含任何实体类型，则不会执行跟踪。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-124">If the result set does not contain any entity types, then no tracking is performed.</span></span> <span data-ttu-id="9a4ff-125">在以下返回匿名类型（具有实体中的某些值，但没有实际实体类型的实例）的查询中，不会执行跟踪。</span><span class="sxs-lookup"><span data-stu-id="9a4ff-125">In the following query, which returns an anonymous type with some of the values from the entity (but no instances of the actual entity type), there is no tracking performed.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Id = b.BlogId,
                Url = b.Url
            });
}
```
