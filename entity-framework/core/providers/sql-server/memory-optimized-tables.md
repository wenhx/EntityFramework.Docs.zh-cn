---
title: Microsoft SQL Server 数据库提供程序-Memory-Optimized 表-EF Core
description: 如何将 Memory-Optimized 表与 SQL Server Entity Framework Core 数据库提供程序一起使用
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 42b2a2ffafb2234e1ce7a6d0844234509a4b5b94
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063915"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="79b12-103">SQL Server EF Core 数据库提供程序中支持 Memory-Optimized 表</span><span class="sxs-lookup"><span data-stu-id="79b12-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="79b12-104">[内存优化表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) 是 SQL Server 的一项功能，其中整个表都驻留在内存中。</span><span class="sxs-lookup"><span data-stu-id="79b12-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="79b12-105">表数据的另一个副本维护在磁盘上，但仅用于持续性目的。</span><span class="sxs-lookup"><span data-stu-id="79b12-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="79b12-106">在数据库恢复期间，内存优化的表中的数据只能从磁盘读取。</span><span class="sxs-lookup"><span data-stu-id="79b12-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="79b12-107">例如，在服务器重新启动后。</span><span class="sxs-lookup"><span data-stu-id="79b12-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="79b12-108">配置内存优化表</span><span class="sxs-lookup"><span data-stu-id="79b12-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="79b12-109">你可以指定实体映射到的表是内存优化表。</span><span class="sxs-lookup"><span data-stu-id="79b12-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="79b12-110">当使用 EF Core 基于模型创建和维护数据库时 (使用 [迁移](xref:core/managing-schemas/migrations/index) 或 [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)) 时，将为这些实体创建内存优化表。</span><span class="sxs-lookup"><span data-stu-id="79b12-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
