---
title: 配置 DbContext-EF Core
description: 用于配置 Dbcontext 与 Entity Framework Core 的策略
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3afad8d220acecbb01b15bbb855b52a895e6eb66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062017"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="c3be6-103">配置 DbContext</span><span class="sxs-lookup"><span data-stu-id="c3be6-103">Configuring a DbContext</span></span>

<span data-ttu-id="c3be6-104">本文介绍了 `DbContext` 如何通过将配置为 `DbContextOptions` 使用特定的 EF Core 提供程序和可选行为连接到数据库的基本模式。</span><span class="sxs-lookup"><span data-stu-id="c3be6-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="c3be6-105">设计时 DbContext 配置</span><span class="sxs-lookup"><span data-stu-id="c3be6-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="c3be6-106">EF Core 的设计时 [工具需要能够](xref:core/managing-schemas/migrations/index) 发现和创建类型的工作实例，以便 `DbContext` 收集有关应用程序实体类型的详细信息以及如何映射到数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c3be6-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="c3be6-107">此过程可以是自动的，只要该工具可以轻松地创建，这样就可以 `DbContext` 将其配置为类似于运行时的配置方式。</span><span class="sxs-lookup"><span data-stu-id="c3be6-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="c3be6-108">尽管提供所需的配置信息的任何模式 `DbContext` 可在运行时工作，但在设计时需要使用的工具 `DbContext` 只能处理有限数量的模式。</span><span class="sxs-lookup"><span data-stu-id="c3be6-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="c3be6-109">[设计时上下文创建](xref:core/miscellaneous/cli/dbcontext-creation)部分更详细地介绍了这些内容。</span><span class="sxs-lookup"><span data-stu-id="c3be6-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="c3be6-110">配置 DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="c3be6-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="c3be6-111">`DbContext` 必须具有的实例 `DbContextOptions` ，才能执行任何工作。</span><span class="sxs-lookup"><span data-stu-id="c3be6-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="c3be6-112">`DbContextOptions`实例携带如下配置信息：</span><span class="sxs-lookup"><span data-stu-id="c3be6-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="c3be6-113">要使用的数据库提供程序，通常通过调用方法（如或）进行选择 `UseSqlServer` `UseSqlite` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="c3be6-114">这些扩展方法需要相应的提供程序包，如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="c3be6-115">方法在命名空间中定义 `Microsoft.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="c3be6-116">任何必需的数据库实例的连接字符串或标识符，通常作为参数传递给上面提到的提供者选择方法</span><span class="sxs-lookup"><span data-stu-id="c3be6-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="c3be6-117">任何提供程序级别的可选行为选择器，通常还链接到对提供程序选择方法的调用中</span><span class="sxs-lookup"><span data-stu-id="c3be6-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="c3be6-118">任何常规 EF Core 行为选择器，通常在提供程序选择器方法之后或之前链接</span><span class="sxs-lookup"><span data-stu-id="c3be6-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="c3be6-119">下面的示例将配置 `DbContextOptions` 为使用 SQL Server 提供程序、变量中包含的连接 `connectionString` 、提供程序级别的命令超时和默认情况下执行所有查询的 EF Core 行为选择器 `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) ：</span><span class="sxs-lookup"><span data-stu-id="c3be6-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="c3be6-120">提供程序选择器方法和上面提到的其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c3be6-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="c3be6-121">若要访问这些扩展方法，你可能需要在范围内 (通常) 命名空间 `Microsoft.EntityFrameworkCore` ，并在项目中包含附加包依赖项。</span><span class="sxs-lookup"><span data-stu-id="c3be6-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="c3be6-122">`DbContextOptions` `DbContext` 通过使用 `OnConfiguring` 构造函数参数重写方法或外部，可以向提供。</span><span class="sxs-lookup"><span data-stu-id="c3be6-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="c3be6-123">如果同时使用这两个， `OnConfiguring` 则最后应用并可以覆盖提供给构造函数参数的选项。</span><span class="sxs-lookup"><span data-stu-id="c3be6-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="c3be6-124">构造函数参数</span><span class="sxs-lookup"><span data-stu-id="c3be6-124">Constructor argument</span></span>

<span data-ttu-id="c3be6-125">构造函数只需接受 `DbContextOptions` ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c3be6-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

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
> <span data-ttu-id="c3be6-126">DbContext 的基构造函数还接受的非泛型版本 `DbContextOptions` ，但对于具有多个上下文类型的应用程序，不建议使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="c3be6-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="c3be6-127">应用程序现在可以 `DbContextOptions` 在实例化上下文时传递，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c3be6-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="c3be6-128">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="c3be6-128">OnConfiguring</span></span>

