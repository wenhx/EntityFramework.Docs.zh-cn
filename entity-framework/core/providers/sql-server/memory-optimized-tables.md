---
title: Microsoft SQL Server 数据库提供程序-内存优化表-EF Core
description: 如何将内存优化表与 SQL Server Entity Framework Core 数据库提供程序结合使用
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: a504fb3487aea6dd36abf204a7427095e3d29118
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414763"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>内存优化表支持 SQL Server EF Core 数据库提供程序

[内存优化表](/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一项功能，其中整个表都驻留在内存中。 表数据的另一个副本维护在磁盘上，但仅用于持续性目的。 在数据库恢复期间，内存优化的表中的数据只能从磁盘读取。 例如，在服务器重新启动后。

## <a name="configuring-a-memory-optimized-table"></a>配置内存优化表

你可以指定实体映射到的表是内存优化表。 使用 EF Core 创建和维护基于模型的数据库（使用[迁移](xref:core/managing-schemas/migrations/index)或[EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)）时，将为这些实体创建内存优化表。

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
