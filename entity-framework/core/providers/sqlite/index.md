---
title: SQLite 数据库提供程序 - EF Core
description: 有关 Entity Framework Core SQLite 数据库提供程序的信息
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616561"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="dbe56-103">SQLite EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="dbe56-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="dbe56-104">此数据库提供程序允许将 Entity Framework Core 与 SQLite 一起使用。</span><span class="sxs-lookup"><span data-stu-id="dbe56-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="dbe56-105">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="dbe56-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="dbe56-106">安装</span><span class="sxs-lookup"><span data-stu-id="dbe56-106">Install</span></span>

<span data-ttu-id="dbe56-107">安装 [Microsoft.EntityFrameworkCore.Sqlite NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="dbe56-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="dbe56-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="dbe56-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="dbe56-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbe56-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="dbe56-110">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="dbe56-110">Supported Database Engines</span></span>

* <span data-ttu-id="dbe56-111">SQLite（3.7 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="dbe56-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="dbe56-112">限制</span><span class="sxs-lookup"><span data-stu-id="dbe56-112">Limitations</span></span>

<span data-ttu-id="dbe56-113">有关 SQLite 提供程序的一些重要限制，请参阅 [SQLite 限制](xref:core/providers/sqlite/limitations)。</span><span class="sxs-lookup"><span data-stu-id="dbe56-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
