---
title: Microsoft SQL Server 数据库提供程序 - EF Core
description: 数据库提供程序的文档，该提供程序允许将 Entity Framework Core 与 Microsoft SQL Server 一起使用
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 4118cd5737ece1ad084bb85d409523d217065353
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430314"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="5015b-103">Microsoft SQL Server EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="5015b-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="5015b-104">此数据库提供程序允许将 Entity Framework Core 与 Microsoft SQL Server（包括 Azure SQL 数据库）一起使用。</span><span class="sxs-lookup"><span data-stu-id="5015b-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="5015b-105">该提供程序作为 [Entity Framework Core 项目](https://github.com/dotnet/efcore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="5015b-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="5015b-106">安装</span><span class="sxs-lookup"><span data-stu-id="5015b-106">Install</span></span>

<span data-ttu-id="5015b-107">安装 [Microsoft.EntityFrameworkCore.SqlServer NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="5015b-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="5015b-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5015b-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="5015b-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5015b-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="5015b-110">自版本 3.0.0 起，提供程序引用 Microsoft.Data.SqlClient（以前的版本依赖于 SqlClient）。</span><span class="sxs-lookup"><span data-stu-id="5015b-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="5015b-111">如果项目直接依赖于 SqlClient，请确保它引用了 Microsoft.Data.SqlClient 包。</span><span class="sxs-lookup"><span data-stu-id="5015b-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="5015b-112">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="5015b-112">Supported Database Engines</span></span>

* <span data-ttu-id="5015b-113">Microsoft SQL Server（2012 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="5015b-113">Microsoft SQL Server (2012 onwards)</span></span>
