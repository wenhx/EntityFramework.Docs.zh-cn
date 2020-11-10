---
title: Azure Cosmos DB 提供程序-限制-EF Core
description: 与其他提供程序相比，Entity Framework Core Azure Cosmos DB 提供程序的限制
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430191"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="876af-103">Azure Cosmos DB 提供程序限制 EF Core</span><span class="sxs-lookup"><span data-stu-id="876af-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="876af-104">Cosmos 提供程序有很多限制。</span><span class="sxs-lookup"><span data-stu-id="876af-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="876af-105">其中的许多限制是由基础 Cosmos 数据库引擎中的限制引起的，并不特定于 EF。</span><span class="sxs-lookup"><span data-stu-id="876af-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="876af-106">但大多数情况还 [没有实现](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="876af-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="876af-107">临时限制</span><span class="sxs-lookup"><span data-stu-id="876af-107">Temporary limitations</span></span>

- <span data-ttu-id="876af-108">`Include` 不支持调用</span><span class="sxs-lookup"><span data-stu-id="876af-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="876af-109">`Join` 不支持调用</span><span class="sxs-lookup"><span data-stu-id="876af-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="876af-110">Azure Cosmos DB SDK 限制</span><span class="sxs-lookup"><span data-stu-id="876af-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="876af-111">仅提供 async 方法</span><span class="sxs-lookup"><span data-stu-id="876af-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="876af-112">由于没有 EF Core 依赖的低级方法的同步版本，因此当前通过对返回的调用来实现相应的功能 `.Wait()` `Task` 。</span><span class="sxs-lookup"><span data-stu-id="876af-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="876af-113">这意味着，使用或等方法（ `SaveChanges` `ToList` 而不是其异步对应项）可能会导致应用程序中出现死锁</span><span class="sxs-lookup"><span data-stu-id="876af-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="876af-114">Azure Cosmos DB 限制</span><span class="sxs-lookup"><span data-stu-id="876af-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="876af-115">你可以查看 [Azure Cosmos DB 支持的功能](/azure/cosmos-db/modeling-data)的完整概述，与关系数据库相比，这些是最明显的区别：</span><span class="sxs-lookup"><span data-stu-id="876af-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="876af-116">不支持客户端启动的事务</span><span class="sxs-lookup"><span data-stu-id="876af-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="876af-117">某些跨分区查询的速度较慢，具体取决于例如 (涉及的运算符 `Skip/Take` 或 `OFFSET LIMIT`) </span><span class="sxs-lookup"><span data-stu-id="876af-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
