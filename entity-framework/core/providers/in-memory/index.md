---
title: InMemory 数据库提供程序 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: fd31c8ef2dc2e35e69f9845933a5578a5ff84c9c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413002"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="8a480-102">EF Core In-Memory 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="8a480-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="8a480-103">此数据库提供程序允许将 Entity Framework Core 和内存数据库一起使用。</span><span class="sxs-lookup"><span data-stu-id="8a480-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="8a480-104">这对测试非常有用，尽管内存中模式下的 SQLite 提供程序可能是针对关系数据库的更合适的测试替代。</span><span class="sxs-lookup"><span data-stu-id="8a480-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="8a480-105">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="8a480-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="8a480-106">安装</span><span class="sxs-lookup"><span data-stu-id="8a480-106">Install</span></span>

<span data-ttu-id="8a480-107">安装 [Microsoft.EntityFrameworkCore.InMemory NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="8a480-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="8a480-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8a480-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="8a480-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a480-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="8a480-110">开始操作</span><span class="sxs-lookup"><span data-stu-id="8a480-110">Get Started</span></span>

<span data-ttu-id="8a480-111">下列资源可帮助你开始使用此提供程序。</span><span class="sxs-lookup"><span data-stu-id="8a480-111">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="8a480-112">使用 InMemory 进行测试</span><span class="sxs-lookup"><span data-stu-id="8a480-112">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)
* [<span data-ttu-id="8a480-113">UnicornStore 示例应用程序测试</span><span class="sxs-lookup"><span data-stu-id="8a480-113">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="8a480-114">支持的数据库引擎</span><span class="sxs-lookup"><span data-stu-id="8a480-114">Supported Database Engines</span></span>

<span data-ttu-id="8a480-115">进程内内存数据库（仅用于测试目的）</span><span class="sxs-lookup"><span data-stu-id="8a480-115">In-process memory database (designed for testing purposes only)</span></span>
