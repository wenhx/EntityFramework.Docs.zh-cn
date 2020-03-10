---
title: Microsoft SQL Server 数据库提供程序 - EF Core
description: 数据库提供程序的文档，该提供程序允许将 Entity Framework Core 与 Microsoft SQL Server 一起使用
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: baae668a7ec255e35ab0e23e5c5ddfa47bda917e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413142"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="f4fb6-103">Microsoft SQL Server EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="f4fb6-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="f4fb6-104">此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 Azure SQL 数据库）一起使用。</span><span class="sxs-lookup"><span data-stu-id="f4fb6-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="f4fb6-105">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="f4fb6-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="f4fb6-106">安装</span><span class="sxs-lookup"><span data-stu-id="f4fb6-106">Install</span></span>

<span data-ttu-id="f4fb6-107">安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="f4fb6-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f4fb6-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f4fb6-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="f4fb6-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4fb6-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="f4fb6-110">自版本 3.0.0 起，提供程序引用 Microsoft.Data.SqlClient（以前的版本依赖于 SqlClient）。</span><span class="sxs-lookup"><span data-stu-id="f4fb6-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="f4fb6-111">如果项目直接依赖于 SqlClient，请确保它引用了 Microsoft.Data.SqlClient 包。</span><span class="sxs-lookup"><span data-stu-id="f4fb6-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="f4fb6-112">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="f4fb6-112">Supported Database Engines</span></span>

* <span data-ttu-id="f4fb6-113">Microsoft SQL Server（2012 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="f4fb6-113">Microsoft SQL Server (2012 onwards)</span></span>
