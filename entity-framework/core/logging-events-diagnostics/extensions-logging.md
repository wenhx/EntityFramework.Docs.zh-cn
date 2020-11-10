---
title: 使用 EF Core 日志记录-
description: 使用 EF Core 在 ASP.NET Core 和其他应用程序类型中进行日志记录
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431219"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a>使用 EF Core 中的登录

例如，[日志记录](/dotnet/core/extensions/logging)是一种可扩展的日志记录机制，适用于很多常见日志记录系统的插件提供程序。 Microsoft 提供的插件 (例如， [microsoft](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 提供的插件) 和第三方插件 (例如， [Serilog](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) 可作为 NuGet 包提供。

Entity Framework Core (EF Core) 与完全集成 `Microsoft.Extensions.Logging` 。 不过，请考虑使用 [简单日志记录](xref:core/logging-events-diagnostics/simple-logging) ，以便更简单地进行日志记录，特别是对于不使用依赖关系注入的应用程序。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

`Microsoft.Extensions.Logging`[默认情况下，在 ASP.NET Core 应用程序中使用](/aspnet/core/fundamentals/logging)。 调用 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 或 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 。

## <a name="other-application-types"></a>其他应用程序类型

其他应用程序类型可以使用 [GenericHost](/dotnet/core/extensions/generic-host) 获取与 ASP.NET Core 中使用的相同的依赖项注入模式。 然后，可以像在 ASP.NET Core 应用程序中一样使用 AddDbContext 或 AddDbContextPool。

`Microsoft.Extensions.Logging` 还可用于不使用依赖关系注入的应用程序，尽管 [简单日志记录](xref:core/logging-events-diagnostics/simple-logging) 可以更轻松地进行设置。

`Microsoft.Extensions.Logging` 需要创建 <xref:Microsoft.Extensions.Logging.LoggerFactory> 。 此工厂应存储为静态/全局实例，并在每次创建 DbContext 时使用。 例如，通常将记录器工厂存储为 DbContext 上的静态属性。

### <a name="ef-core-30-and-above"></a>[EF Core 3.0 及更高版本](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> 在 EF Core 2.1 中，对于每个 DbContext 实例，应用程序不会创建新的 Server.loggerfactory 实例，这一点非常重要。 这样做会导致内存泄漏和性能不佳。 这已在 EF Core 3.0 及更高版本中得到解决。

***

然后，应在上向 EF Core 注册此单一实例/全局实例 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> 。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>获取详细消息

> [!TIP]
> 当使用 AddDbContext 或将 DbContextOptions 实例传递到 DbContext 构造函数时，仍将调用 OnConfiguring。 这使得它成为应用上下文配置的理想位置，而不考虑如何构造 DbContext。

### <a name="sensitive-data"></a>敏感数据

默认情况下，EF Core 不会将任何数据的值包含在异常消息中。 这是因为此类数据可能是机密的，如果未处理异常，则可能会在生产中显示这些数据。

但是，在调试时，知道数据值（特别是对于密钥）会非常有用。 可以通过调用在 EF Core 中启用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>详细查询异常

出于性能方面的考虑，EF Core 不会在 try-catch 块中包装每次调用以读取数据库提供程序的值。 但是，这有时会导致难以诊断的异常，尤其是在数据库不允许的情况下，当模型不允许时，它们会返回 NULL。

启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 将导致 EF 引入这些 try-catch 块，从而提供更详细的错误。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>特定消息的配置

EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API 允许应用程序更改遇到特定事件时所发生的情况。 这可用于：

* 更改记录事件的日志级别
* 完全跳过记录事件
* 事件发生时引发异常

### <a name="changing-the-log-level-for-an-event"></a>更改事件的日志级别

有时，更改事件的预定义日志级别可能会很有用。 例如，这可用于将两个附加事件从升级 `LogLevel.Debug` 到 `LogLevel.Information` ：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>禁止记录事件

同样，可以从日志记录中抑制单个事件。 这对于忽略已评审和理解的警告特别有用。 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>引发事件

最后，可以将 EF Core 配置为引发给定事件。 这对于将警告更改为错误特别有用。  (确实，这是方法的原始用途 `ConfigureWarnings` ，因此是名称。 ) 例如：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>筛选和其他配置

有关日志筛选和其他配置的指导，请参阅 [.net 中的日志记录](/dotnet/core/extensions/logging) 。

EF Core 日志记录事件在以下其中之一进行定义：

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 对于所有 EF Core 数据库提供程序所共有的事件
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 对于所有关系数据库提供程序通用的事件
* 特定于当前数据库提供程序的事件的类似类。 例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> 对于 SQL Server 提供程序。

这些定义包含事件 Id、日志级别和每个事件的类别（由使用） `Microsoft.Extensions.Logging` 。
