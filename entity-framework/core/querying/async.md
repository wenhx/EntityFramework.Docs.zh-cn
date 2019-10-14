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
# <a name="asynchronous-queries"></a>异步查询

当在数据库中执行查询时，异步查询可避免阻止线程。 异步查询对于在胖客户端应用程序中保持响应式 UI 非常重要。 异步查询还可以增加 Web 应用程序中的吞吐量，即通过释放线程，以处理 Web 应用程序中的其他请求。 有关详细信息，请参阅[使用 C# 异步编程](/dotnet/csharp/async)。

> [!WARNING]  
> EF Core 不支持在同一上下文实例上运行多个并行操作。 应始终等待操作完成，然后再开始下一个操作。 这通常是通过在每个异步操作上使用 `await` 关键字完成的。

Entity Framework Core 提供一组类似于 LINQ 方法的异步扩展方法，用于执行查询并返回结果。 示例包括 `ToListAsync()`、`ToArrayAsync()`、`SingleAsync()`。 某些 LINQ 运算符（如 `Where(...)` 或 `OrderBy(...)`）没有对应的异步版本，因为这些方法仅用于构建 LINQ 表达式树，而不会导致在数据库中执行查询。

> [!IMPORTANT]  
> EF Core 异步扩展方法在 `Microsoft.EntityFrameworkCore` 命名空间中定义 。 必须导入此命名空间才能使这些方法可用。

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
