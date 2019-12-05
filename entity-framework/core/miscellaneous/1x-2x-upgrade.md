---
title: 从以前的版本升级到 EF Core 2-EF Core
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: b27c09fdb6210dd7c6aa0c8bc912a8bd183c16b9
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824425"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="e6e25-102">从以前版本的应用程序升级到 EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="e6e25-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="e6e25-103">我们已在2.0 中利用了显著改进现有 Api 和行为的机会。</span><span class="sxs-lookup"><span data-stu-id="e6e25-103">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="e6e25-104">有一些改进可能需要修改现有应用程序代码，但我们认为，对于大多数应用程序而言，这种影响会很低，在大多数情况下，只需重新编译和最少的指导更改即可替换已过时的 Api。</span><span class="sxs-lookup"><span data-stu-id="e6e25-104">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="e6e25-105">更新现有应用程序到 EF Core 2.0 可能还需要：</span><span class="sxs-lookup"><span data-stu-id="e6e25-105">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="e6e25-106">将应用程序的目标 .NET 实现升级到支持 .NET Standard 2.0 的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e6e25-106">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="e6e25-107">有关更多详细信息，请参阅[支持的 .Net 实现](xref:core/platforms/index)。</span><span class="sxs-lookup"><span data-stu-id="e6e25-107">See [Supported .NET Implementations](xref:core/platforms/index) for more details.</span></span>

