---
title: 异步查询 - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181823"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="c1cd9-102">异步查询</span><span class="sxs-lookup"><span data-stu-id="c1cd9-102">Asynchronous Queries</span></span>

<span data-ttu-id="c1cd9-103">当在数据库中执行查询时，异步查询可避免阻止线程。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="c1cd9-104">异步查询对于在胖客户端应用程序中保持响应式 UI 非常重要。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-104">Async queries are important for keeping a responsive UI in thick-client applications.</span></span> <span data-ttu-id="c1cd9-105">异步查询还可以增加 Web 应用程序中的吞吐量，即通过释放线程，以处理 Web 应用程序中的其他请求。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-105">They can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span> <span data-ttu-id="c1cd9-106">有关详细信息，请参阅[使用 C# 异步编程](/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="c1cd9-107">EF Core 不支持在同一上下文实例上运行多个并行操作。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-107">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="c1cd9-108">应始终等待操作完成，然后再开始下一个操作。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="c1cd9-109">这通常是通过在每个异步操作上使用 `await` 关键字完成的。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-109">This is typically done by using the `await` keyword on each async operation.</span></span>

<span data-ttu-id="c1cd9-110">Entity Framework Core 提供一组类似于 LINQ 方法的异步扩展方法，用于执行查询并返回结果。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-110">Entity Framework Core provides a set of async extension methods similar to the LINQ methods, which execute a query and return results.</span></span> <span data-ttu-id="c1cd9-111">示例包括 `ToListAsync()`、`ToArrayAsync()`、`SingleAsync()`。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`.</span></span> <span data-ttu-id="c1cd9-112">某些 LINQ 运算符（如 `Where(...)` 或 `OrderBy(...)`）没有对应的异步版本，因为这些方法仅用于构建 LINQ 表达式树，而不会导致在数据库中执行查询。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-112">There are no async versions of some LINQ operators such as `Where(...)` or `OrderBy(...)` because these methods only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="c1cd9-113">EF Core 异步扩展方法在 `Microsoft.EntityFrameworkCore` 命名空间中定义 。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-113">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="c1cd9-114">必须导入此命名空间才能使这些方法可用。</span><span class="sxs-lookup"><span data-stu-id="c1cd9-114">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
