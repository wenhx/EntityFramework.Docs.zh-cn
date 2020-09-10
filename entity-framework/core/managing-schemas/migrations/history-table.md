---
title: 自定义迁移历史记录表-EF Core
description: 自定义历史记录表以用于与 Entity Framework Core 迁移
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: deed86b2023557036eb14ca5a66f04ab5bb4139f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617976"
---
# <a name="custom-migrations-history-table"></a>自定义迁移历史记录表

默认情况下，EF Core 通过在名为的表中记录哪些迁移已应用于数据库 `__EFMigrationsHistory` 。 由于各种原因，你可能需要自定义此表以更好地满足你的需求。

> [!IMPORTANT]
> 如果在应用迁移 *后* 自定义迁移历史记录表，则需要负责更新数据库中的现有表。

## <a name="schema-and-table-name"></a>架构和表名称

您可以使用 `MigrationsHistoryTable()` (中的方法 `OnConfiguring()` 或 `ConfigureServices()` ASP.NET Core) 上的方法更改架构和表名。 下面是一个示例，说明如何使用 SQL Server EF Core 提供程序。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>其他更改

若要配置表的其他方面，请重写并替换特定于提供程序的 `IHistoryRepository` 服务。 下面是将 MigrationId 列名称更改为 SQL Server 上的 *Id* 的示例。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` 位于内部命名空间内部，并且在将来的版本中可能会更改。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
