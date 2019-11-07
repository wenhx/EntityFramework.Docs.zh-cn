---
title: Azure Cosmos DB 提供程序-限制-EF Core
description: Entity Framework Core Azure Cosmos DB 提供程序的限制
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 2631526b152d6ddcacf25173c8d51e4e3cb24500
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655988"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="e5439-103">Azure Cosmos DB 提供程序限制 EF Core</span><span class="sxs-lookup"><span data-stu-id="e5439-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="e5439-104">Cosmos 提供程序有很多限制。</span><span class="sxs-lookup"><span data-stu-id="e5439-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="e5439-105">其中的许多限制是由基础 Cosmos 数据库引擎中的限制引起的，并不特定于 EF。</span><span class="sxs-lookup"><span data-stu-id="e5439-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="e5439-106">但大多数情况还[没有实现](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="e5439-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="e5439-107">临时限制</span><span class="sxs-lookup"><span data-stu-id="e5439-107">Temporary limitations</span></span>

- <span data-ttu-id="e5439-108">即使只有一个实体类型没有映射到容器，它仍具有鉴别器属性。</span><span class="sxs-lookup"><span data-stu-id="e5439-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="e5439-109">在某些情况下，具有分区键的实体类型不能正常工作</span><span class="sxs-lookup"><span data-stu-id="e5439-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="e5439-110">不支持 `Include` 调用</span><span class="sxs-lookup"><span data-stu-id="e5439-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="e5439-111">不支持 `Join` 调用</span><span class="sxs-lookup"><span data-stu-id="e5439-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="e5439-112">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="e5439-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="e5439-113">仅提供 async 方法</span><span class="sxs-lookup"><span data-stu-id="e5439-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="e5439-114">由于没有 EF Core 依赖的低级方法的同步版本，因此当前通过对返回的 `Task`调用 `.Wait()` 来实现相应的功能。</span><span class="sxs-lookup"><span data-stu-id="e5439-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="e5439-115">这意味着使用 `SaveChanges`或 `ToList` 等方法，而不是其异步对应项可能导致应用程序中出现死锁</span><span class="sxs-lookup"><span data-stu-id="e5439-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="e5439-116">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="e5439-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="e5439-117">你可以查看[Azure Cosmos DB 支持的功能](/azure/cosmos-db/modeling-data)的完整概述，与关系数据库相比，这些是最明显的区别：</span><span class="sxs-lookup"><span data-stu-id="e5439-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="e5439-118">不支持客户端启动的事务</span><span class="sxs-lookup"><span data-stu-id="e5439-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="e5439-119">一些跨分区查询要么不受支持，要么速度慢得多，具体取决于所涉及的运算符</span><span class="sxs-lookup"><span data-stu-id="e5439-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
