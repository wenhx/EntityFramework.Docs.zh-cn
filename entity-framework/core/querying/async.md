---
title: 异步查询 - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: 415c57df599f1cb1a255f01d1072890fedfd6d2b
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921697"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="a95c3-102">异步查询</span><span class="sxs-lookup"><span data-stu-id="a95c3-102">Asynchronous Queries</span></span>

<span data-ttu-id="a95c3-103">当在数据库中执行查询时，异步查询可避免阻止线程。</span><span class="sxs-lookup"><span data-stu-id="a95c3-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="a95c3-104">这有助于避免冻结胖客户端应用程序的 UI。</span><span class="sxs-lookup"><span data-stu-id="a95c3-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="a95c3-105">异步操作还可以增加 Web 应用程序中的吞吐量，其中可以释放线程以在数据库操作完成时处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="a95c3-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="a95c3-106">有关详细信息，请参阅[使用 C# 异步编程](https://docs.microsoft.com/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="a95c3-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="a95c3-107">EF Core 不支持在同一上下文实例上运行多个并行操作。</span><span class="sxs-lookup"><span data-stu-id="a95c3-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="a95c3-108">应始终等待操作完成，然后再开始下一个操作。</span><span class="sxs-lookup"><span data-stu-id="a95c3-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="a95c3-109">这通常是通过在每个异步操作上使用 `await` 关键字完成的。</span><span class="sxs-lookup"><span data-stu-id="a95c3-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="a95c3-110">Entity Framework Core 提供了一组异步扩展方法，可用作导致执行查询并返回结果的 LINQ 方法的替代方法。</span><span class="sxs-lookup"><span data-stu-id="a95c3-110">Entity Framework Core provides a set of asynchronous extension methods that can be used as an alternative to the LINQ methods that cause a query to be executed and results returned.</span></span> <span data-ttu-id="a95c3-111">示例包括 `ToListAsync()`、`ToArrayAsync()`、`SingleAsync()` 等。没有 LINQ 运算符（如 `Where(...)`、`OrderBy(...)` 等）的异步版本，因为这些方法仅生成 LINQ 表达式树，且不会导致在数据库中执行查询。</span><span class="sxs-lookup"><span data-stu-id="a95c3-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`, etc. There are not async versions of LINQ operators such as `Where(...)`, `OrderBy(...)`, etc. because these methods only build up the LINQ expression tree and do not cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="a95c3-112">在 `Microsoft.EntityFrameworkCore` 命名空间中定义 EF Core 异步扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a95c3-112">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="a95c3-113">必须导入此命名空间才能使这些方法可用。</span><span class="sxs-lookup"><span data-stu-id="a95c3-113">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#Sample)]
