---
title: 从先前版本升级到 EF Core 2 - EF Core
description: 升级到 Entity Framework Core 2.0 的说明和注意事项
author: ajcvickers
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: 5054661d308e7ea6acd433981dfb2af6026b7765
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430087"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="98cb9-103">将应用程序从先前版本升级到 EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="98cb9-103">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="98cb9-104">我们利用这个机会在 2.0 中极大地改进了现有 API 和行为。</span><span class="sxs-lookup"><span data-stu-id="98cb9-104">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="98cb9-105">有一些改进需要修改现有应用程序代码，不过我们认为这对大多数应用程序的影响很小，在大多数情况下只需重新编译和进行极少的指导更改即可替换过时的 API。</span><span class="sxs-lookup"><span data-stu-id="98cb9-105">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="98cb9-106">将现有应用程序更新到 EF Core 2.0 可能需要完成以下事项：</span><span class="sxs-lookup"><span data-stu-id="98cb9-106">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="98cb9-107">将应用程序的目标 .NET 实现升级到支持 .NET Standard 2.0 的实现。</span><span class="sxs-lookup"><span data-stu-id="98cb9-107">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="98cb9-108">请参阅[支持的 .NET 实现](xref:core/miscellaneous/platforms)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="98cb9-108">See [Supported .NET Implementations](xref:core/miscellaneous/platforms) for more details.</span></span>

