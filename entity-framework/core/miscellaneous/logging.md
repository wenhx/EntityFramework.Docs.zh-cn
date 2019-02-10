---
title: 日志记录-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 0a996403afdbe076b1690c98eeb305b40c4d1f4a
ms.sourcegitcommit: 109a16478de498b65717a6e09be243647e217fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2019
ms.locfileid: "55985569"
---
# <a name="logging"></a><span data-ttu-id="e025f-102">日志记录</span><span class="sxs-lookup"><span data-stu-id="e025f-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="e025f-103">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)。</span><span class="sxs-lookup"><span data-stu-id="e025f-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="e025f-104">ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="e025f-104">ASP.NET Core applications</span></span>

<span data-ttu-id="e025f-105">只要使用 `AddDbContext` 或 `AddDbContextPool`，EF Core 就会自动与 ASP.NET Core 的日志记录机制集成。</span><span class="sxs-lookup"><span data-stu-id="e025f-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="e025f-106">因此，在使用 ASP.NET Core 时，应按 [ASP.NET Core 文档](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)中的说明配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="e025f-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="e025f-107">其他应用程序</span><span class="sxs-lookup"><span data-stu-id="e025f-107">Other applications</span></span>

<span data-ttu-id="e025f-108">EF Core 日志记录当前需要一个本身配置有一个或多个 ILoggerProvider 的 ILoggerFactory。</span><span class="sxs-lookup"><span data-stu-id="e025f-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="e025f-109">以下包中随附有常见提供程序：</span><span class="sxs-lookup"><span data-stu-id="e025f-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="e025f-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/):一个简单的控制台记录器。</span><span class="sxs-lookup"><span data-stu-id="e025f-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="e025f-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/):支持 Azure 应用服务诊断日志和日志流功能。</span><span class="sxs-lookup"><span data-stu-id="e025f-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="e025f-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/):使用 System.Diagnostics.Debug.WriteLine() 调试器监视的日志。</span><span class="sxs-lookup"><span data-stu-id="e025f-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="e025f-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/):记录到 Windows 事件日志。</span><span class="sxs-lookup"><span data-stu-id="e025f-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="e025f-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/):支持 EventSource/EventListener。</span><span class="sxs-lookup"><span data-stu-id="e025f-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="e025f-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/):会向使用 System.Diagnostics.TraceSource.TraceEvent() 的跟踪侦听器。</span><span class="sxs-lookup"><span data-stu-id="e025f-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

> [!NOTE]
> <span data-ttu-id="e025f-116">下面的代码示例使用`ConsoleLoggerProvider`已过时版本 2.2 中的构造函数。</span><span class="sxs-lookup"><span data-stu-id="e025f-116">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2.</span></span> <span data-ttu-id="e025f-117">适当替换已过时的日志记录 Api 将在版本 3.0 中提供。</span><span class="sxs-lookup"><span data-stu-id="e025f-117">Proper replacements for obsolete logging APIs will be available in version 3.0.</span></span> <span data-ttu-id="e025f-118">在此期间，则可以安全地忽略，并禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="e025f-118">In the meantime, it is safe to ignore and suppress the warnings.</span></span>

<span data-ttu-id="e025f-119">安装相应的程序包之后, 该应用程序应创建 LoggerFactory 的单一实例/全局实例。</span><span class="sxs-lookup"><span data-stu-id="e025f-119">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="e025f-120">例如，使用控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="e025f-120">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="e025f-121">然后，应该在 `DbContextOptionsBuilder` 上向 EF Core 注册此单一实例/全局实例。</span><span class="sxs-lookup"><span data-stu-id="e025f-121">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="e025f-122">例如：</span><span class="sxs-lookup"><span data-stu-id="e025f-122">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="e025f-123">应用程序不应为每个上下文实例创建新的 ILoggerFactory 实例，这一点非常重要。</span><span class="sxs-lookup"><span data-stu-id="e025f-123">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="e025f-124">这样做会导致内存泄漏和性能下降。</span><span class="sxs-lookup"><span data-stu-id="e025f-124">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="e025f-125">筛选记录的内容</span><span class="sxs-lookup"><span data-stu-id="e025f-125">Filtering what is logged</span></span>

> [!NOTE]
> <span data-ttu-id="e025f-126">下面的代码示例使用`ConsoleLoggerProvider`已过时版本 2.2 中的构造函数。</span><span class="sxs-lookup"><span data-stu-id="e025f-126">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2.</span></span> <span data-ttu-id="e025f-127">适当替换已过时的日志记录 Api 将在版本 3.0 中提供。</span><span class="sxs-lookup"><span data-stu-id="e025f-127">Proper replacements for obsolete logging APIs will be available in version 3.0.</span></span> <span data-ttu-id="e025f-128">在此期间，则可以安全地忽略，并禁止显示警告。</span><span class="sxs-lookup"><span data-stu-id="e025f-128">In the meantime, it is safe to ignore and suppress the warnings.</span></span>

<span data-ttu-id="e025f-129">筛选所记录内容的最简单方法是在注册 ILoggerProvider 时对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="e025f-129">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="e025f-130">例如：</span><span class="sxs-lookup"><span data-stu-id="e025f-130">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="e025f-131">在此示例中，筛选日志以仅返回以下位置的消息：</span><span class="sxs-lookup"><span data-stu-id="e025f-131">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="e025f-132">在 Microsoft.EntityFrameworkCore.Database.Command 类别中</span><span class="sxs-lookup"><span data-stu-id="e025f-132">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="e025f-133">在信息级别</span><span class="sxs-lookup"><span data-stu-id="e025f-133">at the 'Information' level</span></span>

<span data-ttu-id="e025f-134">对于 EF Core 记录器类别在中定义`DbLoggerCategory`类，以便可以方便地查找类别，但这些解析为简单的字符串。</span><span class="sxs-lookup"><span data-stu-id="e025f-134">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="e025f-135">有关基础日志记录基础结构的更多详细信息，请参阅 [ASP.NET Core 日志记录文档](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)。</span><span class="sxs-lookup"><span data-stu-id="e025f-135">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
