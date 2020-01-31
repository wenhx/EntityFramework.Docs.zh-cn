---
title: EF6 和 EF Core - 在同一应用程序中使用它们
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: bcf0a26535c4ec880a9ac25478c987fb683f6d26
ms.sourcegitcommit: b3cf5d2e3cb170b9916795d1d8c88678269639b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2020
ms.locfileid: "76888130"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="c76b8-102">在同一个应用程序中使用 EF Core 和 EF6</span><span class="sxs-lookup"><span data-stu-id="c76b8-102">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="c76b8-103">通过安装这两个 NuGet 包，可在同一应用程序或库中使用 EF Core 和 EF6。</span><span class="sxs-lookup"><span data-stu-id="c76b8-103">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="c76b8-104">某些类型在 EF Core 和 EF6 中具有相同的名称，并且仅命名空间有所不同，这可能会使在同一代码文件中同时使用 EF Core 和 EF6 变得复杂。</span><span class="sxs-lookup"><span data-stu-id="c76b8-104">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="c76b8-105">可通过命名空间别名指令轻松消除多义性。</span><span class="sxs-lookup"><span data-stu-id="c76b8-105">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="c76b8-106">例如：</span><span class="sxs-lookup"><span data-stu-id="c76b8-106">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="c76b8-107">如果要迁移具有多个 EF 模型的现有应用程序，则可以将其中一些选择性地迁移到 EF Core，其余程序则继续使用 EF6。</span><span class="sxs-lookup"><span data-stu-id="c76b8-107">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
