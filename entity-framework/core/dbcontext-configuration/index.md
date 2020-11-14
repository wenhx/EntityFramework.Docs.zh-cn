---
title: 配置 DbContext - EF Core
description: 使用 Entity Framework Core 配置 DbContext 的策略
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431116"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="f6943-103">配置 DbContext</span><span class="sxs-lookup"><span data-stu-id="f6943-103">Configuring a DbContext</span></span>

<span data-ttu-id="f6943-104">本文介绍了通过 `DbContextOptions` 配置 `DbContext` 的基本模式，以使用特定的 EF Core 提供程序和可选行为连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="f6943-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="f6943-105">设计时 DbContext 配置</span><span class="sxs-lookup"><span data-stu-id="f6943-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="f6943-106">EF Core 设计时工具（如[迁移](xref:core/managing-schemas/migrations/index)）需要能够发现并创建 `DbContext` 类型的工作实例，以收集有关应用程序的实体类型及其如何映射到数据库架构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f6943-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="f6943-107">只要该工具可以通过与在运行时的配置方式类似的方式轻松地创建 `DbContext`，就可以自动执行此过程。</span><span class="sxs-lookup"><span data-stu-id="f6943-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="f6943-108">虽然向 `DbContext` 提供必要配置信息的任何模式都可以在运行时正常运行，但在设计时需要使用 `DbContext` 的工具只能使用有限数量的模式。</span><span class="sxs-lookup"><span data-stu-id="f6943-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="f6943-109">[设计时上下文创建](xref:core/cli/dbcontext-creation)部分中包含更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="f6943-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="f6943-110">配置 DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="f6943-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="f6943-111">`DbContext` 必须具有 `DbContextOptions` 的实例才能执行任何工作。</span><span class="sxs-lookup"><span data-stu-id="f6943-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="f6943-112">`DbContextOptions` 实例包含如下配置信息：</span><span class="sxs-lookup"><span data-stu-id="f6943-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="f6943-113">要使用的数据库提供程序，通常通过调用 `UseSqlServer` 或 `UseSqlite` 等方法进行选择。</span><span class="sxs-lookup"><span data-stu-id="f6943-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="f6943-114">这些扩展方法需要相应的提供程序包，如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite`。</span><span class="sxs-lookup"><span data-stu-id="f6943-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="f6943-115">这些方法在 `Microsoft.EntityFrameworkCore` 命名空间中定义。</span><span class="sxs-lookup"><span data-stu-id="f6943-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="f6943-116">数据库实例的任何必要的连接字符串或标识符，通常作为参数传递给上面提到的提供程序选择方法</span><span class="sxs-lookup"><span data-stu-id="f6943-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="f6943-117">任何提供程序级别的可选行为选择器，通常还在对提供程序选择方法的调用中链接</span><span class="sxs-lookup"><span data-stu-id="f6943-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="f6943-118">任何常规 EF Core 行为选择器，通常在提供程序选择器方法之后或之前链接</span><span class="sxs-lookup"><span data-stu-id="f6943-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="f6943-119">下面的示例将 `DbContextOptions` 配置为使用 SQL Server 提供程序、包含在 `connectionString` 变量中的连接、提供程序级别的命令超时以及使所有查询默认在 `DbContext` [非跟踪](xref:core/querying/tracking#no-tracking-queries)中执行的 EF Core 行为选择器：</span><span class="sxs-lookup"><span data-stu-id="f6943-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="f6943-120">上面提到的提供程序选择器方法和其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f6943-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="f6943-121">若要访问这些扩展方法，你可能需要在范围内具有命名空间（通常为 `Microsoft.EntityFrameworkCore`），并在项目中包含附加包依赖项。</span><span class="sxs-lookup"><span data-stu-id="f6943-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="f6943-122">可以通过重写 `OnConfiguring` 方法或通过构造函数参数从外部向 `DbContext` 提供 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="f6943-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="f6943-123">如果同时使用这两种方式，则 `OnConfiguring` 最后应用，并且可以覆盖提供给构造函数参数的选项。</span><span class="sxs-lookup"><span data-stu-id="f6943-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="f6943-124">构造函数参数</span><span class="sxs-lookup"><span data-stu-id="f6943-124">Constructor argument</span></span>

<span data-ttu-id="f6943-125">构造函数可以仅接受 `DbContextOptions`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="f6943-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> <span data-ttu-id="f6943-126">DbContext 的基本构造函数还接受 `DbContextOptions` 的非泛型版本，但对于具有多个上下文类型的应用程序，不建议使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="f6943-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="f6943-127">应用程序现在可以在实例化上下文时传递 `DbContextOptions`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="f6943-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="f6943-128">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="f6943-128">OnConfiguring</span></span>

<span data-ttu-id="f6943-129">还可以在上下文本身中初始化 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="f6943-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="f6943-130">虽然你可以将此技术用于基本配置，但通常仍需要从外部获取某些配置详细信息，例如数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="f6943-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="f6943-131">可以使用配置 API 或任何其他方法实现此目的。</span><span class="sxs-lookup"><span data-stu-id="f6943-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="f6943-132">若要在上下文中初始化 `DbContextOptions`，请重写 `OnConfiguring` 方法，并对提供的 `DbContextOptionsBuilder` 调用方法：</span><span class="sxs-lookup"><span data-stu-id="f6943-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="f6943-133">应用程序无需将任何内容传递给构造函数，即可实例化此类上下文：</span><span class="sxs-lookup"><span data-stu-id="f6943-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="f6943-134">此方法不能用于测试，除非测试面向完整数据库。</span><span class="sxs-lookup"><span data-stu-id="f6943-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="f6943-135">将 DbContext 与依赖关系注入一起使用</span><span class="sxs-lookup"><span data-stu-id="f6943-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="f6943-136">EF Core 支持将 `DbContext` 与依赖关系注入容器一起使用。</span><span class="sxs-lookup"><span data-stu-id="f6943-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="f6943-137">可以使用 `AddDbContext<TContext>` 方法将 DbContext 类型添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="f6943-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="f6943-138">`AddDbContext<TContext>` 将使 DbContext 类型 `TContext` 和相应的 `DbContextOptions<TContext>` 可用于从服务容器中注入。</span><span class="sxs-lookup"><span data-stu-id="f6943-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="f6943-139">有关依赖关系注入的其他信息，请参阅下面的[进一步阅读](#more-reading)。</span><span class="sxs-lookup"><span data-stu-id="f6943-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="f6943-140">将 `DbContext` 添加到依赖关系注入：</span><span class="sxs-lookup"><span data-stu-id="f6943-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="f6943-141">这需要将[构造函数参数](#constructor-argument)添加到接受 `DbContextOptions<TContext>` 的 DbContext 类型。</span><span class="sxs-lookup"><span data-stu-id="f6943-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="f6943-142">上下文代码：</span><span class="sxs-lookup"><span data-stu-id="f6943-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="f6943-143">应用程序代码（在 ASP.NET Core 中）：</span><span class="sxs-lookup"><span data-stu-id="f6943-143">Application code (in ASP.NET Core):</span></span>

```csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="f6943-144">应用程序代码（直接使用 ServiceProvider，不太常见）：</span><span class="sxs-lookup"><span data-stu-id="f6943-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="f6943-145">避免 DbContext 线程处理问题</span><span class="sxs-lookup"><span data-stu-id="f6943-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="f6943-146">Entity Framework Core 不支持在同一 `DbContext` 实例上运行多个并行操作。</span><span class="sxs-lookup"><span data-stu-id="f6943-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="f6943-147">这包括异步查询的并行执行以及从多个线程进行的任何显式并发使用。</span><span class="sxs-lookup"><span data-stu-id="f6943-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="f6943-148">因此，始终立即 `await` 异步调用，或对并行执行的操作使用单独的 `DbContext` 实例。</span><span class="sxs-lookup"><span data-stu-id="f6943-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="f6943-149">当 EF Core 检测到尝试同时使用 `DbContext` 实例的情况时，你将看到 `InvalidOperationException`，其中包含类似于以下内容的消息：</span><span class="sxs-lookup"><span data-stu-id="f6943-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="f6943-150">在上一个操作完成之前，第二个操作已在此上下文中启动。</span><span class="sxs-lookup"><span data-stu-id="f6943-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="f6943-151">这通常是由使用同一个 DbContext 实例的不同线程引起的，但不保证实例成员是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="f6943-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="f6943-152">检测不到并发访问时，可能会导致未定义的行为、应用程序崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="f6943-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="f6943-153">一些常见错误可能会无意中导致并发访问同一 `DbContext` 实例：</span><span class="sxs-lookup"><span data-stu-id="f6943-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="f6943-154">在对同一 DbContext 启动任何其他操作之前，忘记等待异步操作完成</span><span class="sxs-lookup"><span data-stu-id="f6943-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="f6943-155">使用异步方法，EF Core 可以启动以非阻挡式访问数据库的操作。</span><span class="sxs-lookup"><span data-stu-id="f6943-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="f6943-156">但是，如果调用方不等待其中一个方法完成，而是继续对 `DbContext` 执行其他操作，则 `DbContext` 的状态可能会（并且很可能会）损坏。</span><span class="sxs-lookup"><span data-stu-id="f6943-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="f6943-157">始终立即等待 EF Core 异步方法。</span><span class="sxs-lookup"><span data-stu-id="f6943-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="f6943-158">通过依赖关系注入在多个线程之间隐式共享 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="f6943-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="f6943-159">默认情况下 [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 扩展方法使用[范围内生存期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)来注册 `DbContext` 类型。</span><span class="sxs-lookup"><span data-stu-id="f6943-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="f6943-160">这样可以避免在大多数 ASP.NET Core 应用程序中出现并发访问问题，因为在给定时间内只有一个线程在执行每个客户端请求，并且每个请求都有单独的依赖关系注入范围（因此有单独的 `DbContext` 实例）。</span><span class="sxs-lookup"><span data-stu-id="f6943-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="f6943-161">对于 Blazor Server 托管模型，一个逻辑请求用来维护 Blazor 用户线路，因此，如果使用默认注入范围，则每个用户线路只能提供一个范围内的 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="f6943-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="f6943-162">任何并行显式执行多个线程的代码都应确保 `DbContext` 实例不会同时访问。</span><span class="sxs-lookup"><span data-stu-id="f6943-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="f6943-163">使用依赖关系注入可以通过以下方式实现：将上下文注册为范围内，并为每个线程创建范围（使用 `IServiceScopeFactory`），或将 `DbContext` 注册为暂时性（使用采用 `ServiceLifetime` 参数的 `AddDbContext` 的重载）。</span><span class="sxs-lookup"><span data-stu-id="f6943-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="f6943-164">进一步阅读</span><span class="sxs-lookup"><span data-stu-id="f6943-164">More reading</span></span>

- <span data-ttu-id="f6943-165">请阅读[依赖关系注入](/aspnet/core/fundamentals/dependency-injection)以了解有关使用 DI 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f6943-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="f6943-166">请阅读[测试](xref:core/testing/index)以了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="f6943-166">Read [Testing](xref:core/testing/index) for more information.</span></span>
