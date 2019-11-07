---
title: 日志记录-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 6a8499f9f0220087e76f2e0b3a75ce551c4ddb80
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197505"
---
# <a name="logging"></a>Logging

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

与 ASP.NET Core 的日志记录机制自动集成，EF Core 每当`AddDbContext`或`AddDbContextPool`使用。 因此，在使用 ASP.NET Core 时，应按 [ASP.NET Core 文档](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中的说明配置日志记录。

## <a name="other-applications"></a>其他应用程序

EF Core 日志记录要求使用一个或多个日志记录提供程序配置的 ILoggerFactory。 以下包中随附有常见提供程序：

* [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：简单的控制台记录器。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支持 Azure 应用服务的 "诊断日志" 和 "日志流" 功能。
* [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)使用 system.exception （）将日志记录到调试器监视器。
* [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)：日志到 Windows 事件日志。
* [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)：支持 EventSource/EventListener。
* [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用`System.Diagnostics.TraceSource.TraceEvent()`将日志记录到跟踪侦听器。

安装适当的包后，应用程序应创建 Server.loggerfactory 的单一实例/全局实例。 例如，使用控制台记录器：

# <a name="version-30tabv3"></a>[版本3.0](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

# <a name="version-2xtabv2"></a>[版本2.x](#tab/v2)

> [!NOTE]
> 下面的代码示例使用`ConsoleLoggerProvider`已在版本2.2 中弃用并替换为3.0 的构造函数。 使用2.2 时，可以安全地忽略和禁止显示警告。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

然后，应该在 `DbContextOptionsBuilder` 上向 EF Core 注册此单一实例/全局实例。 例如:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> 应用程序不应为每个上下文实例创建新的 ILoggerFactory 实例，这一点非常重要。 这样做会导致内存泄漏和性能下降。

## <a name="filtering-what-is-logged"></a>筛选记录内容

应用程序可以通过在 ILoggerProvider 上配置筛选器来控制要记录的内容。 例如:

# <a name="version-30tabv3"></a>[版本3.0](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

# <a name="version-2xtabv2"></a>[版本2.x](#tab/v2)

> [!NOTE]
> 下面的代码示例使用`ConsoleLoggerProvider`已在版本2.2 中弃用并替换为3.0 的构造函数。 使用2.2 时，可以安全地忽略和禁止显示警告。

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

对于 EF Core 记录器类别在中定义`DbLoggerCategory`类，以便可以方便地查找类别，但这些解析为简单的字符串。

在基础的日志记录基础结构上的更多详细信息可在[ASP.NET Core 日志记录文档](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。
