---
title: 异步查询 - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: de00e25279e29355a4eb3e55597a8578ceccecb6
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993560"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="4cad8-102">异步查询</span><span class="sxs-lookup"><span data-stu-id="4cad8-102">Asynchronous Queries</span></span>

<span data-ttu-id="4cad8-103">当在数据库中执行查询时，异步查询可避免阻止线程。</span><span class="sxs-lookup"><span data-stu-id="4cad8-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="4cad8-104">这有助于避免冻结胖客户端应用程序的 UI。</span><span class="sxs-lookup"><span data-stu-id="4cad8-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="4cad8-105">异步操作还可以增加 Web 应用程序的吞吐量，可以释放线程以在数据库操作完成前去处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="4cad8-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="4cad8-106">有关详细信息，请参阅[使用 C# 异步编程](https://docs.microsoft.com/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="4cad8-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="4cad8-107">EF Core 不支持在同一上下文实例上运行多个并行操作。</span><span class="sxs-lookup"><span data-stu-id="4cad8-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="4cad8-108">应始终等待操作完成，然后再开始下一个操作。</span><span class="sxs-lookup"><span data-stu-id="4cad8-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="4cad8-109">这通常是通过在每个异步操作上使用 `await` 关键字完成的。</span><span class="sxs-lookup"><span data-stu-id="4cad8-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="4cad8-110">Entity Framework Core 提供了一组异步扩展方法，可用作执行查询并返回结果的 LINQ 方法的替代方法。</span><span class="sxs-lookup"><span data-stu-id="4cad8-110">Entity Framework Core provides a set of asynchronous extension methods that can be used as an alternative to the LINQ methods that cause a query to be executed and results returned.</span></span> <span data-ttu-id="4cad8-111">示例包括 `ToListAsync()`、`ToArrayAsync()`、`SingleAsync()` 等。对于部分 LINQ 运算符（如 `Where(...)`、`OrderBy(...)` 等），没有对应的异步版本，因为这些方法仅用于构建 LINQ 表达式树，而未将查询发送到数据库中执行。</span><span class="sxs-lookup"><span data-stu-id="4cad8-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`, etc. There are not async versions of LINQ operators such as `Where(...)`, `OrderBy(...)`, etc. because these methods only build up the LINQ expression tree and do not cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="4cad8-112">在 `Microsoft.EntityFrameworkCore` 命名空间中定义 EF Core 异步扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4cad8-112">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="4cad8-113">必须导入此命名空间才能使这些方法可用。</span><span class="sxs-lookup"><span data-stu-id="4cad8-113">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/Async/Sample.cs#Sample)]
