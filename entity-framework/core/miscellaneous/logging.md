---
title: "日志记录-EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
ms.technology: entity-framework-core
uid: core/miscellaneous/logging
ms.openlocfilehash: 807560e563eddfb72d4286353b1403a0d2e2a441
ms.sourcegitcommit: 5367516f063cb42804ec92c31cdf76322554f2b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="logging"></a><span data-ttu-id="840e6-102">日志记录</span><span class="sxs-lookup"><span data-stu-id="840e6-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="840e6-103">你可以查看这篇文章[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="840e6-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="840e6-104">ASP.NET 核心应用程序</span><span class="sxs-lookup"><span data-stu-id="840e6-104">ASP.NET Core applications</span></span>

<span data-ttu-id="840e6-105">与 ASP.NET Core 日志记录机制自动集成的 EF 核心每当`AddDbContext`或`AddDbContextPool`使用。</span><span class="sxs-lookup"><span data-stu-id="840e6-105">EF Core integrates automatically with the logging mechanims of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="840e6-106">因此，在使用 ASP.NET Core，日志记录应配置中所述[ASP.NET 核心文档](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="840e6-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="840e6-107">其他应用程序</span><span class="sxs-lookup"><span data-stu-id="840e6-107">Other applications</span></span>

<span data-ttu-id="840e6-108">当前日志记录的 EF 核心要求 ILoggerFactory 这是配置了一个或多个 ILoggerProvider 本身。</span><span class="sxs-lookup"><span data-stu-id="840e6-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="840e6-109">常见的提供程序随附于下列包：</span><span class="sxs-lookup"><span data-stu-id="840e6-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="840e6-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)： 简单的控制台记录器。</span><span class="sxs-lookup"><span data-stu-id="840e6-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="840e6-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)： 支持 Azure 应用程序服务诊断日志和日志流功能。</span><span class="sxs-lookup"><span data-stu-id="840e6-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="840e6-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/)： 日志传输到使用 System.Diagnostics.Debug.WriteLine() 调试器监视器。</span><span class="sxs-lookup"><span data-stu-id="840e6-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="840e6-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)： 到 Windows 事件日志的日志。</span><span class="sxs-lookup"><span data-stu-id="840e6-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="840e6-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/)： 支持 EventSource/EventListener。</span><span class="sxs-lookup"><span data-stu-id="840e6-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="840e6-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)： 到使用 System.Diagnostics.TraceSource.TraceEvent() 的跟踪侦听器的日志。</span><span class="sxs-lookup"><span data-stu-id="840e6-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

<span data-ttu-id="840e6-116">在安装相应的程序包后, 应用程序应创建 LoggerFactory 的单一实例/全局实例。</span><span class="sxs-lookup"><span data-stu-id="840e6-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="840e6-117">例如，使用控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="840e6-117">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="840e6-118">此 singleton/全局实例应然后注册与 EF 核心上`DbContextOptionsBuilder`。</span><span class="sxs-lookup"><span data-stu-id="840e6-118">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="840e6-119">例如：</span><span class="sxs-lookup"><span data-stu-id="840e6-119">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="840e6-120">它是非常重要的应用程序不会创建每个上下文实例的新 ILoggerFactory 实例。</span><span class="sxs-lookup"><span data-stu-id="840e6-120">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="840e6-121">这样将导致内存泄漏和性能低下。</span><span class="sxs-lookup"><span data-stu-id="840e6-121">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="840e6-122">筛选记录的内容，</span><span class="sxs-lookup"><span data-stu-id="840e6-122">Filtering what is logged</span></span>

<span data-ttu-id="840e6-123">筛选所记录内容的最简单方法是在注册 ILoggerProvider 时对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="840e6-123">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="840e6-124">例如：</span><span class="sxs-lookup"><span data-stu-id="840e6-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="840e6-125">在此示例中，日志筛选以返回仅消息：</span><span class="sxs-lookup"><span data-stu-id="840e6-125">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="840e6-126">Microsoft.EntityFrameworkCore.Database.Command 类别中</span><span class="sxs-lookup"><span data-stu-id="840e6-126">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="840e6-127">在信息级别</span><span class="sxs-lookup"><span data-stu-id="840e6-127">at the 'Information' level</span></span>

<span data-ttu-id="840e6-128">对于 EF 核心记录器类别在中定义`DbLoggerCategory`类，以便可以方便地查找类别，但这些解析为简单的字符串。</span><span class="sxs-lookup"><span data-stu-id="840e6-128">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="840e6-129">在基础的日志记录基础结构上的更多详细信息可在[ASP.NET 核心日志记录文档](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="840e6-129">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>