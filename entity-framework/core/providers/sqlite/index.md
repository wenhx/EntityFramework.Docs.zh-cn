---
title: SQLite 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e4cbdba46f901831892192a343db2920a5760042
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149267"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="211ad-102">SQLite EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="211ad-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="211ad-103">此数据库提供程序允许将 Entity Framework Core 与 SQLite 一起使用。</span><span class="sxs-lookup"><span data-stu-id="211ad-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="211ad-104">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="211ad-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="211ad-105">安装</span><span class="sxs-lookup"><span data-stu-id="211ad-105">Install</span></span>

<span data-ttu-id="211ad-106">安装 [Microsoft.EntityFrameworkCore.Sqlite NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="211ad-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="supported-database-engines"></a><span data-ttu-id="211ad-107">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="211ad-107">Supported Database Engines</span></span>

* <span data-ttu-id="211ad-108">SQLite（3.7 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="211ad-108">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="211ad-109">限制</span><span class="sxs-lookup"><span data-stu-id="211ad-109">Limitations</span></span>

<span data-ttu-id="211ad-110">有关 SQLite 提供程序的一些重要限制，请参阅 [SQLite 限制](limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="211ad-110">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
