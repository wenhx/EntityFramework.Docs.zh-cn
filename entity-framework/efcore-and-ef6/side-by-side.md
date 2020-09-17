---
title: EF6 和 EF Core - 在同一应用程序中使用它们
description: 在同一应用程序中使用 Entity Framework Core 和 Entity Framework 6 的指导
author: ajcvickers
ms.date: 01/23/2019
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: a9a8ab0ec77acf0fb1d1b1408d5711a6c8fa6664
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073521"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>在同一个应用程序中使用 EF Core 和 EF6

通过安装这两个 NuGet 包，可在同一应用程序或库中使用 EF Core 和 EF6。

某些类型在 EF Core 和 EF6 中具有相同的名称，并且仅命名空间有所不同，这可能会使在同一代码文件中同时使用 EF Core 和 EF6 变得复杂。 可通过命名空间别名指令轻松消除多义性。 例如：

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

如果要迁移具有多个 EF 模型的现有应用程序，则可以将其中一些选择性地迁移到 EF Core，其余程序则继续使用 EF6。
