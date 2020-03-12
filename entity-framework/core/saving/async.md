---
title: 异步保存 - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: 0823b86f0579dd3e42f6bd2aebfb433d3cbe00ab
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413689"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="82b5e-102">异步保存</span><span class="sxs-lookup"><span data-stu-id="82b5e-102">Asynchronous Saving</span></span>

<span data-ttu-id="82b5e-103">当所做的更改写入数据库时，异步保存可避免阻止线程。</span><span class="sxs-lookup"><span data-stu-id="82b5e-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="82b5e-104">这有助于避免胖客户端应用程序的 UI 被冻结。</span><span class="sxs-lookup"><span data-stu-id="82b5e-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="82b5e-105">异步操作还可以增加 Web 应用程序中的吞吐量，其中可以释放线程以在数据库操作完成时处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="82b5e-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="82b5e-106">有关详细信息，请参阅[使用 C# 异步编程](https://docs.microsoft.com/dotnet/csharp/async)。</span><span class="sxs-lookup"><span data-stu-id="82b5e-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="82b5e-107">EF Core 不支持在同一上下文实例上运行多个并行操作。</span><span class="sxs-lookup"><span data-stu-id="82b5e-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="82b5e-108">应始终等待操作完成，然后再开始下一个操作。</span><span class="sxs-lookup"><span data-stu-id="82b5e-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="82b5e-109">这通常是通过在每个异步操作上使用 `await` 关键字完成的。</span><span class="sxs-lookup"><span data-stu-id="82b5e-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="82b5e-110">Entity Framework Core 提供了 `DbContext.SaveChangesAsync()` 作为 `DbContext.SaveChanges()` 的异步替代方法。</span><span class="sxs-lookup"><span data-stu-id="82b5e-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
