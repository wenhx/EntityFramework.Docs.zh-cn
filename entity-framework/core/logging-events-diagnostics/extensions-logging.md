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
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="6c5a5-103">使用 EF Core 中的登录</span><span class="sxs-lookup"><span data-stu-id="6c5a5-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="6c5a5-104">例如，[日志记录](/dotnet/core/extensions/logging)是一种可扩展的日志记录机制，适用于很多常见日志记录系统的插件提供程序。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="6c5a5-105">Microsoft 提供的插件 (例如， [microsoft](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 提供的插件) 和第三方插件 (例如， [Serilog](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) 可作为 NuGet 包提供。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="6c5a5-106">Entity Framework Core (EF Core) 与完全集成 `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="6c5a5-107">不过，请考虑使用 [简单日志记录](xref:core/logging-events-diagnostics/simple-logging) ，以便更简单地进行日志记录，特别是对于不使用依赖关系注入的应用程序。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="6c5a5-108">ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="6c5a5-108">ASP.NET Core applications</span></span>

<span data-ttu-id="6c5a5-109">`Microsoft.Extensions.Logging`[默认情况下，在 ASP.NET Core 应用程序中使用](/aspnet/core/fundamentals/logging)。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="6c5a5-110">调用 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 或 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="6c5a5-111">其他应用程序类型</span><span class="sxs-lookup"><span data-stu-id="6c5a5-111">Other application types</span></span>

<span data-ttu-id="6c5a5-112">其他应用程序类型可以使用 [GenericHost](/dotnet/core/extensions/generic-host) 获取与 ASP.NET Core 中使用的相同的依赖项注入模式。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="6c5a5-113">然后，可以像在 ASP.NET Core 应用程序中一样使用 AddDbContext 或 AddDbContextPool。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="6c5a5-114">`Microsoft.Extensions.Logging` 还可用于不使用依赖关系注入的应用程序，尽管 [简单日志记录](xref:core/logging-events-diagnostics/simple-logging) 可以更轻松地进行设置。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="6c5a5-115">`Microsoft.Extensions.Logging` 需要创建 <xref:Microsoft.Extensions.Logging.LoggerFactory> 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="6c5a5-116">此工厂应存储为静态/全局实例，并在每次创建 DbContext 时使用。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="6c5a5-117">例如，通常将记录器工厂存储为 DbContext 上的静态属性。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="6c5a5-118">EF Core 3.0 及更高版本</span><span class="sxs-lookup"><span data-stu-id="6c5a5-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="6c5a5-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="6c5a5-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="6c5a5-120">在 EF Core 2.1 中，对于每个 DbContext 实例，应用程序不会创建新的 Server.loggerfactory 实例，这一点非常重要。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="6c5a5-121">这样做会导致内存泄漏和性能不佳。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="6c5a5-122">这已在 EF Core 3.0 及更高版本中得到解决。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="6c5a5-123">然后，应在上向 EF Core 注册此单一实例/全局实例 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="6c5a5-124">例如：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="6c5a5-125">获取详细消息</span><span class="sxs-lookup"><span data-stu-id="6c5a5-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="6c5a5-126">当使用 AddDbContext 或将 DbContextOptions 实例传递到 DbContext 构造函数时，仍将调用 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="6c5a5-127">这使得它成为应用上下文配置的理想位置，而不考虑如何构造 DbContext。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="6c5a5-128">敏感数据</span><span class="sxs-lookup"><span data-stu-id="6c5a5-128">Sensitive data</span></span>

<span data-ttu-id="6c5a5-129">默认情况下，EF Core 不会将任何数据的值包含在异常消息中。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="6c5a5-130">这是因为此类数据可能是机密的，如果未处理异常，则可能会在生产中显示这些数据。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="6c5a5-131">但是，在调试时，知道数据值（特别是对于密钥）会非常有用。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="6c5a5-132">可以通过调用在 EF Core 中启用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="6c5a5-133">例如：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="6c5a5-134">详细查询异常</span><span class="sxs-lookup"><span data-stu-id="6c5a5-134">Detailed query exceptions</span></span>

<span data-ttu-id="6c5a5-135">出于性能方面的考虑，EF Core 不会在 try-catch 块中包装每次调用以读取数据库提供程序的值。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="6c5a5-136">但是，这有时会导致难以诊断的异常，尤其是在数据库不允许的情况下，当模型不允许时，它们会返回 NULL。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="6c5a5-137">启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 将导致 EF 引入这些 try-catch 块，从而提供更详细的错误。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="6c5a5-138">例如：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="6c5a5-139">特定消息的配置</span><span class="sxs-lookup"><span data-stu-id="6c5a5-139">Configuration for specific messages</span></span>

<span data-ttu-id="6c5a5-140">EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API 允许应用程序更改遇到特定事件时所发生的情况。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="6c5a5-141">这可用于：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-141">This can be used to:</span></span>

* <span data-ttu-id="6c5a5-142">更改记录事件的日志级别</span><span class="sxs-lookup"><span data-stu-id="6c5a5-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="6c5a5-143">完全跳过记录事件</span><span class="sxs-lookup"><span data-stu-id="6c5a5-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="6c5a5-144">事件发生时引发异常</span><span class="sxs-lookup"><span data-stu-id="6c5a5-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="6c5a5-145">更改事件的日志级别</span><span class="sxs-lookup"><span data-stu-id="6c5a5-145">Changing the log level for an event</span></span>

<span data-ttu-id="6c5a5-146">有时，更改事件的预定义日志级别可能会很有用。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="6c5a5-147">例如，这可用于将两个附加事件从升级 `LogLevel.Debug` 到 `LogLevel.Information` ：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="6c5a5-148">禁止记录事件</span><span class="sxs-lookup"><span data-stu-id="6c5a5-148">Suppress logging an event</span></span>

<span data-ttu-id="6c5a5-149">同样，可以从日志记录中抑制单个事件。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="6c5a5-150">这对于忽略已评审和理解的警告特别有用。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="6c5a5-151">例如：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="6c5a5-152">引发事件</span><span class="sxs-lookup"><span data-stu-id="6c5a5-152">Throw for an event</span></span>

<span data-ttu-id="6c5a5-153">最后，可以将 EF Core 配置为引发给定事件。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="6c5a5-154">这对于将警告更改为错误特别有用。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="6c5a5-155"> (确实，这是方法的原始用途 `ConfigureWarnings` ，因此是名称。 ) 例如：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="6c5a5-156">筛选和其他配置</span><span class="sxs-lookup"><span data-stu-id="6c5a5-156">Filtering and other configuration</span></span>

<span data-ttu-id="6c5a5-157">有关日志筛选和其他配置的指导，请参阅 [.net 中的日志记录](/dotnet/core/extensions/logging) 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="6c5a5-158">EF Core 日志记录事件在以下其中之一进行定义：</span><span class="sxs-lookup"><span data-stu-id="6c5a5-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="6c5a5-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 对于所有 EF Core 数据库提供程序所共有的事件</span><span class="sxs-lookup"><span data-stu-id="6c5a5-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="6c5a5-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 对于所有关系数据库提供程序通用的事件</span><span class="sxs-lookup"><span data-stu-id="6c5a5-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="6c5a5-161">特定于当前数据库提供程序的事件的类似类。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="6c5a5-162">例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> 对于 SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="6c5a5-163">这些定义包含事件 Id、日志级别和每个事件的类别（由使用） `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="6c5a5-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
