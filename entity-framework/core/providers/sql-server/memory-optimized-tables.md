---
title: Microsoft SQL Server 数据库提供程序-内存优化表-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 7383e74b3f83172f9b8e0eaf9bd09d4e187e87f8
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813494"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="5617f-102">内存优化表支持 SQL Server EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="5617f-102">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="5617f-103">[内存优化表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一项功能，其中整个表都驻留在内存中。</span><span class="sxs-lookup"><span data-stu-id="5617f-103">[Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="5617f-104">表数据的第二个副本将保留在磁盘上，但仅用于持续性目的。</span><span class="sxs-lookup"><span data-stu-id="5617f-104">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="5617f-105">在数据库恢复期间，内存优化表中的数据仅从磁盘中读取。</span><span class="sxs-lookup"><span data-stu-id="5617f-105">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="5617f-106">例如，在服务器重新启动后。</span><span class="sxs-lookup"><span data-stu-id="5617f-106">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="5617f-107">配置内存优化表</span><span class="sxs-lookup"><span data-stu-id="5617f-107">Configuring a memory-optimized table</span></span>

<span data-ttu-id="5617f-108">你可以指定实体映射到的表是内存优化表。</span><span class="sxs-lookup"><span data-stu-id="5617f-108">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="5617f-109">使用 EF Core 创建和维护基于模型的数据库时（使用迁移或 `Database.EnsureCreated()`），将为这些实体创建内存优化表。</span><span class="sxs-lookup"><span data-stu-id="5617f-109">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
