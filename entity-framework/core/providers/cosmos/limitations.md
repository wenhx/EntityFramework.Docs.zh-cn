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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Azure Cosmos DB 提供程序限制 EF Core

Cosmos 提供程序有很多限制。 其中的许多限制是由基础 Cosmos 数据库引擎中的限制引起的，并不特定于 EF。 但大多数情况还 [没有实现](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)。

## <a name="temporary-limitations"></a>临时限制

- `Include` 不支持调用
- `Join` 不支持调用

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK 限制

- 仅提供 async 方法

> [!WARNING]
> 由于没有 EF Core 依赖的低级方法的同步版本，因此当前通过对返回的调用来实现相应的功能 `.Wait()` `Task` 。 这意味着，使用或等方法（ `SaveChanges` `ToList` 而不是其异步对应项）可能会导致应用程序中出现死锁

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB 限制

你可以查看 [Azure Cosmos DB 支持的功能](/azure/cosmos-db/modeling-data)的完整概述，与关系数据库相比，这些是最明显的区别：

- 不支持客户端启动的事务
- 某些跨分区查询的速度较慢，具体取决于例如 (涉及的运算符 `Skip/Take` 或 `OFFSET LIMIT`) 
