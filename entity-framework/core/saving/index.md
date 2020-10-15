---
title: 保存数据 - EF Core
description: 使用 Entity Framework Core 保存数据的概述
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 7a31f92aff20f4026b984065e53a2dcc77ed46e7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063538"
---
# <a name="saving-data"></a><span data-ttu-id="35b4b-103">保存数据</span><span class="sxs-lookup"><span data-stu-id="35b4b-103">Saving Data</span></span>

<span data-ttu-id="35b4b-104">每个上下文实例都有一个 `ChangeTracker`，它负责跟踪需要写入数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="35b4b-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="35b4b-105">更改实体类的实例时，这些更改会记录在 `ChangeTracker` 中，然后在调用 `SaveChanges` 时被写入数据库。</span><span class="sxs-lookup"><span data-stu-id="35b4b-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="35b4b-106">此数据库提供程序负责将更改转换为特定于数据库的操作（例如，关系数据库的 `INSERT`、`UPDATE` 和 `DELETE` 命令）。</span><span class="sxs-lookup"><span data-stu-id="35b4b-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
