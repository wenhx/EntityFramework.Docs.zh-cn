---
title: DbContext 池
description: Entity Framework Core 中的 DbContext 池
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: 8638c838511be85bd994751b9911b107974dfe2f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061952"
---
# <a name="dbcontext-pooling"></a><span data-ttu-id="760f6-103">DbContext 池</span><span class="sxs-lookup"><span data-stu-id="760f6-103">DbContext pooling</span></span>

<span data-ttu-id="760f6-104">`AddDbContextPool` 启用实例的池 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="760f6-104">`AddDbContextPool` enables pooling of `DbContext` instances.</span></span> <span data-ttu-id="760f6-105">上下文池可以通过重复使用上下文实例，而不是为每个请求创建新实例，从而提高大规模方案（如 web 服务器）的吞吐量。</span><span class="sxs-lookup"><span data-stu-id="760f6-105">Context pooling can increase throughput in high-scale scenarios such as web servers by reusing context instances, rather than creating new instances for each request.</span></span>

<span data-ttu-id="760f6-106">使用 EF Core 的 ASP.NET Core 应用程序中的典型模式涉及将自定义 <xref:Microsoft.EntityFrameworkCore.DbContext> 类型注册到 [依赖关系注入](/aspnet/core/fundamentals/dependency-injection) 容器，并通过控制器或 Razor Pages 中的构造函数参数获取该类型的实例。</span><span class="sxs-lookup"><span data-stu-id="760f6-106">The typical pattern in an ASP.NET Core app using EF Core involves registering a custom <xref:Microsoft.EntityFrameworkCore.DbContext> type into the [dependency injection](/aspnet/core/fundamentals/dependency-injection) container and obtaining instances of that type through constructor parameters in controllers or Razor Pages.</span></span> <span data-ttu-id="760f6-107">使用构造函数注入，将为每个请求创建一个新的上下文实例。</span><span class="sxs-lookup"><span data-stu-id="760f6-107">Using constructor injection, a new context instance is created for each request.</span></span>

<span data-ttu-id="760f6-108"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 启用可重用上下文实例的池。</span><span class="sxs-lookup"><span data-stu-id="760f6-108"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> enables a pool of reusable context instances.</span></span> <span data-ttu-id="760f6-109">若要使用上下文池，请使用 `AddDbContextPool` 方法，而不是 `AddDbContext` 在服务注册期间使用：</span><span class="sxs-lookup"><span data-stu-id="760f6-109">To use context pooling, use the `AddDbContextPool` method instead of `AddDbContext` during service registration:</span></span>

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="760f6-110">`AddDbContextPool`使用时，在请求上下文实例时，EF 首先检查池中是否有可用的实例。</span><span class="sxs-lookup"><span data-stu-id="760f6-110">When `AddDbContextPool` is used, at the time a context instance is requested, EF first checks if there is an instance available in the pool.</span></span> <span data-ttu-id="760f6-111">请求处理完成后，实例的任何状态都将被重置，并且实例本身会返回池中。</span><span class="sxs-lookup"><span data-stu-id="760f6-111">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="760f6-112">从概念上讲，这类似于连接池在 ADO.NET 提供程序中的工作方式，并且具有保存上下文实例的某些初始化开销的优点。</span><span class="sxs-lookup"><span data-stu-id="760f6-112">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of the context instance.</span></span>

<span data-ttu-id="760f6-113">的 `poolSize` 参数 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 设置池保留的最大实例数。</span><span class="sxs-lookup"><span data-stu-id="760f6-113">The `poolSize` parameter of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> sets the maximum number of instances retained by the pool.</span></span> <span data-ttu-id="760f6-114">一旦 `poolSize` 超出，就不会缓存新的上下文实例，EF 会回退到按需创建实例的非池行为。</span><span class="sxs-lookup"><span data-stu-id="760f6-114">Once `poolSize` is exceeded, new context instances are not cached and  EF falls back to the non-pooling behavior of creating instances on demand.</span></span>

## <a name="limitations"></a><span data-ttu-id="760f6-115">限制</span><span class="sxs-lookup"><span data-stu-id="760f6-115">Limitations</span></span>

<span data-ttu-id="760f6-116">应该对应用进行分析和测试，以表明上下文初始化非常重要。</span><span class="sxs-lookup"><span data-stu-id="760f6-116">Apps should be profiled and tested to show that context initialization is a significant cost.</span></span>

<span data-ttu-id="760f6-117">`AddDbContextPool` 对可在上下文的方法中完成的操作有一些限制 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="760f6-117">`AddDbContextPool` has a few limitations on what can be done in the `OnConfiguring` method of the context.</span></span>

> [!WARNING]
> <span data-ttu-id="760f6-118">避免在维护状态的应用程序中使用上下文池。</span><span class="sxs-lookup"><span data-stu-id="760f6-118">Avoid using context pooling in apps that maintain state.</span></span> <span data-ttu-id="760f6-119">例如，不应在请求之间共享的上下文中的私有字段。</span><span class="sxs-lookup"><span data-stu-id="760f6-119">For example, private fields in the context that shouldn't be shared across requests.</span></span> <span data-ttu-id="760f6-120">在将上下文实例添加到池中之前，EF Core 仅重置它知道的状态。</span><span class="sxs-lookup"><span data-stu-id="760f6-120">EF Core only resets the state that it is aware of before adding a context instance to the pool.</span></span>

<span data-ttu-id="760f6-121">上下文池的工作方式是跨请求重复使用同一上下文实例。</span><span class="sxs-lookup"><span data-stu-id="760f6-121">Context pooling works by reusing the same context instance across requests.</span></span> <span data-ttu-id="760f6-122">这意味着，它可以有效地根据实例本身注册为 [单一](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 实例，以便能够持久保存。</span><span class="sxs-lookup"><span data-stu-id="760f6-122">This means that it's effectively registered as a [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in terms of the instance itself so that it's able to persist.</span></span>

<span data-ttu-id="760f6-123">上下文池适用于在请求之间修复了上下文配置（包括服务已解决）的情况。</span><span class="sxs-lookup"><span data-stu-id="760f6-123">Context pooling is intended for scenarios where the context configuration, which includes services resolved, is fixed between requests.</span></span> <span data-ttu-id="760f6-124">对于需要 [范围内](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 的服务或需要更改配置的情况，请不要使用 pooling。</span><span class="sxs-lookup"><span data-stu-id="760f6-124">For cases where [Scoped](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) services are required, or configuration needs to be changed, don't use pooling.</span></span> <span data-ttu-id="760f6-125">除高度优化的方案外，池的性能提升通常可以忽略不计。</span><span class="sxs-lookup"><span data-stu-id="760f6-125">The performance gain from pooling is usually negligible except in highly optimized scenarios.</span></span>
