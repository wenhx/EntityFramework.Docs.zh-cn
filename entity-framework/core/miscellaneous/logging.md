---
title: 日志记录-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 1a3863ee5f508c1fd393d4ec2c25c46ab8634f00
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502092"
---
# <a name="logging"></a><span data-ttu-id="5a5dd-102">Logging</span><span class="sxs-lookup"><span data-stu-id="5a5dd-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="5a5dd-103">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="5a5dd-104">ASP.NET Core应用程序</span><span class="sxs-lookup"><span data-stu-id="5a5dd-104">ASP.NET Core applications</span></span>

<span data-ttu-id="5a5dd-105">与 ASP.NET Core 的日志记录机制自动集成，EF Core 每当`AddDbContext`或`AddDbContextPool`使用。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="5a5dd-106">因此，在使用 ASP.NET Core 时，应按 [ASP.NET Core 文档](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中的说明配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="5a5dd-107">其他应用程序</span><span class="sxs-lookup"><span data-stu-id="5a5dd-107">Other applications</span></span>

<span data-ttu-id="5a5dd-108">EF Core 日志记录要求使用一个或多个日志记录提供程序配置的 ILoggerFactory。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-108">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="5a5dd-109">以下包中随附有常见提供程序：</span><span class="sxs-lookup"><span data-stu-id="5a5dd-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="5a5dd-110">""。[控制台](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)：一个简单的控制台记录器。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="5a5dd-111">[AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)：支持 Azure 应用服务 "诊断日志" 和 "日志流" 功能。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="5a5dd-112">使用 system.exception （）将日志记录到调试器监视器中的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)</span><span class="sxs-lookup"><span data-stu-id="5a5dd-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="5a5dd-113">对 Windows 事件日志的日志[记录。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)事件日志。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="5a5dd-114">EventListener：支持 EventSource/ [。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)</span><span class="sxs-lookup"><span data-stu-id="5a5dd-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="5a5dd-115">[TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)：使用 `System.Diagnostics.TraceSource.TraceEvent()`将日志记录到跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="5a5dd-116">安装适当的包后，应用程序应创建 Server.loggerfactory 的单一实例/全局实例。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="5a5dd-117">例如，使用控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="5a5dd-117">For example, using the console logger:</span></span>

### <a name="version-30tabv3"></a>[<span data-ttu-id="5a5dd-118">版本3.0</span><span class="sxs-lookup"><span data-stu-id="5a5dd-118">Version 3.0</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2xtabv2"></a>[<span data-ttu-id="5a5dd-119">版本2.x</span><span class="sxs-lookup"><span data-stu-id="5a5dd-119">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="5a5dd-120">下面的代码示例使用在版本2.2 中已过时的 `ConsoleLoggerProvider` 构造函数并在3.0 中替换。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-120">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="5a5dd-121">使用2.2 时，可以安全地忽略和禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-121">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="5a5dd-122">此 singleton/全局实例应然后注册与 EF Core 上`DbContextOptionsBuilder`。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-122">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="5a5dd-123">例如：</span><span class="sxs-lookup"><span data-stu-id="5a5dd-123">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="5a5dd-124">应用程序不应为每个上下文实例创建新的 ILoggerFactory 实例，这一点非常重要。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-124">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="5a5dd-125">这样做会导致内存泄漏和性能下降。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-125">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="5a5dd-126">筛选记录内容</span><span class="sxs-lookup"><span data-stu-id="5a5dd-126">Filtering what is logged</span></span>

<span data-ttu-id="5a5dd-127">应用程序可以通过在 ILoggerProvider 上配置筛选器来控制要记录的内容。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-127">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="5a5dd-128">例如：</span><span class="sxs-lookup"><span data-stu-id="5a5dd-128">For example:</span></span>

### <a name="version-30tabv3"></a>[<span data-ttu-id="5a5dd-129">版本3.0</span><span class="sxs-lookup"><span data-stu-id="5a5dd-129">Version 3.0</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2xtabv2"></a>[<span data-ttu-id="5a5dd-130">版本2.x</span><span class="sxs-lookup"><span data-stu-id="5a5dd-130">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="5a5dd-131">下面的代码示例使用在版本2.2 中已过时的 `ConsoleLoggerProvider` 构造函数并在3.0 中替换。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-131">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="5a5dd-132">使用2.2 时，可以安全地忽略和禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-132">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="5a5dd-133">在此示例中，将筛选日志以仅返回消息：</span><span class="sxs-lookup"><span data-stu-id="5a5dd-133">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="5a5dd-134">在 "Microsoft.entityframeworkcore" 类别中</span><span class="sxs-lookup"><span data-stu-id="5a5dd-134">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="5a5dd-135">在 "信息" 级别</span><span class="sxs-lookup"><span data-stu-id="5a5dd-135">at the 'Information' level</span></span>

<span data-ttu-id="5a5dd-136">对于 EF Core 记录器类别在中定义`DbLoggerCategory`类，以便可以方便地查找类别，但这些解析为简单的字符串。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-136">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="5a5dd-137">在基础的日志记录基础结构上的更多详细信息可在[ASP.NET Core 日志记录文档](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="5a5dd-137">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
