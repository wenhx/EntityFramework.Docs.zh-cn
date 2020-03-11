---
title: 使用 SQLite 的 EF Core测试
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: f7f847d8c766c0d4d7577ea6760ee72a17f84933
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414631"
---
# <a name="testing-with-sqlite"></a><span data-ttu-id="84023-102">使用 SQLite 进行测试</span><span class="sxs-lookup"><span data-stu-id="84023-102">Testing with SQLite</span></span>

<span data-ttu-id="84023-103">SQLite 具有内存中模式，借此可使用 SQLite 针对关系数据库编写测试，而不会产生实际数据库操作的开销。</span><span class="sxs-lookup"><span data-stu-id="84023-103">SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="84023-104">你可以在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)</span><span class="sxs-lookup"><span data-stu-id="84023-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="84023-105">示例测试方案</span><span class="sxs-lookup"><span data-stu-id="84023-105">Example testing scenario</span></span>

<span data-ttu-id="84023-106">请考虑以下允许应用程序代码执行一些与博客相关的操作的服务。</span><span class="sxs-lookup"><span data-stu-id="84023-106">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="84023-107">在内部，它使用连接到 SQL Server 数据库的 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="84023-107">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="84023-108">交换此上下文可有效连接到内存中 SQLite 数据库，这样我们就可以为该服务编写高效的测试，而无需修改代码或执行大量的工作来创建上下文的测试副本。</span><span class="sxs-lookup"><span data-stu-id="84023-108">It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="84023-109">准备您的上下文</span><span class="sxs-lookup"><span data-stu-id="84023-109">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="84023-110">避免配置两个数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="84023-110">Avoid configuring two database providers</span></span>

<span data-ttu-id="84023-111">在测试中，从外部配置上下文以使用 InMemory 提供程序。</span><span class="sxs-lookup"><span data-stu-id="84023-111">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="84023-112">如果通过在上下文中重写 `OnConfiguring` 来配置数据库提供程序，则需要添加一些条件代码，以确保仅配置数据库提供程序（如果尚未配置）。</span><span class="sxs-lookup"><span data-stu-id="84023-112">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

> [!TIP]  
> <span data-ttu-id="84023-113">如果使用的是 ASP.NET Core，则不需要此代码，因为数据库提供程序是在上下文之外（在 Startup.cs 中）配置的。</span><span class="sxs-lookup"><span data-stu-id="84023-113">If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="84023-114">添加用于测试的构造函数</span><span class="sxs-lookup"><span data-stu-id="84023-114">Add a constructor for testing</span></span>

<span data-ttu-id="84023-115">对不同数据库启用测试的最简单方法是修改上下文以公开接受 `DbContextOptions<TContext>`的构造函数。</span><span class="sxs-lookup"><span data-stu-id="84023-115">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="84023-116">`DbContextOptions<TContext>` 告知上下文所有设置，如要连接到的数据库。</span><span class="sxs-lookup"><span data-stu-id="84023-116">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="84023-117">这与在上下文中运行 OnConfiguring 方法所生成的对象相同。</span><span class="sxs-lookup"><span data-stu-id="84023-117">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="84023-118">编写测试</span><span class="sxs-lookup"><span data-stu-id="84023-118">Writing tests</span></span>

<span data-ttu-id="84023-119">使用此提供程序进行测试的关键是，可告知上下文使用 SQLite 并控制内存中数据库的范围。</span><span class="sxs-lookup"><span data-stu-id="84023-119">The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database.</span></span> <span data-ttu-id="84023-120">通过打开和关闭连接来控制数据库的范围。</span><span class="sxs-lookup"><span data-stu-id="84023-120">The scope of the database is controlled by opening and closing the connection.</span></span> <span data-ttu-id="84023-121">数据库的范围限定为连接打开的持续时间。</span><span class="sxs-lookup"><span data-stu-id="84023-121">The database is scoped to the duration that the connection is open.</span></span> <span data-ttu-id="84023-122">通常，每个测试方法都需要一个干净的数据库。</span><span class="sxs-lookup"><span data-stu-id="84023-122">Typically you want a clean database for each test method.</span></span>

>[!TIP]
> <span data-ttu-id="84023-123">若要使用 `SqliteConnection()` 和 `.UseSqlite()` 扩展方法，请参考 NuGet 包[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)。</span><span class="sxs-lookup"><span data-stu-id="84023-123">To use `SqliteConnection()` and the `.UseSqlite()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
