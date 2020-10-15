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
# <a name="saving-data"></a>保存数据

每个上下文实例都有一个 `ChangeTracker`，它负责跟踪需要写入数据库的更改。 更改实体类的实例时，这些更改会记录在 `ChangeTracker` 中，然后在调用 `SaveChanges` 时被写入数据库。 此数据库提供程序负责将更改转换为特定于数据库的操作（例如，关系数据库的 `INSERT`、`UPDATE` 和 `DELETE` 命令）。
