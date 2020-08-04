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
# <a name="logging"></a><span data-ttu-id="31283-102">日志记录</span><span class="sxs-lookup"><span data-stu-id="31283-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="31283-103">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。</span><span class="sxs-lookup"><span data-stu-id="31283-103">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="31283-104">ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="31283-104">ASP.NET Core applications</span></span>

<span data-ttu-id="31283-105">使用或时，EF Core 会自动与 ASP.NET Core 的日志记录机制集成 `AddDbContext` `AddDbContextPool` 。</span><span class="sxs-lookup"><span data-stu-id="31283-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="31283-106">因此，在使用 ASP.NET Core 时，应按[ASP.NET Core 文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中所述配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="31283-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="31283-107">其他应用程序</span><span class="sxs-lookup"><span data-stu-id="31283-107">Other applications</span></span>

<span data-ttu-id="31283-108">EF Core 日志记录要求使用一个或多个日志记录提供程序配置的 ILoggerFactory。</span><span class="sxs-lookup"><span data-stu-id="31283-108">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="31283-109">常见的提供程序随附在以下包中：</span><span class="sxs-lookup"><span data-stu-id="31283-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="31283-110">""。[控制台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：一个简单的控制台记录器。</span><span class="sxs-lookup"><span data-stu-id="31283-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="31283-111">[AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支持 Azure 应用服务 "诊断日志" 和 "日志流" 功能。</span><span class="sxs-lookup"><span data-stu-id="31283-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="31283-112">使用 system.exception （）将日志记录到调试器监视器中的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)</span><span class="sxs-lookup"><span data-stu-id="31283-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="31283-113">对 Windows 事件日志的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)事件日志。</span><span class="sxs-lookup"><span data-stu-id="31283-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="31283-114">EventListener：支持 EventSource/ [。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)</span><span class="sxs-lookup"><span data-stu-id="31283-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="31283-115">[TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用记录到跟踪 `System.Diagnostics.TraceSource.TraceEvent()` 侦听器。</span><span class="sxs-lookup"><span data-stu-id="31283-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="31283-116">安装适当的包后，应用程序应创建 Server.loggerfactory 的单一实例/全局实例。</span><span class="sxs-lookup"><span data-stu-id="31283-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="31283-117">例如，使用控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="31283-117">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="31283-118">3\.x 版</span><span class="sxs-lookup"><span data-stu-id="31283-118">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="31283-119">版本 2.x</span><span class="sxs-lookup"><span data-stu-id="31283-119">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="31283-120">下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。</span><span class="sxs-lookup"><span data-stu-id="31283-120">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="31283-121">使用2.2 时，可以安全地忽略和禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="31283-121">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="31283-122">然后，应在上向 EF Core 注册此单一实例/全局实例 `DbContextOptionsBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="31283-122">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="31283-123">例如：</span><span class="sxs-lookup"><span data-stu-id="31283-123">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="31283-124">对于每个上下文实例，应用程序不会创建新的 ILoggerFactory 实例，这一点非常重要。</span><span class="sxs-lookup"><span data-stu-id="31283-124">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="31283-125">这样做会导致内存泄漏和性能不佳。</span><span class="sxs-lookup"><span data-stu-id="31283-125">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="31283-126">筛选记录内容</span><span class="sxs-lookup"><span data-stu-id="31283-126">Filtering what is logged</span></span>

<span data-ttu-id="31283-127">应用程序可以通过在 ILoggerProvider 上配置筛选器来控制要记录的内容。</span><span class="sxs-lookup"><span data-stu-id="31283-127">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="31283-128">例如：</span><span class="sxs-lookup"><span data-stu-id="31283-128">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="31283-129">3\.x 版</span><span class="sxs-lookup"><span data-stu-id="31283-129">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="31283-130">版本 2.x</span><span class="sxs-lookup"><span data-stu-id="31283-130">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="31283-131">下面的代码示例使用已 `ConsoleLoggerProvider` 在版本2.2 中弃用并替换为3.0 的构造函数。</span><span class="sxs-lookup"><span data-stu-id="31283-131">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="31283-132">使用2.2 时，可以安全地忽略和禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="31283-132">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="31283-133">在此示例中，将筛选日志以仅返回消息：</span><span class="sxs-lookup"><span data-stu-id="31283-133">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="31283-134">在 "Microsoft.entityframeworkcore" 类别中</span><span class="sxs-lookup"><span data-stu-id="31283-134">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="31283-135">在 "信息" 级别</span><span class="sxs-lookup"><span data-stu-id="31283-135">at the 'Information' level</span></span>

<span data-ttu-id="31283-136">对于 EF Core，记录器类别在类中定义 `DbLoggerCategory` ，以方便查找类别，但这些类别解析为简单字符串。</span><span class="sxs-lookup"><span data-stu-id="31283-136">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="31283-137">有关基础日志记录基础结构的更多详细信息，请参阅[ASP.NET Core 日志记录文档](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="31283-137">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
