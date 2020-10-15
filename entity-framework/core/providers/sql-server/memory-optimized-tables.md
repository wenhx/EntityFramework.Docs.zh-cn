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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core 数据库提供程序中支持 Memory-Optimized 表

[内存优化表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) 是 SQL Server 的一项功能，其中整个表都驻留在内存中。 表数据的另一个副本维护在磁盘上，但仅用于持续性目的。 在数据库恢复期间，内存优化的表中的数据只能从磁盘读取。 例如，在服务器重新启动后。

## <a name="configuring-a-memory-optimized-table"></a>配置内存优化表

你可以指定实体映射到的表是内存优化表。 当使用 EF Core 基于模型创建和维护数据库时 (使用 [迁移](xref:core/managing-schemas/migrations/index) 或 [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)) 时，将为这些实体创建内存优化表。

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