<span data-ttu-id="c3be6-129">您还可以在 `DbContextOptions` 上下文自身内初始化。</span><span class="sxs-lookup"><span data-stu-id="c3be6-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="c3be6-130">虽然你可以将此方法用于基本配置，但你通常仍需要从外部获取某些配置详细信息，例如数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c3be6-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="c3be6-131">可以使用配置 API 或其他任何方法来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="c3be6-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="c3be6-132">若要在 `DbContextOptions` 上下文中进行初始化，请重写 `OnConfiguring` 方法，并对提供的调用方法 `DbContextOptionsBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="c3be6-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

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

<span data-ttu-id="c3be6-133">应用程序可以简单地实例化此类上下文，而无需将任何内容传递给构造函数：</span><span class="sxs-lookup"><span data-stu-id="c3be6-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="c3be6-134">此方法不会对其进行测试，除非测试以完整数据库为目标。</span><span class="sxs-lookup"><span data-stu-id="c3be6-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="c3be6-135">将 DbContext 与依赖关系注入一起使用</span><span class="sxs-lookup"><span data-stu-id="c3be6-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="c3be6-136">EF Core 支持 `DbContext` 将与依赖关系注入容器一起使用。</span><span class="sxs-lookup"><span data-stu-id="c3be6-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="c3be6-137">可以使用方法将 DbContext 类型添加到服务容器中 `AddDbContext<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="c3be6-138">`AddDbContext<TContext>` 会将 DbContext 类型、 `TContext` 和对应的 `DbContextOptions<TContext>` 可用于从服务容器中注入。</span><span class="sxs-lookup"><span data-stu-id="c3be6-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="c3be6-139">有关依赖关系注入的其他信息 [，请参阅](#more-reading) 下文。</span><span class="sxs-lookup"><span data-stu-id="c3be6-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="c3be6-140">将添加 `DbContext` 到依赖关系注入：</span><span class="sxs-lookup"><span data-stu-id="c3be6-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="c3be6-141">这要求向接受的 DbContext 类型添加 [构造函数参数](#constructor-argument) `DbContextOptions<TContext>` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="c3be6-142">上下文代码：</span><span class="sxs-lookup"><span data-stu-id="c3be6-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="c3be6-143">ASP.NET Core) 中的应用程序代码 (：</span><span class="sxs-lookup"><span data-stu-id="c3be6-143">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="c3be6-144">应用程序代码 (直接使用 ServiceProvider，不太常见) ：</span><span class="sxs-lookup"><span data-stu-id="c3be6-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="c3be6-145">避免 DbContext 线程问题</span><span class="sxs-lookup"><span data-stu-id="c3be6-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="c3be6-146">Entity Framework Core 不支持在同一实例上运行多个并行操作 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="c3be6-147">这包括异步查询的并行执行以及从多个线程进行的任何显式并发使用。</span><span class="sxs-lookup"><span data-stu-id="c3be6-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="c3be6-148">因此，应 `await` 立即异步调用，或 `DbContext` 对并行执行的操作使用单独的实例。</span><span class="sxs-lookup"><span data-stu-id="c3be6-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="c3be6-149">当 EF Core 检测到并行使用某个 `DbContext` 实例时，您将看到一 `InvalidOperationException` 条消息，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c3be6-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="c3be6-150">在上一个操作完成之前，在此上下文上启动的第二个操作。</span><span class="sxs-lookup"><span data-stu-id="c3be6-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="c3be6-151">这通常是由使用同一个 DbContext 实例的不同线程引起的，但不保证实例成员是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="c3be6-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="c3be6-152">并发访问未被检测时，可能会导致未定义的行为、应用程序崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="c3be6-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="c3be6-153">存在一些常见错误，这些错误可能会无意中导致对同一实例的并发访问 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="c3be6-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="c3be6-154">在对同一 DbContext 启动任何其他操作之前，忘记等待异步操作完成</span><span class="sxs-lookup"><span data-stu-id="c3be6-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="c3be6-155">使用异步方法，EF Core 可以启动以非阻止方式访问数据库的操作。</span><span class="sxs-lookup"><span data-stu-id="c3be6-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="c3be6-156">但是，如果调用方不等待其中一种方法完成，并继续对执行其他操作，则的 `DbContext` 状态 `DbContext` 可以为， (并且很可能会) 损坏。</span><span class="sxs-lookup"><span data-stu-id="c3be6-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="c3be6-157">始终等待立即 EF Core 异步方法。</span><span class="sxs-lookup"><span data-stu-id="c3be6-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="c3be6-158">通过依赖关系注入在多个线程之间隐式共享 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="c3be6-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="c3be6-159">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) `DbContext` 默认情况下，扩展方法使用[范围生存期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)注册类型。</span><span class="sxs-lookup"><span data-stu-id="c3be6-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="c3be6-160">这对于大多数 ASP.NET Core 应用程序中的并发访问问题是安全的，因为在给定的时间只有一个线程在执行每个客户端请求，因此，每个请求都将获取单独的依赖项注入范围 (，因而) 单独的 `DbContext` 实例。</span><span class="sxs-lookup"><span data-stu-id="c3be6-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="c3be6-161">对于 Blazor 服务器托管模型，使用一个逻辑请求来维护 Blazor 用户线路，因此，如果使用默认注入作用域，则每个用户线路只能提供一个作用域内 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="c3be6-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="c3be6-162">任何并行执行多个线程的代码都应确保 `DbContext` 不会同时访问实例。</span><span class="sxs-lookup"><span data-stu-id="c3be6-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="c3be6-163">使用依赖关系注入，可以通过以下方式实现此目的：将上下文注册为作用域，并使用每个线程的) 创建作用域 (`IServiceScopeFactory` ，或者 `DbContext` 使用的重载将 `AddDbContext` 参数) 注册为暂时性 (`ServiceLifetime` 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="c3be6-164">阅读更多</span><span class="sxs-lookup"><span data-stu-id="c3be6-164">More reading</span></span>

- <span data-ttu-id="c3be6-165">有关使用 DI 的详细信息，请参阅 [依赖关系注入](/aspnet/core/fundamentals/dependency-injection) 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="c3be6-166">有关详细信息，请参阅 [测试](xref:core/miscellaneous/testing/index) 。</span><span class="sxs-lookup"><span data-stu-id="c3be6-166">Read [Testing](xref:core/miscellaneous/testing/index) for more information.</span></span>
