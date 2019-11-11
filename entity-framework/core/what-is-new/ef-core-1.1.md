---
title: EF Core 1.1 中的新增功能 - EF Core
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: d582712ed62443318f4b9e209511fb2a557d667e
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656014"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="033bc-102">EF Core 1.1 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="033bc-102">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="033bc-103">建模</span><span class="sxs-lookup"><span data-stu-id="033bc-103">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="033bc-104">字段映射</span><span class="sxs-lookup"><span data-stu-id="033bc-104">Field mapping</span></span>

<span data-ttu-id="033bc-105">允许配置属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="033bc-105">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="033bc-106">这对于只读属性或具有 Get/Set 方法（而不是属性）的数据很有用。</span><span class="sxs-lookup"><span data-stu-id="033bc-106">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="033bc-107">映射到 SQL Server 内存优化表</span><span class="sxs-lookup"><span data-stu-id="033bc-107">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="033bc-108">你可以指定实体映射到的表是内存优化表。</span><span class="sxs-lookup"><span data-stu-id="033bc-108">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="033bc-109">使用 EF Core 创建和维护基于模型的数据库时（使用迁移或 `Database.EnsureCreated()`），将为这些实体创建内存优化表。</span><span class="sxs-lookup"><span data-stu-id="033bc-109">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="033bc-110">Change tracking</span><span class="sxs-lookup"><span data-stu-id="033bc-110">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="033bc-111">来自 EF6 的其他更改跟踪 API</span><span class="sxs-lookup"><span data-stu-id="033bc-111">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="033bc-112">如 `Reload`、`GetModifiedProperties`、`GetDatabaseValues` 等。</span><span class="sxs-lookup"><span data-stu-id="033bc-112">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="033bc-113">查询</span><span class="sxs-lookup"><span data-stu-id="033bc-113">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="033bc-114">显式加载</span><span class="sxs-lookup"><span data-stu-id="033bc-114">Explicit Loading</span></span>

<span data-ttu-id="033bc-115">允许在先前从数据库加载的实体上触发导航属性填充。</span><span class="sxs-lookup"><span data-stu-id="033bc-115">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="033bc-116">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="033bc-116">DbSet.Find</span></span>

<span data-ttu-id="033bc-117">提供一种基于主键值提取实体的简单方法。</span><span class="sxs-lookup"><span data-stu-id="033bc-117">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="033bc-118">其他</span><span class="sxs-lookup"><span data-stu-id="033bc-118">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="033bc-119">连接复原</span><span class="sxs-lookup"><span data-stu-id="033bc-119">Connection resiliency</span></span>

<span data-ttu-id="033bc-120">自动重试失败的数据库命令。</span><span class="sxs-lookup"><span data-stu-id="033bc-120">Automatically retries failed database commands.</span></span> <span data-ttu-id="033bc-121">当连接到 SQL Azure 时（其中瞬间失败非常普遍）该操作非常有用。</span><span class="sxs-lookup"><span data-stu-id="033bc-121">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="033bc-122">简化的服务替换</span><span class="sxs-lookup"><span data-stu-id="033bc-122">Simplified service replacement</span></span>

<span data-ttu-id="033bc-123">替换 EF 使用的内部服务更加简单。</span><span class="sxs-lookup"><span data-stu-id="033bc-123">Makes it easier to replace internal services that EF uses.</span></span>
