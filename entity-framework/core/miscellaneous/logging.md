---
title: 日志记录-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 4f39e0ad1f061970aae7f7eb7abdc72e4bb0d691
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526454"
---
# <a name="logging"></a>日志记录

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

使用或时，EF Core 会自动与 ASP.NET Core 的日志记录机制集成 `AddDbContext` `AddDbContextPool` 。 因此，在使用 ASP.NET Core 时，应按[ASP.NET Core 文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述配置日志记录。

## <a name="other-applications"></a>其他应用程序

EF Core 日志记录要求使用一个或多个日志记录提供程序配置的 ILoggerFactory。 常见的提供程序随附在以下包中：

* ""。[控制台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：一个简单的控制台记录器。
* [AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支持 Azure 应用服务 "诊断日志" 和 "日志流" 功能。
* 使用 system.exception （）将日志记录到调试器监视器中的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)
* 对 Windows 事件日志的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)事件日志。
* EventListener：支持 EventSource/ [。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)
* [TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用记录到跟踪 `System.Diagnostics.TraceSource.TraceEvent()` 侦听器。

安装适当的包后，应用程序应创建 Server.loggerfactory 的单一实例/全局实例。 例如，使用控制台记录器：

### <a name="version-3x"></a>[3\.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[版本 2.x](#tab/v2)

> [!NOTE]
> 下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。 使用2.2 时，可以安全地忽略和禁止显示警告。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

然后，应在上向 EF Core 注册此单一实例/全局实例 `DbContextOptionsBuilder` 。 例如：

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> 对于每个上下文实例，应用程序不会创建新的 ILoggerFactory 实例，这一点非常重要。 这样做会导致内存泄漏和性能不佳。

## <a name="filtering-what-is-logged"></a>筛选记录内容

应用程序可以通过在 ILoggerProvider 上配置筛选器来控制要记录的内容。 例如：

### <a name="version-3x"></a>[3\.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[版本 2.x](#tab/v2)

> [!NOTE]
> 下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。 使用2.2 时，可以安全地忽略和禁止显示警告。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

在此示例中，将筛选日志以仅返回消息：

* 在 "Microsoft.entityframeworkcore" 类别中
* 在 "信息" 级别

对于 EF Core，记录器类别在类中定义 `DbLoggerCategory` ，以方便查找类别，但这些类别解析为简单字符串。

有关基础日志记录基础结构的更多详细信息，请参阅[ASP.NET Core 日志记录文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。
