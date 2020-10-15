---
title: 简单日志记录-EF Core
description: 使用 LogTo 从 EFCore DbContext 进行日志记录
author: ajcvickers
ms.date: 10/03/2020
uid: core/miscellaneous/events/simple-logging
ms.openlocfilehash: 2d671dd9daad16a6431b91419736ec67733e41b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066507"
---
# <a name="simple-logging"></a><span data-ttu-id="c950b-103">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="c950b-103">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="c950b-104">此功能是在 EF Core 5.0 中添加的。</span><span class="sxs-lookup"><span data-stu-id="c950b-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="c950b-105">你可以在 GitHub 上 [查看并下载此文章的示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) 。</span><span class="sxs-lookup"><span data-stu-id="c950b-105">You can [view and download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) on GitHub.</span></span>

<span data-ttu-id="c950b-106">Entity Framework Core (EF Core) 简单日志记录可用于在开发和调试应用程序时轻松获取日志。</span><span class="sxs-lookup"><span data-stu-id="c950b-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="c950b-107">这种形式的日志记录需要最少的配置，而不需要其他 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c950b-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="c950b-108">EF Core 还与 [Microsoft. Extensions. 日志记录](/aspnet/core/fundamentals/logging)，这需要更多配置，但通常更适合用于在生产应用程序中进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c950b-108">EF Core also integrates with [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="c950b-109">配置</span><span class="sxs-lookup"><span data-stu-id="c950b-109">Configuration</span></span>

<span data-ttu-id="c950b-110">通过使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) ，可以从任何类型的应用程序访问 EF Core 日志</span><span class="sxs-lookup"><span data-stu-id="c950b-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="c950b-111">[配置 DbContext 实例](xref:core/miscellaneous/configuring-dbcontext)时。</span><span class="sxs-lookup"><span data-stu-id="c950b-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="c950b-112">此配置通常在的重写中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="c950b-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c950b-113">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="c950b-114">或者， `LogTo` <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 在创建 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 要传递给构造函数的实例时，可以将作为或的一部分来调用 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="c950b-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="c950b-115">当使用 AddDbContext 或将 DbContextOptions 实例传递到 DbContext 构造函数时，仍将调用 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="c950b-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="c950b-116">这使得它成为应用上下文配置的理想位置，而不考虑如何构造 DbContext。</span><span class="sxs-lookup"><span data-stu-id="c950b-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="c950b-117">定向日志</span><span class="sxs-lookup"><span data-stu-id="c950b-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="c950b-118">日志记录到控制台</span><span class="sxs-lookup"><span data-stu-id="c950b-118">Logging to the console</span></span>

<span data-ttu-id="c950b-119">`LogTo` 需要一个 <xref:System.Action%601> 接受字符串的委托。</span><span class="sxs-lookup"><span data-stu-id="c950b-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="c950b-120">EF Core 将为每个生成的日志消息调用此委托和一个字符串。</span><span class="sxs-lookup"><span data-stu-id="c950b-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="c950b-121">然后，将由委托来处理给定的消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="c950b-122"><xref:System.Console.WriteLine%2A?displayProperty=nameWithType>方法通常用于此委托，如上所示。</span><span class="sxs-lookup"><span data-stu-id="c950b-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="c950b-123">这会导致每个日志消息都写入控制台。</span><span class="sxs-lookup"><span data-stu-id="c950b-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="c950b-124">记录到调试窗口</span><span class="sxs-lookup"><span data-stu-id="c950b-124">Logging to the debug window</span></span>

<span data-ttu-id="c950b-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> 可用于将输出发送到 Visual Studio 或其他 Ide 中的 "调试" 窗口。</span><span class="sxs-lookup"><span data-stu-id="c950b-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="c950b-126">在这种情况下，必须使用[Lambda 语法](/dotnet/csharp/language-reference/operators/lambda-expressions)，因为 `Debug` 该类是从发布版本编译而来的。</span><span class="sxs-lookup"><span data-stu-id="c950b-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="c950b-127">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="c950b-128">记录到文件</span><span class="sxs-lookup"><span data-stu-id="c950b-128">Logging to a file</span></span>

<span data-ttu-id="c950b-129">写入文件时，需要 <xref:System.IO.StreamWriter> 为该文件创建一个或类似的。</span><span class="sxs-lookup"><span data-stu-id="c950b-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="c950b-130"><xref:System.IO.StreamWriter.WriteLine%2A>然后，可以使用方法，如以上其他示例中所示。</span><span class="sxs-lookup"><span data-stu-id="c950b-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="c950b-131">请记住，在释放上下文时释放编写器，以确保文件完全关闭。</span><span class="sxs-lookup"><span data-stu-id="c950b-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="c950b-132">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-132">For example:</span></span>

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true); 

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }
    
    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="c950b-133">请考虑使用 [Microsoft Extensions](/aspnet/core/fundamentals/logging) 日志记录日志记录到生产应用程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="c950b-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="c950b-134">获取详细消息</span><span class="sxs-lookup"><span data-stu-id="c950b-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="c950b-135">敏感数据</span><span class="sxs-lookup"><span data-stu-id="c950b-135">Sensitive data</span></span>

