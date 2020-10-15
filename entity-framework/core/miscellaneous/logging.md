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
# <a name="logging"></a><span data-ttu-id="04ce9-103">Logging</span><span class="sxs-lookup"><span data-stu-id="04ce9-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="04ce9-104">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。</span><span class="sxs-lookup"><span data-stu-id="04ce9-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="04ce9-105">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="04ce9-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="04ce9-106">此功能是在 EF Core 5.0 中添加的。</span><span class="sxs-lookup"><span data-stu-id="04ce9-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="04ce9-107">Entity Framework Core (EF Core) 为操作（如执行查询或保存对数据库所做的更改）生成日志消息。</span><span class="sxs-lookup"><span data-stu-id="04ce9-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="04ce9-108">可以通过使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)从任何类型的应用程序访问这些应用程序</span><span class="sxs-lookup"><span data-stu-id="04ce9-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="04ce9-109">[配置 DbContext 实例](xref:core/miscellaneous/configuring-dbcontext)时。</span><span class="sxs-lookup"><span data-stu-id="04ce9-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="04ce9-110">此配置通常在的重写中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="04ce9-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="04ce9-111">例如：</span><span class="sxs-lookup"><span data-stu-id="04ce9-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="04ce9-112">此概念类似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6 中的。</span><span class="sxs-lookup"><span data-stu-id="04ce9-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="04ce9-113">有关详细信息，请参阅 [简单日志记录](xref:core/miscellaneous/events/simple-logging) 。</span><span class="sxs-lookup"><span data-stu-id="04ce9-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="04ce9-114">ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="04ce9-114">ASP.NET Core applications</span></span>

<span data-ttu-id="04ce9-115">使用或时，EF Core 会自动与 ASP.NET Core 的日志记录机制集成 `AddDbContext` `AddDbContextPool` 。</span><span class="sxs-lookup"><span data-stu-id="04ce9-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="04ce9-116">因此，在使用 ASP.NET Core 时，应按 [ASP.NET Core 文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="04ce9-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="04ce9-117">其他应用程序</span><span class="sxs-lookup"><span data-stu-id="04ce9-117">Other applications</span></span>

<span data-ttu-id="04ce9-118">EF Core 日志记录要求使用一个或多个日志记录提供程序配置的 ILoggerFactory。</span><span class="sxs-lookup"><span data-stu-id="04ce9-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="04ce9-119">常见的提供程序随附在以下包中：</span><span class="sxs-lookup"><span data-stu-id="04ce9-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="04ce9-120">""。[控制台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：一个简单的控制台记录器。</span><span class="sxs-lookup"><span data-stu-id="04ce9-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="04ce9-121">[AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支持 Azure 应用服务 "诊断日志" 和 "日志流" 功能。</span><span class="sxs-lookup"><span data-stu-id="04ce9-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="04ce9-122">使用 ( # A1，则使用 System.web： [debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)：记录到调试器监视器。</span><span class="sxs-lookup"><span data-stu-id="04ce9-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="04ce9-123">对 Windows 事件日志的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)事件日志。</span><span class="sxs-lookup"><span data-stu-id="04ce9-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="04ce9-124">EventListener：支持 EventSource/ [。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)</span><span class="sxs-lookup"><span data-stu-id="04ce9-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="04ce9-125">[TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用记录到跟踪 `System.Diagnostics.TraceSource.TraceEvent()` 侦听器。</span><span class="sxs-lookup"><span data-stu-id="04ce9-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="04ce9-126">在)  (安装适当的包后，应用程序应创建 Server.loggerfactory 的单一实例/全局实例。</span><span class="sxs-lookup"><span data-stu-id="04ce9-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="04ce9-127">例如，使用控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="04ce9-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="04ce9-128">3\.x 版</span><span class="sxs-lookup"><span data-stu-id="04ce9-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="04ce9-129">版本 2.x</span><span class="sxs-lookup"><span data-stu-id="04ce9-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="04ce9-130">下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。</span><span class="sxs-lookup"><span data-stu-id="04ce9-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="04ce9-131">使用2.2 时，可以安全地忽略和禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="04ce9-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="04ce9-132">然后，应在上向 EF Core 注册此单一实例/全局实例 `DbContextOptionsBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="04ce9-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="04ce9-133">例如：</span><span class="sxs-lookup"><span data-stu-id="04ce9-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="04ce9-134">对于每个上下文实例，应用程序不会创建新的 ILoggerFactory 实例，这一点非常重要。</span><span class="sxs-lookup"><span data-stu-id="04ce9-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="04ce9-135">这样做会导致内存泄漏和性能不佳。</span><span class="sxs-lookup"><span data-stu-id="04ce9-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="04ce9-136">筛选记录内容</span><span class="sxs-lookup"><span data-stu-id="04ce9-136">Filtering what is logged</span></span>

<span data-ttu-id="04ce9-137">应用程序可以通过在 ILoggerProvider 上配置筛选器来控制要记录的内容。</span><span class="sxs-lookup"><span data-stu-id="04ce9-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="04ce9-138">例如：</span><span class="sxs-lookup"><span data-stu-id="04ce9-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="04ce9-139">3\.x 版</span><span class="sxs-lookup"><span data-stu-id="04ce9-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="04ce9-140">版本 2.x</span><span class="sxs-lookup"><span data-stu-id="04ce9-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="04ce9-141">下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。</span><span class="sxs-lookup"><span data-stu-id="04ce9-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="04ce9-142">使用2.2 时，可以安全地忽略和禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="04ce9-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="04ce9-143">在此示例中，将筛选日志以仅返回消息：</span><span class="sxs-lookup"><span data-stu-id="04ce9-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="04ce9-144">在 "Microsoft.entityframeworkcore" 类别中</span><span class="sxs-lookup"><span data-stu-id="04ce9-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="04ce9-145">在 "信息" 级别</span><span class="sxs-lookup"><span data-stu-id="04ce9-145">at the 'Information' level</span></span>

<span data-ttu-id="04ce9-146">对于 EF Core，记录器类别在类中定义 `DbLoggerCategory` ，以方便查找类别，但这些类别解析为简单字符串。</span><span class="sxs-lookup"><span data-stu-id="04ce9-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="04ce9-147">有关基础日志记录基础结构的更多详细信息，请参阅 [ASP.NET Core 日志记录文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="04ce9-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
