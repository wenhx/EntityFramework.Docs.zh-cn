---
title: 保存数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: c610ea2a9138482f93d2d54c9085ef827af276c8
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413092"
---
# <a name="saving-data"></a><span data-ttu-id="71610-102">保存数据</span><span class="sxs-lookup"><span data-stu-id="71610-102">Saving Data</span></span>

<span data-ttu-id="71610-103">每个上下文实例都有一个 `ChangeTracker`，它负责跟踪需要写入数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="71610-103">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="71610-104">更改实体类的实例时，这些更改会记录在 `ChangeTracker` 中，然后在调用 `SaveChanges` 时被写入数据库。</span><span class="sxs-lookup"><span data-stu-id="71610-104">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="71610-105">此数据库提供程序负责将更改转换为特定于数据库的操作（例如，关系数据库的 `INSERT`、`UPDATE` 和 `DELETE` 命令）。</span><span class="sxs-lookup"><span data-stu-id="71610-105">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