<span data-ttu-id="c950b-136">默认情况下，EF Core 不会将任何数据的值包含在异常消息中。</span><span class="sxs-lookup"><span data-stu-id="c950b-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="c950b-137">这是因为此类数据可能是机密的，如果未处理异常，则可能会在生产中显示这些数据。</span><span class="sxs-lookup"><span data-stu-id="c950b-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="c950b-138">但是，在调试时，知道数据值（特别是对于密钥）会非常有用。</span><span class="sxs-lookup"><span data-stu-id="c950b-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="c950b-139">可以通过调用在 EF Core 中启用此功能 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> 。</span><span class="sxs-lookup"><span data-stu-id="c950b-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="c950b-140">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="c950b-141">详细查询异常</span><span class="sxs-lookup"><span data-stu-id="c950b-141">Detailed query exceptions</span></span>

<span data-ttu-id="c950b-142">出于性能方面的考虑，EF Core 不会在 try-catch 块中包装每次调用以读取数据库提供程序的值。</span><span class="sxs-lookup"><span data-stu-id="c950b-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="c950b-143">但是，这有时会导致难以诊断的异常，尤其是在数据库不允许的情况下，当模型不允许时，它们会返回 NULL。</span><span class="sxs-lookup"><span data-stu-id="c950b-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="c950b-144">启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> 将导致 EF 引入这些 try-catch 块，从而提供更详细的错误。</span><span class="sxs-lookup"><span data-stu-id="c950b-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="c950b-145">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="c950b-146">筛选</span><span class="sxs-lookup"><span data-stu-id="c950b-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="c950b-147">日志级别</span><span class="sxs-lookup"><span data-stu-id="c950b-147">Log levels</span></span>

<span data-ttu-id="c950b-148">每 EF Core 日志消息都分配给枚举定义的级别 <xref:Microsoft.Extensions.Logging.LogLevel> 。</span><span class="sxs-lookup"><span data-stu-id="c950b-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="c950b-149">默认情况下，EF Core 简单日志记录在 `Debug` 级别或更高级别包含每条消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="c950b-150">`LogTo` 可向传递较高的最低级别以筛选出某些消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="c950b-151">例如，传递将 `Information` 导致限制为数据库访问和某些内务处理消息的最小日志集。</span><span class="sxs-lookup"><span data-stu-id="c950b-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="c950b-152">特定消息</span><span class="sxs-lookup"><span data-stu-id="c950b-152">Specific messages</span></span>

<span data-ttu-id="c950b-153">每条日志消息都分配有一个 <xref:Microsoft.Extensions.Logging.EventId> 。</span><span class="sxs-lookup"><span data-stu-id="c950b-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="c950b-154">这些 Id 可以从 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> 类或 <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> 类用于关系特定消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="c950b-155">数据库提供程序还可以在类似类中具有特定于提供程序的 Id。</span><span class="sxs-lookup"><span data-stu-id="c950b-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="c950b-156">例如， <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> 对于 SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="c950b-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="c950b-157">`LogTo` 可以配置为仅记录与一个或多个事件 Id 相关联的消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="c950b-158">例如，若要只记录要初始化或释放的上下文的消息，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c950b-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="c950b-159">消息类别</span><span class="sxs-lookup"><span data-stu-id="c950b-159">Message categories</span></span>

<span data-ttu-id="c950b-160">每条日志消息都分配到一个命名的分层记录器类别。</span><span class="sxs-lookup"><span data-stu-id="c950b-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="c950b-161">类别为：</span><span class="sxs-lookup"><span data-stu-id="c950b-161">The categories are:</span></span>

