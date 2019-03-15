---
title: EF Core 3.0 中的新增功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: b6774f615b04bf9579aac5dea217e7321631da0c
ms.sourcegitcommit: a709054b2bc7a8365201d71f59325891aacd315f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57829182"
---
# <a name="new-features-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="1b257-102">EF Core 3.0 中包含的新功能（目前处于预览状态）</span><span class="sxs-lookup"><span data-stu-id="1b257-102">New features included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1b257-103">请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。</span><span class="sxs-lookup"><span data-stu-id="1b257-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="1b257-104">以下列表包括为 EF Core 3.0 计划的主要新功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-104">The following list includes the major new features planned for EF Core 3.0.</span></span>
<span data-ttu-id="1b257-105">大多数这些功能都不包含在当前预览中，但随着我们在 RTM 方面取得进展，这些功能将可用。</span><span class="sxs-lookup"><span data-stu-id="1b257-105">Most of these features are not included in the current preview, but will become available as we make progress towards RTM.</span></span>

<span data-ttu-id="1b257-106">原因是，在发布之初，我们专注于实现计划中的[中断性变更](xref:core/what-is-new/ef-core-3.0/breaking-changes)。</span><span class="sxs-lookup"><span data-stu-id="1b257-106">The reason is that at the beginning of the release we are focusing on implementing planned [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes).</span></span>
<span data-ttu-id="1b257-107">其中许多中断性变更都是对 EF Core 的改进。</span><span class="sxs-lookup"><span data-stu-id="1b257-107">Many of these breaking changes are improvements to EF Core on their own.</span></span>
<span data-ttu-id="1b257-108">还需要进行许多其他变更来实现进一步改进。</span><span class="sxs-lookup"><span data-stu-id="1b257-108">Many others are required to unblock further improvements.</span></span> 

