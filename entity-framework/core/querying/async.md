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
# <a name="asynchronous-queries"></a>异步查询

当在数据库中执行查询时，异步查询可避免阻止线程。 这有助于避免冻结胖客户端应用程序的 UI。 异步操作还可以增加 Web 应用程序中的吞吐量，其中可以释放线程以在数据库操作完成时处理其他请求。 有关详细信息，请参阅[使用 C# 异步编程](https://docs.microsoft.com/dotnet/csharp/async)。

> [!WARNING]  
> EF Core 不支持在同一上下文实例上运行多个并行操作。 应始终等待操作完成，然后再开始下一个操作。 这通常是通过在每个异步操作上使用 `await` 关键字完成的。

Entity Framework Core 提供了一组异步扩展方法，可用作导致执行查询并返回结果的 LINQ 方法的替代方法。 示例包括 `ToListAsync()`、`ToArrayAsync()`、`SingleAsync()` 等。没有 LINQ 运算符（如 `Where(...)`、`OrderBy(...)` 等）的异步版本，因为这些方法仅生成 LINQ 表达式树，且不会导致在数据库中执行查询。

> [!IMPORTANT]  
> 在 `Microsoft.EntityFrameworkCore` 命名空间中定义 EF Core 异步扩展方法。 必须导入此命名空间才能使这些方法可用。

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#Sample)]
