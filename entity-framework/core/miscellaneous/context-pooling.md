---
title: DbContext 池
description: Entity Framework Core 中的 DbContext 池
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: 8638c838511be85bd994751b9911b107974dfe2f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061952"
---
# <a name="dbcontext-pooling"></a>DbContext 池

`AddDbContextPool` 启用实例的池 `DbContext` 。 上下文池可以通过重复使用上下文实例，而不是为每个请求创建新实例，从而提高大规模方案（如 web 服务器）的吞吐量。

使用 EF Core 的 ASP.NET Core 应用程序中的典型模式涉及将自定义 <xref:Microsoft.EntityFrameworkCore.DbContext> 类型注册到 [依赖关系注入](/aspnet/core/fundamentals/dependency-injection) 容器，并通过控制器或 Razor Pages 中的构造函数参数获取该类型的实例。 使用构造函数注入，将为每个请求创建一个新的上下文实例。

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 启用可重用上下文实例的池。 若要使用上下文池，请使用 `AddDbContextPool` 方法，而不是 `AddDbContext` 在服务注册期间使用：

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

`AddDbContextPool`使用时，在请求上下文实例时，EF 首先检查池中是否有可用的实例。 请求处理完成后，实例的任何状态都将被重置，并且实例本身会返回池中。

从概念上讲，这类似于连接池在 ADO.NET 提供程序中的工作方式，并且具有保存上下文实例的某些初始化开销的优点。

的 `poolSize` 参数 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 设置池保留的最大实例数。 一旦 `poolSize` 超出，就不会缓存新的上下文实例，EF 会回退到按需创建实例的非池行为。

## <a name="limitations"></a>限制

应该对应用进行分析和测试，以表明上下文初始化非常重要。

`AddDbContextPool` 对可在上下文的方法中完成的操作有一些限制 `OnConfiguring` 。

> [!WARNING]
> 避免在维护状态的应用程序中使用上下文池。 例如，不应在请求之间共享的上下文中的私有字段。 在将上下文实例添加到池中之前，EF Core 仅重置它知道的状态。

上下文池的工作方式是跨请求重复使用同一上下文实例。 这意味着，它可以有效地根据实例本身注册为 [单一](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 实例，以便能够持久保存。

上下文池适用于在请求之间修复了上下文配置（包括服务已解决）的情况。 对于需要 [范围内](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) 的服务或需要更改配置的情况，请不要使用 pooling。 除高度优化的方案外，池的性能提升通常可以忽略不计。
