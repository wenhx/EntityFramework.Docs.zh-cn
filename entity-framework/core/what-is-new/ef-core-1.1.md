---
title: EF Core 1.1 中的新增功能 - EF Core
description: Entity Framework Core 1.1 中的更改和改进
author: divega
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 5f81e8b25feba5cdf5ae5e84b1d3362912ab5b26
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072403"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="5ea1c-103">EF Core 1.1 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="5ea1c-103">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="5ea1c-104">建模</span><span class="sxs-lookup"><span data-stu-id="5ea1c-104">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="5ea1c-105">字段映射</span><span class="sxs-lookup"><span data-stu-id="5ea1c-105">Field mapping</span></span>

<span data-ttu-id="5ea1c-106">允许配置属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-106">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="5ea1c-107">这对于只读属性或具有 Get/Set 方法（而不是属性）的数据很有用。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-107">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="5ea1c-108">映射到 SQL Server 内存优化表</span><span class="sxs-lookup"><span data-stu-id="5ea1c-108">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="5ea1c-109">你可以指定实体映射到的表是内存优化表。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="5ea1c-110">使用 EF Core 创建和维护基于模型的数据库时（使用迁移或 `Database.EnsureCreated()`），将为这些实体创建内存优化表。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="5ea1c-111">更改跟踪</span><span class="sxs-lookup"><span data-stu-id="5ea1c-111">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="5ea1c-112">来自 EF6 的其他更改跟踪 API</span><span class="sxs-lookup"><span data-stu-id="5ea1c-112">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="5ea1c-113">如 `Reload`、`GetModifiedProperties`、`GetDatabaseValues` 等。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-113">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="5ea1c-114">查询</span><span class="sxs-lookup"><span data-stu-id="5ea1c-114">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="5ea1c-115">显式加载</span><span class="sxs-lookup"><span data-stu-id="5ea1c-115">Explicit Loading</span></span>

<span data-ttu-id="5ea1c-116">允许在先前从数据库加载的实体上触发导航属性填充。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-116">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="5ea1c-117">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="5ea1c-117">DbSet.Find</span></span>

<span data-ttu-id="5ea1c-118">提供一种基于主键值提取实体的简单方法。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-118">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="5ea1c-119">其他</span><span class="sxs-lookup"><span data-stu-id="5ea1c-119">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="5ea1c-120">连接复原</span><span class="sxs-lookup"><span data-stu-id="5ea1c-120">Connection resiliency</span></span>

<span data-ttu-id="5ea1c-121">自动重试失败的数据库命令。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-121">Automatically retries failed database commands.</span></span> <span data-ttu-id="5ea1c-122">当连接到 SQL Azure 时（其中瞬间失败非常普遍）该操作非常有用。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-122">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="5ea1c-123">简化的服务替换</span><span class="sxs-lookup"><span data-stu-id="5ea1c-123">Simplified service replacement</span></span>

<span data-ttu-id="5ea1c-124">替换 EF 使用的内部服务更加简单。</span><span class="sxs-lookup"><span data-stu-id="5ea1c-124">Makes it easier to replace internal services that EF uses.</span></span>