<span data-ttu-id="1b257-109">有关正在进行的 bug 修复和增强功能的完整列表，可查看[问题跟踪程序中的此查询](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="1b257-109">For a complete list of bug fixes and enhancements underway, you can see [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc).</span></span>

## <a name="linq-improvements"></a><span data-ttu-id="1b257-110">LINQ 的改进</span><span class="sxs-lookup"><span data-stu-id="1b257-110">LINQ improvements</span></span> 

[<span data-ttu-id="1b257-111">跟踪问题 #12795</span><span class="sxs-lookup"><span data-stu-id="1b257-111">Tracking Issue #12795</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

<span data-ttu-id="1b257-112">此功能的相关工作已经开始，但当前预览中不包含此功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-112">Work on this feature has started but it isn't included in the current preview.</span></span>

<span data-ttu-id="1b257-113">LINQ 可便于编写数据库查询，而无需离开所选的语言，同时还能利用丰富的类型信息来获取 IntelliSense 和编译时类型检查。</span><span class="sxs-lookup"><span data-stu-id="1b257-113">LINQ enables you to write database queries without leaving your language of choice, taking advantage of rich type information to get IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="1b257-114">不过，LINQ 也支持编写数量不限的复杂查询，而这对于 LINQ 提供程序来说，一直都是一项巨大挑战。</span><span class="sxs-lookup"><span data-stu-id="1b257-114">But LINQ also enables you to write an unlimited number of complicated queries, and that has always been a huge challenge for LINQ providers.</span></span>
<span data-ttu-id="1b257-115">在之前的几个 EF Core 版本中，我们找出了能够转换为 SQL 的查询的部分，并允许查询的其余部分在客户端的内存中执行，从而部分解决了这一问题。</span><span class="sxs-lookup"><span data-stu-id="1b257-115">In the first few versions of EF Core, we solved that in part by figuring out what portions of a query could be translated to SQL, and then by allowing the rest of the query to execute in memory on the client.</span></span>
<span data-ttu-id="1b257-116">在某些情况下，此客户端执行是可取的，但在其他许多情况下，这可能会导致低效的查询，直到应用程序被部署到生产时才被发现。</span><span class="sxs-lookup"><span data-stu-id="1b257-116">This client-side execution can be desirable in some situations, but in many other cases it can result in inefficient queries that may not be identified until an application is deployed to production.</span></span>
<span data-ttu-id="1b257-117">在 EF Core 3.0 中，我们计划对我们的 LINQ 实现的工作原理以及测试它的方式进行重大更改。</span><span class="sxs-lookup"><span data-stu-id="1b257-117">In EF Core 3.0, we're planning to make profound changes to how our LINQ implementation works, and how we test it.</span></span>
<span data-ttu-id="1b257-118">目标是使其更为可靠（例如，避免修补程序版本中的查询中断）、使其能够将更多表达式正确地转换为 SQL、使其在更多场景中生成更有效的查询以及防止无法检测到效率低下的查询。</span><span class="sxs-lookup"><span data-stu-id="1b257-118">The goals are to make it more robust (for example, to avoid breaking queries in patch releases), to enable translating more expressions correctly into SQL, to generate efficient queries in more cases, and to prevent inefficient queries from going undetected.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="1b257-119">Cosmos DB 支持</span><span class="sxs-lookup"><span data-stu-id="1b257-119">Cosmos DB support</span></span> 

[<span data-ttu-id="1b257-120">跟踪问题 #8443</span><span class="sxs-lookup"><span data-stu-id="1b257-120">Tracking Issue #8443</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/8443)

<span data-ttu-id="1b257-121">当前预览中包含此功能，但尚不完善。</span><span class="sxs-lookup"><span data-stu-id="1b257-121">This feature is included in the current preview, but isn't complete yet.</span></span> 

<span data-ttu-id="1b257-122">我们正在致力于开发适用于 EF Core 的 Cosmos DB 提供程序，以便开发人员能够熟悉 EF 编程模型，从而轻松地将 Azure Cosmos DB 定目标为应用程序数据库。</span><span class="sxs-lookup"><span data-stu-id="1b257-122">We're working on a Cosmos DB provider for EF Core, to enable developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span>
<span data-ttu-id="1b257-123">目标是利用 Cosmos DB 的一些优势，如全局分发、“始终开启”可用性、弹性可伸缩性和低延迟，甚至包括 .NET 开发人员可以更轻松地访问它。</span><span class="sxs-lookup"><span data-stu-id="1b257-123">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span>
<span data-ttu-id="1b257-124">此提供程序将针对 Cosmos DB 中的 SQL API 启用大部分 EF Core 功能，如自动更改跟踪、LINQ 和值转换。</span><span class="sxs-lookup"><span data-stu-id="1b257-124">The provider will enable most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>
<span data-ttu-id="1b257-125">我们在 EF Core 2.2 之前开始这一开发，并且[我们已发布了一些提供程序的预览版](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。</span><span class="sxs-lookup"><span data-stu-id="1b257-125">We started this effort before EF Core 2.2, and [we have made some preview versions of the provider available](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/).</span></span>
<span data-ttu-id="1b257-126">新的计划是与 EF Core 3.0 一起继续开发提供程序。</span><span class="sxs-lookup"><span data-stu-id="1b257-126">The new plan is to continue developing the provider alongside EF Core 3.0.</span></span> 

## <a name="c-80-support"></a><span data-ttu-id="1b257-127">C#8.0 支持</span><span class="sxs-lookup"><span data-stu-id="1b257-127">C# 8.0 support</span></span>

<span data-ttu-id="1b257-128">[跟踪问题 #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[跟踪问题 #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)</span><span class="sxs-lookup"><span data-stu-id="1b257-128">[Tracking Issue #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[Tracking Issue #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)</span></span>

<span data-ttu-id="1b257-129">此功能的相关工作已经开始，但当前预览中不包含此功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-129">Work on this feature has started but it isn't included in the current preview.</span></span>

<span data-ttu-id="1b257-130">我们希望客户利用 [C# 8.0 推出的新功能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)，如在使用 EF Core 的同时使用异步流（包括 `await foreach`）和可以为 null 的引用类型。</span><span class="sxs-lookup"><span data-stu-id="1b257-130">We want our customers to take advantage of some of the [new features coming in C# 8.0](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/) like async streams (including `await foreach`) and nullable reference types while using EF Core.</span></span>

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="1b257-131">数据库视图的反向工程</span><span class="sxs-lookup"><span data-stu-id="1b257-131">Reverse engineering of database views</span></span>

[<span data-ttu-id="1b257-132">跟踪问题 #1679</span><span class="sxs-lookup"><span data-stu-id="1b257-132">Tracking Issue #1679</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/1679)

<span data-ttu-id="1b257-133">此功能包含在当前预览中。</span><span class="sxs-lookup"><span data-stu-id="1b257-133">This feature isn't included in the current preview.</span></span>

<span data-ttu-id="1b257-134">在 EF Core 2.1 中引入并在 EF Core 3.0 中视为没有键的实体类型的[查询类型](xref:core/modeling/query-types)代表可以从数据库读取但无法更新的数据。</span><span class="sxs-lookup"><span data-stu-id="1b257-134">[Query types](xref:core/modeling/query-types), introduced in EF Core 2.1 and considered entity types without keys in EF Core 3.0, represent data that can be read from the database, but cannot be updated.</span></span>
<span data-ttu-id="1b257-135">在大多数情况下，这一特性使它们非常适合数据库视图，因此我们计划在执行数据库视图的反向工程时自动创建没有键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="1b257-135">This characteristic makes them an excellent fit for database views in most scenarios, so we plan to automate the creation of entity types without keys when reverse engineering database views.</span></span>

## <a name="property-bag-entities"></a><span data-ttu-id="1b257-136">属性包实体</span><span class="sxs-lookup"><span data-stu-id="1b257-136">Property bag entities</span></span> 

<span data-ttu-id="1b257-137">[跟踪问题 #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 和 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span><span class="sxs-lookup"><span data-stu-id="1b257-137">[Tracking Issue #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) and [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span></span>

<span data-ttu-id="1b257-138">此功能的相关工作已经开始，但当前预览中不包含此功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-138">Work on this feature has started but it isn't included in the current preview.</span></span> 

<span data-ttu-id="1b257-139">此功能可以启用在索引的属性（而非常规属性）中存储数据的实体，并能够使用同一 .NET 类的实例（可能像 `Dictionary<string, object>` 一样简单）来表示同一 EF Core 模型中的不同的实体类型。</span><span class="sxs-lookup"><span data-stu-id="1b257-139">This feature is about enabling entities that store data in indexed properties instead of regular properties, and also about being able to use instances of the same .NET class (potentially something as simple as a `Dictionary<string, object>`) to represent different entity types in the same EF Core model.</span></span>
<span data-ttu-id="1b257-140">此功能是支持多对多关系而无需联接实体的基石（[问题 #1368](https://github.com/aspnet/EntityFrameworkCore/issues/1368)），是针对 EF Core 提出改进要求最多的一项功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-140">This feature is a stepping stone to support many-to-many relationships without a join entity ([issue #1368](https://github.com/aspnet/EntityFrameworkCore/issues/1368)), which is one of the most requested improvements for EF Core.</span></span>

## <a name="ef-63-on-net-core"></a><span data-ttu-id="1b257-141">.NET Core 上的 EF 6.3</span><span class="sxs-lookup"><span data-stu-id="1b257-141">EF 6.3 on .NET Core</span></span> 

[<span data-ttu-id="1b257-142">跟踪问题 EF6#271</span><span class="sxs-lookup"><span data-stu-id="1b257-142">Tracking Issue EF6#271</span></span>](https://github.com/aspnet/EntityFramework6/issues/271)

<span data-ttu-id="1b257-143">此功能的相关工作已经开始，但当前预览中不包含此功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-143">Work on this feature has started but it isn't included in the current preview.</span></span> 

<span data-ttu-id="1b257-144">我们知道许多现有应用程序使用以前版本的 EF，而仅为了利用 .NET Core 将其移植到 EF Core 有时需要大量工作。</span><span class="sxs-lookup"><span data-stu-id="1b257-144">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can sometimes require a significant effort.</span></span>
<span data-ttu-id="1b257-145">为此，我们将对下一版本的 EF 6 进行调整，以在 .NET Core 3.0 上运行。</span><span class="sxs-lookup"><span data-stu-id="1b257-145">For that reason, we will be adapting the next version of EF 6 to run on .NET Core 3.0.</span></span>
<span data-ttu-id="1b257-146">我们这样做是为了在尽可能减少更改的情况下推动现有应用程序的移植。</span><span class="sxs-lookup"><span data-stu-id="1b257-146">We are doing this to facilitate porting existing applications with minimal changes.</span></span>
<span data-ttu-id="1b257-147">将存在一些限制。</span><span class="sxs-lookup"><span data-stu-id="1b257-147">There are going to be some limitations.</span></span> <span data-ttu-id="1b257-148">例如:</span><span class="sxs-lookup"><span data-stu-id="1b257-148">For example:</span></span>
- <span data-ttu-id="1b257-149">除了 .NET Core 上包含的 SQL Server 支持之外，它还需要新的提供程序才能与其他数据库一起使用</span><span class="sxs-lookup"><span data-stu-id="1b257-149">It will require new providers to work with other databases besides the included SQL Server support on .NET Core</span></span>
- <span data-ttu-id="1b257-150">将不启用 SQL Server 的空间支持</span><span class="sxs-lookup"><span data-stu-id="1b257-150">Spatial support with SQL Server won't be enabled</span></span>

<span data-ttu-id="1b257-151">另请注意，此时没有为 EF 6 计划新功能。</span><span class="sxs-lookup"><span data-stu-id="1b257-151">Note also that there are no new features planned for EF 6 at this point.</span></span>
