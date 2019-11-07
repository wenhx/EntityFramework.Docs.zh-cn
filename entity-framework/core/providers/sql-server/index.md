---
title: Microsoft SQL Server 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: dd352b81da05fa8ea8970495f20947bd109edf65
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655897"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="57141-102">Microsoft SQL Server EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="57141-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="57141-103">此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 SQL Azure）一起使用。</span><span class="sxs-lookup"><span data-stu-id="57141-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="57141-104">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="57141-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="57141-105">安装</span><span class="sxs-lookup"><span data-stu-id="57141-105">Install</span></span>

<span data-ttu-id="57141-106">安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="57141-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

## <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="57141-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="57141-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="visual-studiotabvs"></a>[<span data-ttu-id="57141-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57141-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="57141-109">自版本 3.0.0 起，提供程序引用 Microsoft.Data.SqlClient（以前的版本依赖于 SqlClient）。</span><span class="sxs-lookup"><span data-stu-id="57141-109">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="57141-110">如果项目直接依赖于 SqlClient，请确保它引用了正确的包。</span><span class="sxs-lookup"><span data-stu-id="57141-110">If your project takes a direct dependency on SqlClient, make sure it references the correct package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="57141-111">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="57141-111">Supported Database Engines</span></span>

* <span data-ttu-id="57141-112">Microsoft SQL Server（2012 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="57141-112">Microsoft SQL Server (2012 onwards)</span></span>
