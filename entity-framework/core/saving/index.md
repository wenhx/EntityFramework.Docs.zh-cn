---
title: 保存数据 - EF Core
description: 使用 Entity Framework Core 保存数据的概述
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 98a23fd4956a8646e9760f9ecb0b48166ad6caa1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072468"
---
# <a name="saving-data"></a><span data-ttu-id="5284e-103">保存数据</span><span class="sxs-lookup"><span data-stu-id="5284e-103">Saving Data</span></span>

<span data-ttu-id="5284e-104">每个上下文实例都有一个 `ChangeTracker`，它负责跟踪需要写入数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="5284e-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="5284e-105">更改实体类的实例时，这些更改会记录在 `ChangeTracker` 中，然后在调用 `SaveChanges` 时被写入数据库。</span><span class="sxs-lookup"><span data-stu-id="5284e-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="5284e-106">此数据库提供程序负责将更改转换为特定于数据库的操作（例如，关系数据库的 `INSERT`、`UPDATE` 和 `DELETE` 命令）。</span><span class="sxs-lookup"><span data-stu-id="5284e-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
