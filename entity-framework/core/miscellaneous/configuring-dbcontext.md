---
title: 配置 DbContext 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: ddabf825ef23c2ec07efcde390df7d0cf48db33c
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306512"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="6891d-102">配置 DbContext</span><span class="sxs-lookup"><span data-stu-id="6891d-102">Configuring a DbContext</span></span>

<span data-ttu-id="6891d-103">本文演示通过 `DbContextOptions` 配置 `DbContext` 以使用特定的 EF Core 提供程序和可选行为来连接到数据库的基本模式。</span><span class="sxs-lookup"><span data-stu-id="6891d-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="6891d-104">设计时 DbContext 配置</span><span class="sxs-lookup"><span data-stu-id="6891d-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="6891d-105">EF Core 设计时工具（如[迁移](xref:core/managing-schemas/migrations/index)）需要能够发现和创建 `DbContext` 类型的工作实例，以便收集有关应用程序实体类型及其如何映射到数据库架构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6891d-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="6891d-106">此过程可以自动执行，只要该工具可以轻松创建 `DbContext` 并使其与在运行时采用相似的配置方式。</span><span class="sxs-lookup"><span data-stu-id="6891d-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="6891d-107">尽管为 `DbContext` 提供必要配置信息的任何模式都可以在运行时工作，但需要在设计时使用 `DbContext`的工具仅适用于有限数量的模式。“设计时上下文创建”部分包含对这些内容的详细介绍。</span><span class="sxs-lookup"><span data-stu-id="6891d-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="6891d-108">          [“设计时上下文创建”](xref:core/miscellaneous/cli/dbcontext-creation)部分包含对这些内容的详细介绍。</span><span class="sxs-lookup"><span data-stu-id="6891d-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="6891d-109">配置 DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="6891d-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="6891d-110">`DbContext` 必须具有 `DbContextOptions` 的实例才能执行工作。</span><span class="sxs-lookup"><span data-stu-id="6891d-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="6891d-111">          `DbContextOptions` 实例包含如下配置信息：</span><span class="sxs-lookup"><span data-stu-id="6891d-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="6891d-112">要使用的数据库提供程序, 通常通过调用方法 (如`UseSqlServer`或`UseSqlite`) 进行选择。</span><span class="sxs-lookup"><span data-stu-id="6891d-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="6891d-113">这些扩展方法需要相应的提供程序包, 如`Microsoft.EntityFrameworkCore.SqlServer`或`Microsoft.EntityFrameworkCore.Sqlite`。</span><span class="sxs-lookup"><span data-stu-id="6891d-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="6891d-114">方法在`Microsoft.EntityFrameworkCore`命名空间中定义。</span><span class="sxs-lookup"><span data-stu-id="6891d-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="6891d-115">任何必需的数据库实例的连接字符串或标识符, 通常作为参数传递给上面提到的提供者选择方法</span><span class="sxs-lookup"><span data-stu-id="6891d-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="6891d-116">任何提供程序级别的可选行为选择器, 通常还链接到对提供程序选择方法的调用中</span><span class="sxs-lookup"><span data-stu-id="6891d-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="6891d-117">任何常规 EF Core 行为选择器，通常链接之后或之前提供程序选择器方法</span><span class="sxs-lookup"><span data-stu-id="6891d-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="6891d-118">下面的示例将配置`DbContextOptions`若要使用 SQL Server 提供程序，在连接包含`connectionString`变量、 提供程序级别的命令超时，以及可使在中执行的所有查询 EF Core 行为选择器`DbContext`[否跟踪](xref:core/querying/tracking#no-tracking-queries)默认情况下：</span><span class="sxs-lookup"><span data-stu-id="6891d-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="6891d-119">上面提到的提供程序选择器方法和其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类上的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6891d-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="6891d-120">若要访问这些扩展方法，可能需要具有定义域内的命名空间（通常为 `Microsoft.EntityFrameworkCore`）并在项目中包含其他包依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6891d-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="6891d-121">通过使用构造函数参数重写`OnConfiguring`方法或外部,可以向提供。`DbContextOptions` `DbContext`</span><span class="sxs-lookup"><span data-stu-id="6891d-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="6891d-122">如果同时使用这两`OnConfiguring`个, 则最后应用并可以覆盖提供给构造函数参数的选项。</span><span class="sxs-lookup"><span data-stu-id="6891d-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="6891d-123">构造函数参数</span><span class="sxs-lookup"><span data-stu-id="6891d-123">Constructor argument</span></span>

<span data-ttu-id="6891d-124">具有构造函数的上下文代码:</span><span class="sxs-lookup"><span data-stu-id="6891d-124">Context code with constructor:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="6891d-125">DbContext 的基构造函数还接受的非泛型版本`DbContextOptions`, 但对于具有多个上下文类型的应用程序, 不建议使用非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="6891d-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="6891d-126">要从构造函数参数中初始化的应用程序代码:</span><span class="sxs-lookup"><span data-stu-id="6891d-126">Application code to initialize from constructor argument:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="6891d-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="6891d-127">OnConfiguring</span></span>

<span data-ttu-id="6891d-128">上下文代码`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="6891d-128">Context code with `OnConfiguring`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="6891d-129">用于初始化`DbContext`的应用程序代码, `OnConfiguring`该代码使用:</span><span class="sxs-lookup"><span data-stu-id="6891d-129">Application code to initialize a `DbContext` that uses `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="6891d-130">此方法不会对其进行测试, 除非测试以完整数据库为目标。</span><span class="sxs-lookup"><span data-stu-id="6891d-130">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="6891d-131">将 DbContext 与依赖关系注入一起使用</span><span class="sxs-lookup"><span data-stu-id="6891d-131">Using DbContext with dependency injection</span></span>

<span data-ttu-id="6891d-132">EF Core 支持使用`DbContext`与依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="6891d-132">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="6891d-133">可以使用`AddDbContext<TContext>`方法将 DbContext 类型添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="6891d-133">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="6891d-134">`AddDbContext<TContext>`会将 DbContext 类型、 `TContext`和对应`DbContextOptions<TContext>`的可用于从服务容器中注入。</span><span class="sxs-lookup"><span data-stu-id="6891d-134">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="6891d-135">有关依赖关系注入的其他信息[, 请参阅](#more-reading)下文。</span><span class="sxs-lookup"><span data-stu-id="6891d-135">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="6891d-136">将添加`DbContext`到依赖关系注入:</span><span class="sxs-lookup"><span data-stu-id="6891d-136">Adding the `DbContext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="6891d-137">这要求向接受`DbContextOptions<TContext>`的 DbContext 类型添加[构造函数参数](#constructor-argument)。</span><span class="sxs-lookup"><span data-stu-id="6891d-137">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="6891d-138">上下文代码:</span><span class="sxs-lookup"><span data-stu-id="6891d-138">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="6891d-139">（在 ASP.NET Core) 的应用程序代码：</span><span class="sxs-lookup"><span data-stu-id="6891d-139">Application code (in ASP.NET Core):</span></span>

``` csharp
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

<span data-ttu-id="6891d-140">应用程序代码 (直接使用 ServiceProvider, 不太常见):</span><span class="sxs-lookup"><span data-stu-id="6891d-140">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="6891d-141">避免 DbContext 线程问题</span><span class="sxs-lookup"><span data-stu-id="6891d-141">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="6891d-142">Entity Framework Core 不支持在同一`DbContext`实例上运行多个并行操作。</span><span class="sxs-lookup"><span data-stu-id="6891d-142">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="6891d-143">这包括异步查询的并行执行以及从多个线程进行的任何显式并发使用。</span><span class="sxs-lookup"><span data-stu-id="6891d-143">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="6891d-144">因此, 应`await`立即异步调用, 或对并行`DbContext`执行的操作使用单独的实例。</span><span class="sxs-lookup"><span data-stu-id="6891d-144">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="6891d-145">当 EF Core 检测到并行使用某个`DbContext`实例时, 您将`InvalidOperationException`看到一条消息, 如下所示:</span><span class="sxs-lookup"><span data-stu-id="6891d-145">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span> 

> <span data-ttu-id="6891d-146">在上一个操作完成之前, 在此上下文上启动的第二个操作。</span><span class="sxs-lookup"><span data-stu-id="6891d-146">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="6891d-147">这通常是由使用同一个 DbContext 实例的不同线程引起的, 但不保证实例成员是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="6891d-147">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="6891d-148">并发访问未被检测时, 可能会导致未定义的行为、应用程序崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="6891d-148">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="6891d-149">存在一些常见错误, 这些错误可能会无意中导致对`DbContext`同一实例的并发访问:</span><span class="sxs-lookup"><span data-stu-id="6891d-149">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="6891d-150">在对同一 DbContext 启动任何其他操作之前, 忘记等待异步操作完成</span><span class="sxs-lookup"><span data-stu-id="6891d-150">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="6891d-151">使用异步方法, EF Core 可以启动以非阻止方式访问数据库的操作。</span><span class="sxs-lookup"><span data-stu-id="6891d-151">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="6891d-152">但是, 如果调用方不等待其中一种方法完成, 并继续在上`DbContext`执行其他操作, 则的`DbContext`状态可能会损坏。</span><span class="sxs-lookup"><span data-stu-id="6891d-152">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span> 

<span data-ttu-id="6891d-153">始终等待立即 EF Core 异步方法。</span><span class="sxs-lookup"><span data-stu-id="6891d-153">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="6891d-154">通过依赖关系注入在多个线程之间隐式共享 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="6891d-154">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="6891d-155">默认[`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)情况下, `DbContext`扩展方法使用[范围生存期](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)注册类型。</span><span class="sxs-lookup"><span data-stu-id="6891d-155">The [`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span> 

<span data-ttu-id="6891d-156">由于在给定的时间只有一个线程在执行每个客户端请求, 而且每个请求都获取了单独的依赖项注入范围 (因此单独`DbContext`的 ASP.NET Core实例)。</span><span class="sxs-lookup"><span data-stu-id="6891d-156">This is safe from concurrent access issues in ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span>

<span data-ttu-id="6891d-157">不过, 任何并行显式执行多个线程的代码都应该`DbContext`确保不会同时访问实例。</span><span class="sxs-lookup"><span data-stu-id="6891d-157">However any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="6891d-158">使用依赖关系注入, 这可以通过以下方式实现: 将上下文注册为作用域, 并`IServiceScopeFactory`为每个线程创建作用域 (使用`DbContext` ), 或注册为暂时性`AddDbContext` (使用的重载, 该重载采用`ServiceLifetime`参数)。</span><span class="sxs-lookup"><span data-stu-id="6891d-158">Using dependency injection, this can be achieved by either registering the context as scoped and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="6891d-159">阅读更多</span><span class="sxs-lookup"><span data-stu-id="6891d-159">More reading</span></span>

* <span data-ttu-id="6891d-160">阅读 [ASP.NET Core 入门](../get-started/aspnetcore/index.md)，了解有关配合使用 ASP.NET Core 和 EF 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6891d-160">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="6891d-161">阅读[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)，了解有关使用 DI 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6891d-161">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
* <span data-ttu-id="6891d-162">阅读[测试](testing/index.md)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="6891d-162">Read [Testing](testing/index.md) for more information.</span></span>
