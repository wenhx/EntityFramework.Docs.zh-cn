---
title: SQLite 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e8c3d675322b163fdf1e2e7e01f3815e28f427a2
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502248"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="33d12-102">SQLite EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="33d12-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="33d12-103">此数据库提供程序允许将 Entity Framework Core 与 SQLite 一起使用。</span><span class="sxs-lookup"><span data-stu-id="33d12-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="33d12-104">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="33d12-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="33d12-105">安装</span><span class="sxs-lookup"><span data-stu-id="33d12-105">Install</span></span>

<span data-ttu-id="33d12-106">安装 [Microsoft.EntityFrameworkCore.Sqlite NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="33d12-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="33d12-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33d12-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studiotabvs"></a>[<span data-ttu-id="33d12-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d12-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="33d12-109">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="33d12-109">Supported Database Engines</span></span>

* <span data-ttu-id="33d12-110">SQLite（3.7 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="33d12-110">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="33d12-111">限制</span><span class="sxs-lookup"><span data-stu-id="33d12-111">Limitations</span></span>

<span data-ttu-id="33d12-112">有关 SQLite 提供程序的一些重要限制，请参阅 [SQLite 限制](limitations.md)。</span><span class="sxs-lookup"><span data-stu-id="33d12-112">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
