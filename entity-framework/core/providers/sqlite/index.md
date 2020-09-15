---
title: SQLite 数据库提供程序 - EF Core
description: 有关 Entity Framework Core SQLite 数据库提供程序的信息
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: ba537acdf537fa475378c08b8c6290930b29239d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071233"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="a1cda-103">SQLite EF Core 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="a1cda-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="a1cda-104">此数据库提供程序允许将 Entity Framework Core 与 SQLite 一起使用。</span><span class="sxs-lookup"><span data-stu-id="a1cda-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="a1cda-105">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="a1cda-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="a1cda-106">安装</span><span class="sxs-lookup"><span data-stu-id="a1cda-106">Install</span></span>

<span data-ttu-id="a1cda-107">安装 [Microsoft.EntityFrameworkCore.Sqlite NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="a1cda-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="a1cda-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1cda-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="a1cda-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1cda-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="a1cda-110">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="a1cda-110">Supported Database Engines</span></span>

* <span data-ttu-id="a1cda-111">SQLite（3.7 及以上版本）</span><span class="sxs-lookup"><span data-stu-id="a1cda-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="a1cda-112">限制</span><span class="sxs-lookup"><span data-stu-id="a1cda-112">Limitations</span></span>

<span data-ttu-id="a1cda-113">有关 SQLite 提供程序的一些重要限制，请参阅 [SQLite 限制](xref:core/providers/sqlite/limitations)。</span><span class="sxs-lookup"><span data-stu-id="a1cda-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
