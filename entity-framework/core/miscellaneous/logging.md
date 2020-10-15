---
title: 日志记录-EF Core
description: 配置 Entity Framework Core 日志记录
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063005"
---
# <a name="logging"></a>Logging

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。

## <a name="simple-logging"></a>简单的日志记录

> [!NOTE]
> 此功能是在 EF Core 5.0 中添加的。

Entity Framework Core (EF Core) 为操作（如执行查询或保存对数据库所做的更改）生成日志消息。 可以通过使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)从任何类型的应用程序访问这些应用程序 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> [配置 DbContext 实例](xref:core/miscellaneous/configuring-dbcontext)时。 此配置通常在的重写中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

此概念类似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6 中的。

有关详细信息，请参阅 [简单日志记录](xref:core/miscellaneous/events/simple-logging) 。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

使用或时，EF Core 会自动与 ASP.NET Core 的日志记录机制集成 `AddDbContext` `AddDbContextPool` 。 因此，在使用 ASP.NET Core 时，应按 [ASP.NET Core 文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述配置日志记录。

## <a name="other-applications"></a>其他应用程序

EF Core 日志记录要求使用一个或多个日志记录提供程序配置的 ILoggerFactory。 常见的提供程序随附在以下包中：

* ""。[控制台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：一个简单的控制台记录器。
* [AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支持 Azure 应用服务 "诊断日志" 和 "日志流" 功能。
* 使用 ( # A1，则使用 System.web： [debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)：记录到调试器监视器。
* 对 Windows 事件日志的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)事件日志。
* EventListener：支持 EventSource/ [。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)
* [TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用记录到跟踪 `System.Diagnostics.TraceSource.TraceEvent()` 侦听器。

在)  (安装适当的包后，应用程序应创建 Server.loggerfactory 的单一实例/全局实例。 例如，使用控制台记录器：

### <a name="version-3x"></a>[3\.x 版](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[版本 2.x](#tab/v2)

> [!NOTE]
> 下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。 使用2.2 时，可以安全地忽略和禁止显示警告。

```csharp
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

```csharp
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

有关基础日志记录基础结构的更多详细信息，请参阅 [ASP.NET Core 日志记录文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。
