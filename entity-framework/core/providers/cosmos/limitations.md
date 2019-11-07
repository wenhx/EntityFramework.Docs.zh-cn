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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Azure Cosmos DB 提供程序限制 EF Core

Cosmos 提供程序有很多限制。 其中的许多限制是由基础 Cosmos 数据库引擎中的限制引起的，并不特定于 EF。 但大多数情况还[没有实现](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。

## <a name="temporary-limitations"></a>临时限制

- 即使只有一个实体类型没有映射到容器，它仍具有鉴别器属性。
- 在某些情况下，具有分区键的实体类型不能正常工作
- 不支持 `Include` 调用
- 不支持 `Join` 调用

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK 限制

- 仅提供 async 方法

> [!WARNING]
> 由于没有 EF Core 依赖的低级方法的同步版本，因此当前通过对返回的 `Task`调用 `.Wait()` 来实现相应的功能。 这意味着使用 `SaveChanges`或 `ToList` 等方法，而不是其异步对应项可能导致应用程序中出现死锁

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB 限制

你可以查看[Azure Cosmos DB 支持的功能](/azure/cosmos-db/modeling-data)的完整概述，与关系数据库相比，这些是最明显的区别：

- 不支持客户端启动的事务
- 一些跨分区查询要么不受支持，要么速度慢得多，具体取决于所涉及的运算符