2. <span data-ttu-id="98cb9-109">确定与 EF Core 2.0 兼容的目标数据库的提供程序。</span><span class="sxs-lookup"><span data-stu-id="98cb9-109">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="98cb9-110">请参阅下面的 [EF Core 2.0 需要 2.0 数据库提供程序](#ef-core-20-requires-a-20-database-provider)。</span><span class="sxs-lookup"><span data-stu-id="98cb9-110">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="98cb9-111">将所有 EF Core 包（运行时和工具）升级为 2.0。</span><span class="sxs-lookup"><span data-stu-id="98cb9-111">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="98cb9-112">请参阅[安装 EF Core](xref:core/get-started/overview/install) 了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="98cb9-112">Refer to [Installing EF Core](xref:core/get-started/overview/install) for more details.</span></span>

4. <span data-ttu-id="98cb9-113">进行任何必要的代码更改，以补充本文档其余部分所述的中断性变更。</span><span class="sxs-lookup"><span data-stu-id="98cb9-113">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="98cb9-114">ASP.NET Core 目前包括 EF Core</span><span class="sxs-lookup"><span data-stu-id="98cb9-114">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="98cb9-115">面向 ASP.NET Core 2.0 的应用程序可以使用 EF Core 2.0，而不需要第三方数据库提供程序以外的其他依赖项。</span><span class="sxs-lookup"><span data-stu-id="98cb9-115">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="98cb9-116">但是，面向旧版 ASP.NET Core 的应用程序必须升级到 ASP.NET Core 2.0，才能使用 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="98cb9-116">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="98cb9-117">有关将 ASP.NET Core 应用程序升级到 2.0 的更多详细信息，请参阅[关于该主题的 ASP.NET Core 文档](/aspnet/core/migration/1x-to-2x/)。</span><span class="sxs-lookup"><span data-stu-id="98cb9-117">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="98cb9-118">在 ASP.NET Core 中获取应用程序服务的新方法</span><span class="sxs-lookup"><span data-stu-id="98cb9-118">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="98cb9-119">针对 ASP.NET Core Web 应用程序的建议模式已针对 2.0 进行了更新，这打破了 EF Core 在 1.x 中使用的设计时逻辑。</span><span class="sxs-lookup"><span data-stu-id="98cb9-119">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="98cb9-120">以前在设计时，EF Core 会尝试直接调用 `Startup.ConfigureServices` 以访问应用程序的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="98cb9-120">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="98cb9-121">在 ASP.NET Core 2.0 中，将在 `Startup` 类之外初始化配置。</span><span class="sxs-lookup"><span data-stu-id="98cb9-121">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="98cb9-122">使用 EF Core 的应用程序通常从配置中访问其连接字符串，因此单是 `Startup` 就已经不够了。</span><span class="sxs-lookup"><span data-stu-id="98cb9-122">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="98cb9-123">如果升级 ASP.NET Core 1.x 应用程序，则使用 EF Core 工具时可能会收到以下错误。</span><span class="sxs-lookup"><span data-stu-id="98cb9-123">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="98cb9-124">未在“ApplicationContext”上找到任何无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="98cb9-124">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="98cb9-125">向“ApplicationContext”添加一个无参数构造函数，或者在与“ApplicationContext”所在的程序集中添加“IDesignTimeDbContextFactory&lt;ApplicationContext&gt;”的实现</span><span class="sxs-lookup"><span data-stu-id="98cb9-125">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="98cb9-126">ASP.NET Core 2.0 的默认模板中添加了新的设计时挂钩。</span><span class="sxs-lookup"><span data-stu-id="98cb9-126">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="98cb9-127">静态 `Program.BuildWebHost` 方法使 EF Core 能够在设计时访问应用程序的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="98cb9-127">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="98cb9-128">如果要升级 ASP.NET Core 1.x 应用程序，则需要将 `Program` 类更新为以下相似内容。</span><span class="sxs-lookup"><span data-stu-id="98cb9-128">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

```csharp
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

<span data-ttu-id="98cb9-129">将应用程序更新到 2.0 时，强烈建议采用此新模式，Entity Framework Core 迁移等产品功能需要此模式才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="98cb9-129">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="98cb9-130">另一种常见的替代方法是[实现 IDesignTimeDbContextFactory\<TContext>](xref:core/cli/dbcontext-creation#from-a-design-time-factory)。</span><span class="sxs-lookup"><span data-stu-id="98cb9-130">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="98cb9-131">已重命名 IDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="98cb9-131">IDbContextFactory renamed</span></span>

<span data-ttu-id="98cb9-132">为了支持不同的应用程序模式并使用户能够更好地控制其 `DbContext` 在设计时的使用方式，我们过去提供了 `IDbContextFactory<TContext>` 接口。</span><span class="sxs-lookup"><span data-stu-id="98cb9-132">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="98cb9-133">在设计时，EF Core 工具将在项目中发现此接口的实现，并使用它来创建 `DbContext` 对象。</span><span class="sxs-lookup"><span data-stu-id="98cb9-133">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="98cb9-134">此接口具有一个非常通用的名称，会误导某些用户尝试将其重复用于其他创建 `DbContext` 的场景。</span><span class="sxs-lookup"><span data-stu-id="98cb9-134">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="98cb9-135">当 EF 工具尝试在设计时使用他们的实现并导致 `Update-Database` 或 `dotnet ef database update` 等命令失败时，他们毫无准备。</span><span class="sxs-lookup"><span data-stu-id="98cb9-135">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="98cb9-136">为了传达此接口的强设计时语义，我们已将其重命名为 `IDesignTimeDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="98cb9-136">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="98cb9-137">对于 2.0 版本，`IDbContextFactory<TContext>` 仍存在，但被标记为已过时。</span><span class="sxs-lookup"><span data-stu-id="98cb9-137">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="98cb9-138">已删除 DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="98cb9-138">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="98cb9-139">由于上述 ASP.NET Core 2.0 更改，我们发现新的 `IDesignTimeDbContextFactory<TContext>` 接口不再需要 `DbContextFactoryOptions`。</span><span class="sxs-lookup"><span data-stu-id="98cb9-139">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="98cb9-140">下面是你应使用的替代方法。</span><span class="sxs-lookup"><span data-stu-id="98cb9-140">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="98cb9-141">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="98cb9-141">DbContextFactoryOptions</span></span> | <span data-ttu-id="98cb9-142">替代方法</span><span class="sxs-lookup"><span data-stu-id="98cb9-142">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="98cb9-143">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="98cb9-143">ApplicationBasePath</span></span>     | <span data-ttu-id="98cb9-144">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="98cb9-144">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="98cb9-145">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="98cb9-145">ContentRootPath</span></span>         | <span data-ttu-id="98cb9-146">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="98cb9-146">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="98cb9-147">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="98cb9-147">EnvironmentName</span></span>         | <span data-ttu-id="98cb9-148">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="98cb9-148">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="98cb9-149">已更改设计时工作目录</span><span class="sxs-lookup"><span data-stu-id="98cb9-149">Design-time working directory changed</span></span>

<span data-ttu-id="98cb9-150">ASP.NET Core 2.0 更改还要求 `dotnet ef` 使用的工作目录与 Visual Studio 在运行应用程序时使用的工作目录保持一致。</span><span class="sxs-lookup"><span data-stu-id="98cb9-150">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="98cb9-151">此要求的一个明显副作用是，SQLite 文件名现在与项目目录相关，而不是像之前那样与输出目录相关。</span><span class="sxs-lookup"><span data-stu-id="98cb9-151">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="98cb9-152">EF Core 2.0 需要 2.0 数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="98cb9-152">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="98cb9-153">对于 EF Core 2.0，我们在数据库提供程序的工作方式方面进行了许多简化和改进。</span><span class="sxs-lookup"><span data-stu-id="98cb9-153">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="98cb9-154">这意味着，1.0.x 和 1.1.x 提供程序将不再适用于 EF Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="98cb9-154">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="98cb9-155">SQL Server 和 SQLite 提供程序由 EF 团队提供，2.0 版本将作为 2.0 发布的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="98cb9-155">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="98cb9-156">[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL) 和 [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 的开源第三方提供程序已针对 2.0 进行了更新。</span><span class="sxs-lookup"><span data-stu-id="98cb9-156">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="98cb9-157">对于所有其他提供程序，请与提供程序作者联系。</span><span class="sxs-lookup"><span data-stu-id="98cb9-157">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="98cb9-158">日志记录和诊断事件已更改</span><span class="sxs-lookup"><span data-stu-id="98cb9-158">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="98cb9-159">注意：这些更改不应影响大部分应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="98cb9-159">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="98cb9-160">发送给 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 的消息的事件 ID 在 2.0 中已更改。</span><span class="sxs-lookup"><span data-stu-id="98cb9-160">The event IDs for messages sent to an [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) have changed in 2.0.</span></span> <span data-ttu-id="98cb9-161">现在，事件 ID 在 EF Core 代码内具有唯一性。</span><span class="sxs-lookup"><span data-stu-id="98cb9-161">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="98cb9-162">这些消息现在还遵循 MVC 等所用的结构化日志记录的标准模式。</span><span class="sxs-lookup"><span data-stu-id="98cb9-162">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="98cb9-163">记录器类别也已更改。</span><span class="sxs-lookup"><span data-stu-id="98cb9-163">Logger categories have also changed.</span></span> <span data-ttu-id="98cb9-164">现提供通过 [DbLoggerCategory](https://github.com/dotnet/efcore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 访问的熟知类别集。</span><span class="sxs-lookup"><span data-stu-id="98cb9-164">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/dotnet/efcore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="98cb9-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件现使用与相应 `ILogger` 消息相同的事件 ID 名称。</span><span class="sxs-lookup"><span data-stu-id="98cb9-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="98cb9-166">事件负载是派生自 [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata) 的所有名义类型。</span><span class="sxs-lookup"><span data-stu-id="98cb9-166">The event payloads are all nominal types derived from [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span></span>

<span data-ttu-id="98cb9-167">事件 ID、负载类型和类别记录在 [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) 和 [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) 类中。</span><span class="sxs-lookup"><span data-stu-id="98cb9-167">Event IDs, payload types, and categories are documented in the [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) and the [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) classes.</span></span>

<span data-ttu-id="98cb9-168">ID 也从 Microsoft.EntityFrameworkCore.Infrastructure 移动到了新的 Microsoft.EntityFrameworkCore.Diagnostics 命名空间。</span><span class="sxs-lookup"><span data-stu-id="98cb9-168">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="98cb9-169">EF Core 关系元数据 API 更改</span><span class="sxs-lookup"><span data-stu-id="98cb9-169">EF Core relational metadata API changes</span></span>

<span data-ttu-id="98cb9-170">EF Core 2.0 现将对所用的每个不同提供程序生成不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。</span><span class="sxs-lookup"><span data-stu-id="98cb9-170">EF Core 2.0 will now build a different [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) for each different provider being used.</span></span> <span data-ttu-id="98cb9-171">这对应用程序而言通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="98cb9-171">This is usually transparent to the application.</span></span> <span data-ttu-id="98cb9-172">这有助于简化较低级别的元数据 API，从而始终通过调用 `.Relational`（而不是 `.SqlServer`、`.Sqlite` 等）来访问常见关系元数据概念。例如，1.1.x 代码如下：</span><span class="sxs-lookup"><span data-stu-id="98cb9-172">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="98cb9-173">现在应这样编写：</span><span class="sxs-lookup"><span data-stu-id="98cb9-173">Should now be written like this:</span></span>

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="98cb9-174">现在可使用扩展方法（而不是 `ForSqlServerToTable` 方法）基于当前使用的提供程序来编写条件代码。</span><span class="sxs-lookup"><span data-stu-id="98cb9-174">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="98cb9-175">例如：</span><span class="sxs-lookup"><span data-stu-id="98cb9-175">For example:</span></span>

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="98cb9-176">请注意，此更改仅适用于为所有关系提供程序定义的 API/元数据。</span><span class="sxs-lookup"><span data-stu-id="98cb9-176">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="98cb9-177">仅特定于单个提供程序时，API 和元数据保持不变。</span><span class="sxs-lookup"><span data-stu-id="98cb9-177">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="98cb9-178">例如，聚集索引特定于 SQL Server，因此仍必须使用 `ForSqlServerIsClustered` 和 `.SqlServer().IsClustered()`。</span><span class="sxs-lookup"><span data-stu-id="98cb9-178">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="98cb9-179">不控制 EF 服务提供程序</span><span class="sxs-lookup"><span data-stu-id="98cb9-179">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="98cb9-180">EF Core 将内部 `IServiceProvider`（依赖关系注入容器）用于其内部实现。</span><span class="sxs-lookup"><span data-stu-id="98cb9-180">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="98cb9-181">应用程序应允许 EF Core 创建和管理此提供程序，特殊情况除外。</span><span class="sxs-lookup"><span data-stu-id="98cb9-181">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="98cb9-182">强烈建议删除对 `UseInternalServiceProvider` 的任何调用。</span><span class="sxs-lookup"><span data-stu-id="98cb9-182">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="98cb9-183">如果某应用程序确实需要调用 `UseInternalServiceProvider`，则请考虑[提交问题](https://github.com/dotnet/efcore/Issues)，这样我们就可以研究处理此情况的其他方法。</span><span class="sxs-lookup"><span data-stu-id="98cb9-183">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/dotnet/efcore/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="98cb9-184">除非同时调用了 `UseInternalServiceProvider`，否则应用程序代码不需要调用 `AddEntityFramework`、`AddEntityFrameworkSqlServer` 等。</span><span class="sxs-lookup"><span data-stu-id="98cb9-184">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="98cb9-185">删除对 `AddEntityFramework` 或 `AddEntityFrameworkSqlServer` 等的任何现有调用之后，仍应按照之前的相同方式来使用 `AddDbContext`。</span><span class="sxs-lookup"><span data-stu-id="98cb9-185">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="98cb9-186">必须对内存中数据库进行命名</span><span class="sxs-lookup"><span data-stu-id="98cb9-186">In-memory databases must be named</span></span>

<span data-ttu-id="98cb9-187">全局未命名内存中数据库已删除，而且必须对所有内存中数据库进行命名。</span><span class="sxs-lookup"><span data-stu-id="98cb9-187">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="98cb9-188">例如：</span><span class="sxs-lookup"><span data-stu-id="98cb9-188">For example:</span></span>

```csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="98cb9-189">这将创建/使用名为“MyDatabase”的数据库。</span><span class="sxs-lookup"><span data-stu-id="98cb9-189">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="98cb9-190">如果使用同一名称再次调用 `UseInMemoryDatabase`，则将使用同一内存中数据库，以允许多个上下文实例共享它。</span><span class="sxs-lookup"><span data-stu-id="98cb9-190">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="98cb9-191">只读 API 更改</span><span class="sxs-lookup"><span data-stu-id="98cb9-191">Read-only API changes</span></span>

<span data-ttu-id="98cb9-192">`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave` 和 `IsStoreGeneratedAlways` 已弃用，并已替换为 [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) 和 [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。</span><span class="sxs-lookup"><span data-stu-id="98cb9-192">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) and [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span></span> <span data-ttu-id="98cb9-193">这些行为适用于任何属性（不仅仅是存储生成的属性），可确定在插入到数据库行 (`BeforeSaveBehavior`) 或更新现有数据库行 (`AfterSaveBehavior`) 时应如何使用属性的值。</span><span class="sxs-lookup"><span data-stu-id="98cb9-193">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="98cb9-194">标记为 [OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated)（例如对于计算列）的属性将默认忽略当前在属性上设置的任何值。</span><span class="sxs-lookup"><span data-stu-id="98cb9-194">Properties marked as [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="98cb9-195">这意味着，无论是否在跟踪的实体上设置或修改了任何值，都将始终获得存储生成的值。</span><span class="sxs-lookup"><span data-stu-id="98cb9-195">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="98cb9-196">通过设置其他 `Before\AfterSaveBehavior` 参数可以对此进行更改。</span><span class="sxs-lookup"><span data-stu-id="98cb9-196">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="98cb9-197">新的 ClientSetNull 删除行为</span><span class="sxs-lookup"><span data-stu-id="98cb9-197">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="98cb9-198">在以前的版本中，[DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) 具有针对上下文所跟踪实体的行为，该行为更接近于 `SetNull` 语义。</span><span class="sxs-lookup"><span data-stu-id="98cb9-198">In previous releases, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="98cb9-199">在 EF Core 2.0 中，引入了新的 `ClientSetNull` 行为作为可选关系的默认行为。</span><span class="sxs-lookup"><span data-stu-id="98cb9-199">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="98cb9-200">此行为具有针对跟踪的实体的 `SetNull` 语义和针对使用 EF Core 创建的数据库的 `Restrict` 行为。</span><span class="sxs-lookup"><span data-stu-id="98cb9-200">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="98cb9-201">根据我们的经验，这些是针对跟踪的实体和数据库的最需要/最有用的行为。</span><span class="sxs-lookup"><span data-stu-id="98cb9-201">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="98cb9-202">对可选关系进行设置时，现在 `DeleteBehavior.Restrict` 可用于跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="98cb9-202">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="98cb9-203">已删除提供程序设计时包</span><span class="sxs-lookup"><span data-stu-id="98cb9-203">Provider design-time packages removed</span></span>

<span data-ttu-id="98cb9-204">已删除 `Microsoft.EntityFrameworkCore.Relational.Design` 包。</span><span class="sxs-lookup"><span data-stu-id="98cb9-204">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="98cb9-205">其内容已合并到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design` 中。</span><span class="sxs-lookup"><span data-stu-id="98cb9-205">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="98cb9-206">这会传播到提供程序设计时包。</span><span class="sxs-lookup"><span data-stu-id="98cb9-206">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="98cb9-207">这些包（`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design` 等）已删除，其内容已合并到主提供程序包中。</span><span class="sxs-lookup"><span data-stu-id="98cb9-207">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="98cb9-208">若要在 EF Core 2.0 中启用 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`，只需引用一个提供程序包：</span><span class="sxs-lookup"><span data-stu-id="98cb9-208">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