| <span data-ttu-id="c950b-162">Category</span><span class="sxs-lookup"><span data-stu-id="c950b-162">Category</span></span>                                             | <span data-ttu-id="c950b-163">消息</span><span class="sxs-lookup"><span data-stu-id="c950b-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="c950b-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c950b-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="c950b-165">所有 EF Core 消息</span><span class="sxs-lookup"><span data-stu-id="c950b-165">All EF Core messages</span></span>
| <span data-ttu-id="c950b-166">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="c950b-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="c950b-167">所有数据库交互</span><span class="sxs-lookup"><span data-stu-id="c950b-167">All database interactions</span></span>
| <span data-ttu-id="c950b-168">Microsoft.entityframeworkcore。连接</span><span class="sxs-lookup"><span data-stu-id="c950b-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="c950b-169">使用数据库连接</span><span class="sxs-lookup"><span data-stu-id="c950b-169">Uses of a database connection</span></span>
| <span data-ttu-id="c950b-170">Microsoft.entityframeworkcore。</span><span class="sxs-lookup"><span data-stu-id="c950b-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="c950b-171">使用数据库命令</span><span class="sxs-lookup"><span data-stu-id="c950b-171">Uses of a database command</span></span>
| <span data-ttu-id="c950b-172">Microsoft.entityframeworkcore 事务</span><span class="sxs-lookup"><span data-stu-id="c950b-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="c950b-173">使用数据库事务</span><span class="sxs-lookup"><span data-stu-id="c950b-173">Uses of a database transaction</span></span>
| <span data-ttu-id="c950b-174">Microsoft.entityframeworkcore。更新</span><span class="sxs-lookup"><span data-stu-id="c950b-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="c950b-175">保存实体，排除数据库交互</span><span class="sxs-lookup"><span data-stu-id="c950b-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="c950b-176">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="c950b-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="c950b-177">所有模型和元数据交互</span><span class="sxs-lookup"><span data-stu-id="c950b-177">All model and metadata interactions</span></span>
| <span data-ttu-id="c950b-178">Microsoft.entityframeworkcore。验证</span><span class="sxs-lookup"><span data-stu-id="c950b-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="c950b-179">模型验证</span><span class="sxs-lookup"><span data-stu-id="c950b-179">Model validation</span></span>
| <span data-ttu-id="c950b-180">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="c950b-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="c950b-181">查询，排除数据库交互</span><span class="sxs-lookup"><span data-stu-id="c950b-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="c950b-182">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="c950b-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="c950b-183">常规事件，例如上下文创建</span><span class="sxs-lookup"><span data-stu-id="c950b-183">General events, such as context creation</span></span>
| <span data-ttu-id="c950b-184">Microsoft.entityframeworkcore 基架</span><span class="sxs-lookup"><span data-stu-id="c950b-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="c950b-185">数据库反向工程</span><span class="sxs-lookup"><span data-stu-id="c950b-185">Database reverse engineering</span></span>
| <span data-ttu-id="c950b-186">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="c950b-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="c950b-187">迁移</span><span class="sxs-lookup"><span data-stu-id="c950b-187">Migrations</span></span>
| <span data-ttu-id="c950b-188">Microsoft.entityframeworkcore. 更改跟踪</span><span class="sxs-lookup"><span data-stu-id="c950b-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="c950b-189">更改跟踪交互</span><span class="sxs-lookup"><span data-stu-id="c950b-189">Change tracking interactions</span></span>

<span data-ttu-id="c950b-190">`LogTo` 可以配置为仅记录来自一个或多个类别的消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="c950b-191">例如，若要只记录数据库交互：</span><span class="sxs-lookup"><span data-stu-id="c950b-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="c950b-192">请注意， <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> 类提供了一个分层 API 用于查找类别，并避免了对字符串进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="c950b-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="c950b-193">由于类别具有层次结构，因此使用类别的此示例 `Database` 将包括子类别、和的所有消息 `Database.Connection` `Database.Command` `Database.Transaction` 。</span><span class="sxs-lookup"><span data-stu-id="c950b-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="c950b-194">自定义筛选器</span><span class="sxs-lookup"><span data-stu-id="c950b-194">Custom filters</span></span>

<span data-ttu-id="c950b-195">`LogTo` 允许使用自定义筛选器，用于上述筛选选项均不足的情况。</span><span class="sxs-lookup"><span data-stu-id="c950b-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="c950b-196">例如，若要记录任何级别 `Information` 或更高级别的消息，以及用于打开和关闭连接的消息：</span><span class="sxs-lookup"><span data-stu-id="c950b-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="c950b-197">使用自定义筛选器或使用此处所示的任何其他选项进行筛选比 LogTo 委托中的筛选更有效。</span><span class="sxs-lookup"><span data-stu-id="c950b-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="c950b-198">这是因为如果筛选器确定不应记录消息，则不会创建日志消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="message-contents-and-formatting"></a><span data-ttu-id="c950b-199">消息内容和格式设置</span><span class="sxs-lookup"><span data-stu-id="c950b-199">Message contents and formatting</span></span>

<span data-ttu-id="c950b-200">中的默认内容 `LogTo` 是跨多行格式设置的。</span><span class="sxs-lookup"><span data-stu-id="c950b-200">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="c950b-201">第一行包含消息元数据：</span><span class="sxs-lookup"><span data-stu-id="c950b-201">The first line contains message metadata:</span></span>