2. <span data-ttu-id="e6e25-108">标识的提供程序与 EF Core 2.0 兼容的目标数据库。</span><span class="sxs-lookup"><span data-stu-id="e6e25-108">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="e6e25-109">请参阅[EF Core 2.0 需要 2.0 版数据库提供程序](#ef-core-20-requires-a-20-database-provider)下面。</span><span class="sxs-lookup"><span data-stu-id="e6e25-109">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="e6e25-110">升级到 2.0 所有 EF Core 包 （运行时和工具）。</span><span class="sxs-lookup"><span data-stu-id="e6e25-110">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="e6e25-111">请参阅[安装 EF Core](xref:core/get-started/install/index) 有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="e6e25-111">Refer to [Installing EF Core](xref:core/get-started/install/index) for more details.</span></span>

4. <span data-ttu-id="e6e25-112">进行任何必要的代码更改，以弥补本文档其余部分所述的重大更改。</span><span class="sxs-lookup"><span data-stu-id="e6e25-112">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="e6e25-113">ASP.NET Core 现在包括 EF Core</span><span class="sxs-lookup"><span data-stu-id="e6e25-113">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="e6e25-114">面向 ASP.NET Core 2.0 的应用程序可以使用 EF Core 2.0，而不需要第三方数据库提供程序以外的其他依赖项。</span><span class="sxs-lookup"><span data-stu-id="e6e25-114">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="e6e25-115">但是，面向以前版本的 ASP.NET Core 应用程序需要为了使用 EF Core 2.0 升级到 ASP.NET Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="e6e25-115">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="e6e25-116">有关升级到 2.0 的 ASP.NET Core 应用程序的详细信息，请参阅[有关该主题的 ASP.NET Core 文档](/aspnet/core/migration/1x-to-2x/)。</span><span class="sxs-lookup"><span data-stu-id="e6e25-116">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="e6e25-117">在 ASP.NET Core 中获取应用程序服务的新方法</span><span class="sxs-lookup"><span data-stu-id="e6e25-117">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="e6e25-118">已为 2.0 中断 1.x 中使用的 EF Core 的设计时逻辑的方式更新的建议的模式为 ASP.NET Core web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="e6e25-118">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="e6e25-119">以前在设计时，EF Core 会尝试调用`Startup.ConfigureServices`直接才能访问应用程序的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6e25-119">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="e6e25-120">在 ASP.NET Core 2.0 中，配置初始化外部`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="e6e25-120">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="e6e25-121">通常使用 EF Core 应用程序配置中，从访问其连接字符串因此`Startup`本身已不再够用。</span><span class="sxs-lookup"><span data-stu-id="e6e25-121">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="e6e25-122">如果升级 ASP.NET Core 1.x 应用程序，你可能会收到以下错误，使用 EF Core 工具时。</span><span class="sxs-lookup"><span data-stu-id="e6e25-122">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="e6e25-123">未在 "ApplicationContext" 上找到任何无参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="e6e25-123">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="e6e25-124">将无参数构造函数添加到 "ApplicationContext"，或在与 "ApplicationContext" 相同的程序集中添加 "IDesignTimeDbContextFactory&lt;ApplicationContext&gt;" 的实现</span><span class="sxs-lookup"><span data-stu-id="e6e25-124">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="e6e25-125">ASP.NET Core 2.0 的默认模板中已添加新的设计时挂钩。</span><span class="sxs-lookup"><span data-stu-id="e6e25-125">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="e6e25-126">静态`Program.BuildWebHost`方法使 EF Core 以在设计时访问应用程序的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6e25-126">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="e6e25-127">如果要升级 ASP.NET Core 1.x 应用程序，则需要将 `Program` 类更新为类似于以下内容。</span><span class="sxs-lookup"><span data-stu-id="e6e25-127">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

<span data-ttu-id="e6e25-128">强烈建议在将应用程序更新到2.0 时采用这一新模式，这是为了使 Entity Framework Core 迁移等产品功能有效。</span><span class="sxs-lookup"><span data-stu-id="e6e25-128">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="e6e25-129">另一种常见方法是[实现*IDesignTimeDbContextFactory\<TContext >* ](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。</span><span class="sxs-lookup"><span data-stu-id="e6e25-129">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="e6e25-130">已重命名 IDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="e6e25-130">IDbContextFactory renamed</span></span>

<span data-ttu-id="e6e25-131">为了支持不同的应用程序模式并使用户能够更好地控制其 `DbContext` 在设计时的使用方式，我们在过去提供 `IDbContextFactory<TContext>` 接口。</span><span class="sxs-lookup"><span data-stu-id="e6e25-131">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="e6e25-132">EF Core 工具将在设计时发现你的项目中的此接口的实现并使用它来创建`DbContext`对象。</span><span class="sxs-lookup"><span data-stu-id="e6e25-132">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="e6e25-133">此接口具有一个非常通用的名称，此名称误导某些用户尝试重复使用它来执行其他 `DbContext`创建的方案。</span><span class="sxs-lookup"><span data-stu-id="e6e25-133">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="e6e25-134">当 EF 工具在设计时尝试使用其实现并引发 `Update-Database` 或 `dotnet ef database update` 之类的命令失败时，它们被捕获到 "保护"。</span><span class="sxs-lookup"><span data-stu-id="e6e25-134">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="e6e25-135">为了传达此接口的强设计时语义，我们已将其重命名为 `IDesignTimeDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="e6e25-135">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="e6e25-136">对于2.0 版，`IDbContextFactory<TContext>` 仍存在，但被标记为过时。</span><span class="sxs-lookup"><span data-stu-id="e6e25-136">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="e6e25-137">DbContextFactoryOptions 已删除</span><span class="sxs-lookup"><span data-stu-id="e6e25-137">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="e6e25-138">由于上面所述的 ASP.NET Core 2.0 更改，我们发现，`DbContextFactoryOptions`已不再需要在新`IDesignTimeDbContextFactory<TContext>`接口。</span><span class="sxs-lookup"><span data-stu-id="e6e25-138">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="e6e25-139">下面是你应改用的替代方法。</span><span class="sxs-lookup"><span data-stu-id="e6e25-139">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="e6e25-140">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="e6e25-140">DbContextFactoryOptions</span></span> | <span data-ttu-id="e6e25-141">替代项</span><span class="sxs-lookup"><span data-stu-id="e6e25-141">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="e6e25-142">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="e6e25-142">ApplicationBasePath</span></span>     | <span data-ttu-id="e6e25-143">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="e6e25-143">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="e6e25-144">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e6e25-144">ContentRootPath</span></span>         | <span data-ttu-id="e6e25-145">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="e6e25-145">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="e6e25-146">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e6e25-146">EnvironmentName</span></span>         | <span data-ttu-id="e6e25-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="e6e25-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="e6e25-148">设计时工作目录已更改</span><span class="sxs-lookup"><span data-stu-id="e6e25-148">Design-time working directory changed</span></span>

<span data-ttu-id="e6e25-149">ASP.NET Core 2.0 更改也需要使用的工作目录`dotnet ef`为了符合运行你的应用程序时，由 Visual Studio 使用的工作目录。</span><span class="sxs-lookup"><span data-stu-id="e6e25-149">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="e6e25-150">这种情况的一个显而易见的副作用是，SQLite 文件名现在是相对于项目目录的，而不是相对于其使用的输出目录。</span><span class="sxs-lookup"><span data-stu-id="e6e25-150">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="e6e25-151">EF Core 2.0 需要 2.0 版数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="e6e25-151">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="e6e25-152">为使用 EF Core 2.0 我们所做工作许多简化和方式的数据库提供程序中的改进。</span><span class="sxs-lookup"><span data-stu-id="e6e25-152">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="e6e25-153">这意味着 1.0.x 版和 1.1.x 提供程序不会使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="e6e25-153">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="e6e25-154">SQL Server 和 SQLite 提供程序由 EF 团队提供，2.0 版本将作为2.0 版本的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="e6e25-154">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="e6e25-155">正在为2.0 更新面向[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的开源第三方提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6e25-155">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="e6e25-156">对于所有其他提供商，请与提供商联系。</span><span class="sxs-lookup"><span data-stu-id="e6e25-156">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="e6e25-157">日志记录和诊断事件已更改</span><span class="sxs-lookup"><span data-stu-id="e6e25-157">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="e6e25-158">注意：这些更改不应影响大部分应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="e6e25-158">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="e6e25-159">发送到[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)的消息的事件 id 在2.0 中发生了更改。</span><span class="sxs-lookup"><span data-stu-id="e6e25-159">The event IDs for messages sent to an [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) have changed in 2.0.</span></span> <span data-ttu-id="e6e25-160">现在，事件 ID 在 EF Core 代码内具有唯一性。</span><span class="sxs-lookup"><span data-stu-id="e6e25-160">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="e6e25-161">这些消息现在还遵循 MVC 等所用的结构化日志记录的标准模式。</span><span class="sxs-lookup"><span data-stu-id="e6e25-161">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="e6e25-162">记录器类别也已更改。</span><span class="sxs-lookup"><span data-stu-id="e6e25-162">Logger categories have also changed.</span></span> <span data-ttu-id="e6e25-163">现提供通过 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 访问的熟知类别集。</span><span class="sxs-lookup"><span data-stu-id="e6e25-163">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="e6e25-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件现在使用与对应 `ILogger` 消息相同的事件 ID 名称。</span><span class="sxs-lookup"><span data-stu-id="e6e25-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="e6e25-165">事件负载是派生自[EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)的所有名义类型。</span><span class="sxs-lookup"><span data-stu-id="e6e25-165">The event payloads are all nominal types derived from [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span></span>

<span data-ttu-id="e6e25-166">事件 Id、负载类型和类别记录在[CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid)和[RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid)类中。</span><span class="sxs-lookup"><span data-stu-id="e6e25-166">Event IDs, payload types, and categories are documented in the [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) and the [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) classes.</span></span>

<span data-ttu-id="e6e25-167">Id 还会从 Microsoft.entityframeworkcore 移动到新的 Microsoft.entityframeworkcore 命名空间。</span><span class="sxs-lookup"><span data-stu-id="e6e25-167">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="e6e25-168">EF Core 关系元数据 API 更改</span><span class="sxs-lookup"><span data-stu-id="e6e25-168">EF Core relational metadata API changes</span></span>

<span data-ttu-id="e6e25-169">EF Core 2.0 现将对所用的每个不同提供程序生成不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。</span><span class="sxs-lookup"><span data-stu-id="e6e25-169">EF Core 2.0 will now build a different [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) for each different provider being used.</span></span> <span data-ttu-id="e6e25-170">这对应用程序而言通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="e6e25-170">This is usually transparent to the application.</span></span> <span data-ttu-id="e6e25-171">这样一来，就可以简化较低级别的元数据 Api，使对_公共关系元数据概念_的任何访问始终通过调用 `.Relational` 而不是 `.SqlServer`、`.Sqlite`等。例如，1.1. x 代码如下：</span><span class="sxs-lookup"><span data-stu-id="e6e25-171">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="e6e25-172">现在应如下所示：</span><span class="sxs-lookup"><span data-stu-id="e6e25-172">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="e6e25-173">现在可使用扩展方法（而不是 `ForSqlServerToTable`方法），根据当前使用的提供程序来编写条件代码。</span><span class="sxs-lookup"><span data-stu-id="e6e25-173">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="e6e25-174">例如：</span><span class="sxs-lookup"><span data-stu-id="e6e25-174">For example:</span></span>

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="e6e25-175">请注意，此更改仅适用于为_所有_关系提供程序定义的 api/元数据。</span><span class="sxs-lookup"><span data-stu-id="e6e25-175">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="e6e25-176">当 API 和元数据仅特定于单个提供程序时，它保持不变。</span><span class="sxs-lookup"><span data-stu-id="e6e25-176">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="e6e25-177">例如，聚集索引特定于 SQL server，因此仍必须使用 `ForSqlServerIsClustered` 和 `.SqlServer().IsClustered()`。</span><span class="sxs-lookup"><span data-stu-id="e6e25-177">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="e6e25-178">不控制 EF 服务提供程序</span><span class="sxs-lookup"><span data-stu-id="e6e25-178">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="e6e25-179">EF Core 使用内部 `IServiceProvider` （依赖关系注入容器）来实现其内部实现。</span><span class="sxs-lookup"><span data-stu-id="e6e25-179">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="e6e25-180">应用程序应允许 EF Core 以创建和管理在特殊情况下此提供程序除外。</span><span class="sxs-lookup"><span data-stu-id="e6e25-180">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="e6e25-181">请考虑删除对 `UseInternalServiceProvider`的任何调用。</span><span class="sxs-lookup"><span data-stu-id="e6e25-181">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="e6e25-182">如果应用程序需要调用 `UseInternalServiceProvider`，请考虑将[问题存档](https://github.com/aspnet/EntityFramework/Issues)，以便我们能够调查处理方案的其他方法。</span><span class="sxs-lookup"><span data-stu-id="e6e25-182">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="e6e25-183">除非还调用了 `UseInternalServiceProvider`，否则应用程序代码不需要调用 `AddEntityFramework`、`AddEntityFrameworkSqlServer`等。</span><span class="sxs-lookup"><span data-stu-id="e6e25-183">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="e6e25-184">删除对 `AddEntityFramework` 或 `AddEntityFrameworkSqlServer`等的任何现有调用，`AddDbContext` 仍以与以前相同的方式使用。</span><span class="sxs-lookup"><span data-stu-id="e6e25-184">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="e6e25-185">内存中数据库必须命名为</span><span class="sxs-lookup"><span data-stu-id="e6e25-185">In-memory databases must be named</span></span>

<span data-ttu-id="e6e25-186">全局未命名内存中数据库已删除，而所有内存中数据库必须命名为。</span><span class="sxs-lookup"><span data-stu-id="e6e25-186">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="e6e25-187">例如：</span><span class="sxs-lookup"><span data-stu-id="e6e25-187">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="e6e25-188">这将创建/使用名为 "MyDatabase" 的数据库。</span><span class="sxs-lookup"><span data-stu-id="e6e25-188">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="e6e25-189">如果使用相同的名称再次调用 `UseInMemoryDatabase`，则将使用相同的内存中数据库，以允许多个上下文实例共享它。</span><span class="sxs-lookup"><span data-stu-id="e6e25-189">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="e6e25-190">只读 API 更改</span><span class="sxs-lookup"><span data-stu-id="e6e25-190">Read-only API changes</span></span>

<span data-ttu-id="e6e25-191">`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`和 `IsStoreGeneratedAlways` 已弃用并已替换为[BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior)和[AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。</span><span class="sxs-lookup"><span data-stu-id="e6e25-191">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) and [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span></span> <span data-ttu-id="e6e25-192">这些行为适用于任何属性（而不仅是存储生成的属性），并确定在插入到数据库行（`BeforeSaveBehavior`）或更新现有数据库行（`AfterSaveBehavior`）时应如何使用属性的值。</span><span class="sxs-lookup"><span data-stu-id="e6e25-192">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="e6e25-193">标记为[ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) （例如，对于计算列）的属性将默认忽略当前在属性上设置的任何值。</span><span class="sxs-lookup"><span data-stu-id="e6e25-193">Properties marked as [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="e6e25-194">这意味着，无论是否已对所跟踪的实体设置或修改任何值，都将始终获取存储生成的值。</span><span class="sxs-lookup"><span data-stu-id="e6e25-194">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="e6e25-195">可以通过设置其他 `Before\AfterSaveBehavior`来更改此设置。</span><span class="sxs-lookup"><span data-stu-id="e6e25-195">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="e6e25-196">新 ClientSetNull 删除行为</span><span class="sxs-lookup"><span data-stu-id="e6e25-196">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="e6e25-197">在以前的版本中， [DeleteBehavior](/dotnet/api/microsoft.entityframeworkcore.deletebehavior)具有与上下文跟踪的实体的行为，这些实体与 `SetNull` 语义更的已关闭匹配。</span><span class="sxs-lookup"><span data-stu-id="e6e25-197">In previous releases, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="e6e25-198">在 EF Core 2.0 中，新`ClientSetNull`作为的默认值为可选关系引入了行为。</span><span class="sxs-lookup"><span data-stu-id="e6e25-198">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="e6e25-199">此行为具有`SetNull`语义跟踪的实体和`Restrict`创建使用 EF Core 的数据库的行为。</span><span class="sxs-lookup"><span data-stu-id="e6e25-199">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="e6e25-200">在我们的经验中，这是跟踪的实体和数据库的最预期/有用的行为。</span><span class="sxs-lookup"><span data-stu-id="e6e25-200">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="e6e25-201">为可选关系设置时，`DeleteBehavior.Restrict` 现在适用于跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="e6e25-201">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="e6e25-202">已删除提供程序设计时包</span><span class="sxs-lookup"><span data-stu-id="e6e25-202">Provider design-time packages removed</span></span>

<span data-ttu-id="e6e25-203">`Microsoft.EntityFrameworkCore.Relational.Design` 包已删除。</span><span class="sxs-lookup"><span data-stu-id="e6e25-203">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="e6e25-204">它的内容已合并到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design`中。</span><span class="sxs-lookup"><span data-stu-id="e6e25-204">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="e6e25-205">这会传播到提供程序的设计时包。</span><span class="sxs-lookup"><span data-stu-id="e6e25-205">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="e6e25-206">将删除这些包（`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`等），并将其内容合并到主提供程序包中。</span><span class="sxs-lookup"><span data-stu-id="e6e25-206">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="e6e25-207">若要启用`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`在 EF Core 2.0 中，你只需引用单个提供程序包：</span><span class="sxs-lookup"><span data-stu-id="e6e25-207">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
