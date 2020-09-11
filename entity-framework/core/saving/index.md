---
title: 保存数据 - EF Core
description: 使用 Entity Framework Core 保存数据的概述
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: 13e7c470e71f9cf9a1112aad18a8902e2235cef1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616312"
---
# <a name="saving-data"></a><span data-ttu-id="b6916-103">保存数据</span><span class="sxs-lookup"><span data-stu-id="b6916-103">Saving Data</span></span>

<span data-ttu-id="b6916-104">每个上下文实例都有一个 `ChangeTracker`，它负责跟踪需要写入数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="b6916-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="b6916-105">更改实体类的实例时，这些更改会记录在 `ChangeTracker` 中，然后在调用 `SaveChanges` 时被写入数据库。</span><span class="sxs-lookup"><span data-stu-id="b6916-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="b6916-106">此数据库提供程序负责将更改转换为特定于数据库的操作（例如，关系数据库的 `INSERT`、`UPDATE` 和 `DELETE` 命令）。</span><span class="sxs-lookup"><span data-stu-id="b6916-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
