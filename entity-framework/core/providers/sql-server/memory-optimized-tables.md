---
title: Microsoft SQL Server 数据库提供程序-内存优化表-EF Core
description: 如何将内存优化表与 SQL Server Entity Framework Core 数据库提供程序结合使用
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: a504fb3487aea6dd36abf204a7427095e3d29118
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824646"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="d94a0-103">内存优化表支持 SQL Server EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="d94a0-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="d94a0-104">[内存优化表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一项功能，其中整个表都驻留在内存中。</span><span class="sxs-lookup"><span data-stu-id="d94a0-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="d94a0-105">表数据的另一个副本维护在磁盘上，但仅用于持续性目的。</span><span class="sxs-lookup"><span data-stu-id="d94a0-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="d94a0-106">在数据库恢复期间，内存优化的表中的数据只能从磁盘读取。</span><span class="sxs-lookup"><span data-stu-id="d94a0-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="d94a0-107">例如，在服务器重新启动后。</span><span class="sxs-lookup"><span data-stu-id="d94a0-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="d94a0-108">配置内存优化表</span><span class="sxs-lookup"><span data-stu-id="d94a0-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="d94a0-109">你可以指定实体映射到的表是内存优化表。</span><span class="sxs-lookup"><span data-stu-id="d94a0-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="d94a0-110">使用 EF Core 创建和维护基于模型的数据库（使用[迁移](xref:core/managing-schemas/migrations/index)或[EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)）时，将为这些实体创建内存优化表。</span><span class="sxs-lookup"><span data-stu-id="d94a0-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
