---
title: Microsoft SQL Server 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: 70e7f3d4bc1f57f1b23d9b3e0bd6264236ddbd27
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149196"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="41356-102">Microsoft SQL Server EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="41356-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="41356-103">此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 SQL Azure）一起使用。</span><span class="sxs-lookup"><span data-stu-id="41356-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="41356-104">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="41356-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="41356-105">安装</span><span class="sxs-lookup"><span data-stu-id="41356-105">Install</span></span>

<span data-ttu-id="41356-106">安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="41356-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="supported-database-engines"></a><span data-ttu-id="41356-107">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="41356-107">Supported Database Engines</span></span>

* <span data-ttu-id="41356-108">Microsoft SQL Server（2012 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="41356-108">Microsoft SQL Server (2012 onwards)</span></span>