* <span data-ttu-id="c950b-202"><xref:Microsoft.Extensions.Logging.LogLevel>作为四字符前缀</span><span class="sxs-lookup"><span data-stu-id="c950b-202">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="c950b-203">为当前区域性设置格式的本地时间戳</span><span class="sxs-lookup"><span data-stu-id="c950b-203">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="c950b-204"><xref:Microsoft.Extensions.Logging.EventId>可以复制/粘贴以便从或其他某个类获取成员的窗体中的 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> `EventId` ，以及原始 ID 值</span><span class="sxs-lookup"><span data-stu-id="c950b-204">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="c950b-205">事件类别，如上所述。</span><span class="sxs-lookup"><span data-stu-id="c950b-205">The event category, as described above.</span></span>

<span data-ttu-id="c950b-206">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-206">For example:</span></span>

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) 
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committed transaction.
```

<span data-ttu-id="c950b-207">可以通过从[DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)传递值来自定义此内容</span><span class="sxs-lookup"><span data-stu-id="c950b-207">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="c950b-208">，如以下部分所示。</span><span class="sxs-lookup"><span data-stu-id="c950b-208">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="c950b-209">请考虑使用 [Microsoft Extensions](/aspnet/core/fundamentals/logging) 日志来更好地控制日志格式。</span><span class="sxs-lookup"><span data-stu-id="c950b-209">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="c950b-210">使用 UTC 时间</span><span class="sxs-lookup"><span data-stu-id="c950b-210">Using UTC time</span></span>

<span data-ttu-id="c950b-211">默认情况下，timestamnps 在调试时专用于本地消耗。</span><span class="sxs-lookup"><span data-stu-id="c950b-211">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="c950b-212">使用 `DbContextLoggerOptions.DefaultWithUtcTime` 可以改为使用与区域性无关的 UTC 时间戳，但保留所有其他内容。</span><span class="sxs-lookup"><span data-stu-id="c950b-212">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="c950b-213">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-213">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="c950b-214">此示例将生成以下日志格式：</span><span class="sxs-lookup"><span data-stu-id="c950b-214">This example results in the following log formatting:</span></span>

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) 
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committed transaction.
```

### <a name="single-line-logging"></a><span data-ttu-id="c950b-215">单行日志记录</span><span class="sxs-lookup"><span data-stu-id="c950b-215">Single line logging</span></span>

<span data-ttu-id="c950b-216">有时，每条日志消息只获取一行很有用。</span><span class="sxs-lookup"><span data-stu-id="c950b-216">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="c950b-217">这可以由启用 `DbContextLoggerOptions.SingleLine` 。</span><span class="sxs-lookup"><span data-stu-id="c950b-217">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="c950b-218">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-218">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="c950b-219">此示例将生成以下日志格式：</span><span class="sxs-lookup"><span data-stu-id="c950b-219">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="c950b-220">其他内容选项</span><span class="sxs-lookup"><span data-stu-id="c950b-220">Other content options</span></span>

<span data-ttu-id="c950b-221">[DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)中的其他标志</span><span class="sxs-lookup"><span data-stu-id="c950b-221">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="c950b-222">可用于修整日志中包含的元数据量。</span><span class="sxs-lookup"><span data-stu-id="c950b-222">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="c950b-223">这对于单行日志记录很有用。</span><span class="sxs-lookup"><span data-stu-id="c950b-223">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="c950b-224">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-224">For example:</span></span>

<span data-ttu-id="c950b-225">此示例将生成以下日志格式：</span><span class="sxs-lookup"><span data-stu-id="c950b-225">This example results in the following log formatting:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="c950b-226">从 EF6 移动</span><span class="sxs-lookup"><span data-stu-id="c950b-226">Moving from EF6</span></span>

<span data-ttu-id="c950b-227">EF Core 简单日志记录 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> 在以下两个重要方面不同于 EF6：</span><span class="sxs-lookup"><span data-stu-id="c950b-227">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="c950b-228">日志消息不仅限于数据库交互</span><span class="sxs-lookup"><span data-stu-id="c950b-228">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="c950b-229">必须在上下文初始化时配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c950b-229">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="c950b-230">对于第一个差异，可以使用上述筛选来限制要记录的消息。</span><span class="sxs-lookup"><span data-stu-id="c950b-230">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="c950b-231">第二个差异是指在不需要日志消息时不生成日志消息，从而提高性能的故意更改。</span><span class="sxs-lookup"><span data-stu-id="c950b-231">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="c950b-232">但是，仍然可以通过 `Log` 在上创建一个属性 `DbContext` ，然后仅在设置该属性时使用该属性，来使 EF6 的行为类似。</span><span class="sxs-lookup"><span data-stu-id="c950b-232">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="c950b-233">例如：</span><span class="sxs-lookup"><span data-stu-id="c950b-233">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
