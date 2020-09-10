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
# <a name="custom-migrations-history-table"></a><span data-ttu-id="b77fc-103">自定义迁移历史记录表</span><span class="sxs-lookup"><span data-stu-id="b77fc-103">Custom Migrations History Table</span></span>

<span data-ttu-id="b77fc-104">默认情况下，EF Core 通过在名为的表中记录哪些迁移已应用于数据库 `__EFMigrationsHistory` 。</span><span class="sxs-lookup"><span data-stu-id="b77fc-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="b77fc-105">由于各种原因，你可能需要自定义此表以更好地满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="b77fc-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b77fc-106">如果在应用迁移 *后* 自定义迁移历史记录表，则需要负责更新数据库中的现有表。</span><span class="sxs-lookup"><span data-stu-id="b77fc-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="b77fc-107">架构和表名称</span><span class="sxs-lookup"><span data-stu-id="b77fc-107">Schema and table name</span></span>

<span data-ttu-id="b77fc-108">您可以使用 `MigrationsHistoryTable()` (中的方法 `OnConfiguring()` 或 `ConfigureServices()` ASP.NET Core) 上的方法更改架构和表名。</span><span class="sxs-lookup"><span data-stu-id="b77fc-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="b77fc-109">下面是一个示例，说明如何使用 SQL Server EF Core 提供程序。</span><span class="sxs-lookup"><span data-stu-id="b77fc-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="b77fc-110">其他更改</span><span class="sxs-lookup"><span data-stu-id="b77fc-110">Other changes</span></span>

<span data-ttu-id="b77fc-111">若要配置表的其他方面，请重写并替换特定于提供程序的 `IHistoryRepository` 服务。</span><span class="sxs-lookup"><span data-stu-id="b77fc-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="b77fc-112">下面是将 MigrationId 列名称更改为 SQL Server 上的 *Id* 的示例。</span><span class="sxs-lookup"><span data-stu-id="b77fc-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="b77fc-113">`SqlServerHistoryRepository` 位于内部命名空间内部，并且在将来的版本中可能会更改。</span><span class="sxs-lookup"><span data-stu-id="b77fc-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
