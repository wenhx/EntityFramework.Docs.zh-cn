---
title: InMemory 数据库提供程序 - EF Core
description: 有关 Entity Framework Core InMemory 数据库提供程序的信息
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1857ebbfa0eded1572220825a5b0d75961bcf3dd
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064006"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="060f9-103">EF Core In-Memory 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="060f9-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="060f9-104">此数据库提供程序允许将 Entity Framework Core 和内存数据库一起使用。</span><span class="sxs-lookup"><span data-stu-id="060f9-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="060f9-105">内存中数据库对测试来说非常有用，尽管内存中模式下的 SQLite 提供程序可能是针对关系数据库的更合适的测试替代。</span><span class="sxs-lookup"><span data-stu-id="060f9-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="060f9-106">内存中数据库仅用于测试。</span><span class="sxs-lookup"><span data-stu-id="060f9-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="060f9-107">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="060f9-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="060f9-108">安装</span><span class="sxs-lookup"><span data-stu-id="060f9-108">Install</span></span>

<span data-ttu-id="060f9-109">安装 [Microsoft.EntityFrameworkCore.InMemory NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="060f9-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="060f9-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="060f9-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="060f9-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="060f9-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="060f9-112">入门</span><span class="sxs-lookup"><span data-stu-id="060f9-112">Get Started</span></span>

<span data-ttu-id="060f9-113">下列资源可帮助你开始使用此提供程序。</span><span class="sxs-lookup"><span data-stu-id="060f9-113">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="060f9-114">使用 InMemory 进行测试</span><span class="sxs-lookup"><span data-stu-id="060f9-114">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="060f9-115">UnicornStore 示例应用程序测试</span><span class="sxs-lookup"><span data-stu-id="060f9-115">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="060f9-116">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="060f9-116">Supported Database Engines</span></span>

<span data-ttu-id="060f9-117">进程内内存数据库，仅用于测试目的。</span><span class="sxs-lookup"><span data-stu-id="060f9-117">In-process memory database, designed for testing purposes only.</span></span>
