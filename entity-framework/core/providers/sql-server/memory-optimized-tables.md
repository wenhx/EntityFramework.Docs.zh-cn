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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>内存优化表支持 SQL Server EF Core 数据库提供程序

[内存优化表](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)是 SQL Server 的一项功能，其中整个表都驻留在内存中。 表数据的第二个副本将保留在磁盘上，但仅用于持续性目的。 在数据库恢复期间，内存优化表中的数据仅从磁盘中读取。 例如，在服务器重新启动后。

## <a name="configuring-a-memory-optimized-table"></a>配置内存优化表

你可以指定实体映射到的表是内存优化表。 使用 EF Core 创建和维护基于模型的数据库时（使用迁移或 `Database.EnsureCreated()`），将为这些实体创建内存优化表。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
