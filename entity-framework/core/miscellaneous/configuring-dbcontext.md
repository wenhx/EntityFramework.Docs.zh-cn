---
title: 配置 DbContext 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 316d363d4a1b8a909efc1c32b492280c0d16cb4e
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405210"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="8fbf3-102">配置 DbContext</span><span class="sxs-lookup"><span data-stu-id="8fbf3-102">Configuring a DbContext</span></span>

<span data-ttu-id="8fbf3-103">本文演示通过 `DbContextOptions` 配置 `DbContext` 以使用特定的 EF Core 提供程序和可选行为来连接到数据库的基本模式。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="8fbf3-104">设计时 DbContext 配置</span><span class="sxs-lookup"><span data-stu-id="8fbf3-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="8fbf3-105">EF Core 设计时工具（如[迁移](xref:core/managing-schemas/migrations/index)）需要能够发现和创建 `DbContext` 类型的工作实例，以便收集有关应用程序实体类型及其如何映射到数据库架构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="8fbf3-106">此过程可以自动执行，只要该工具可以轻松创建 `DbContext` 并使其与在运行时采用相似的配置方式。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="8fbf3-107">尽管为 `DbContext` 提供必要配置信息的任何模式都可以在运行时工作，但需要在设计时使用 `DbContext`的工具仅适用于有限数量的模式。“设计时上下文创建”部分包含对这些内容的详细介绍。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="8fbf3-108">[“设计时上下文创建”](xref:core/miscellaneous/cli/dbcontext-creation)部分包含对这些内容的详细介绍。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="8fbf3-109">配置 DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="8fbf3-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="8fbf3-110">`DbContext` 必须具有 `DbContextOptions` 的实例才能执行工作。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="8fbf3-111">`DbContextOptions` 实例包含如下配置信息：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="8fbf3-112">数据库提供程序，若要使用，通常选择通过调用的方法，如`UseSqlServer`或`UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="8fbf3-113">这些扩展方法需要相应的提供程序包，如`Microsoft.EntityFrameworkCore.SqlServer`或`Microsoft.EntityFrameworkCore.Sqlite`。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="8fbf3-114">中定义的方法`Microsoft.EntityFrameworkCore`命名空间。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="8fbf3-115">任何必要的连接字符串或标识符的数据库实例中，通常作为参数传递到上述提供程序选择方法</span><span class="sxs-lookup"><span data-stu-id="8fbf3-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="8fbf3-116">任何提供程序级别的可选行为选择器，通常还链接到提供程序选择方法调用中</span><span class="sxs-lookup"><span data-stu-id="8fbf3-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="8fbf3-117">任何常规 EF Core 行为选择器，通常链接之后或之前提供程序选择器方法</span><span class="sxs-lookup"><span data-stu-id="8fbf3-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="8fbf3-118">下面的示例将配置`DbContextOptions`若要使用 SQL Server 提供程序，在连接包含`connectionString`变量、 提供程序级别的命令超时，以及可使在中执行的所有查询 EF Core 行为选择器`DbContext`[否跟踪](xref:core/querying/tracking#no-tracking-queries)默认情况下：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="8fbf3-119">上面提到的提供程序选择器方法和其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类上的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="8fbf3-120">若要访问这些扩展方法，可能需要具有定义域内的命名空间（通常为 `Microsoft.EntityFrameworkCore`）并在项目中包含其他包依赖关系。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="8fbf3-121">`DbContextOptions`可以提供给`DbContext`通过重写`OnConfiguring`方法或构造函数参数通过从外部。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="8fbf3-122">如果将使用它们，`OnConfiguring`最后应用，并且可以覆盖选项提供给构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="8fbf3-123">构造函数参数</span><span class="sxs-lookup"><span data-stu-id="8fbf3-123">Constructor argument</span></span>

<span data-ttu-id="8fbf3-124">使用构造函数的上下文代码：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-124">Context code with constructor:</span></span>

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
> <span data-ttu-id="8fbf3-125">DbContext 基构造函数还接受非泛型版本的`DbContextOptions`，但不是建议使用多个上下文类型的应用程序使用的非泛型版本。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="8fbf3-126">从构造函数自变量进行初始化的应用程序代码：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-126">Application code to initialize from constructor argument:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="8fbf3-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="8fbf3-127">OnConfiguring</span></span>

<span data-ttu-id="8fbf3-128">上下文代码`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="8fbf3-128">Context code with `OnConfiguring`:</span></span>

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

<span data-ttu-id="8fbf3-129">应用程序代码来初始化`DbContext`，它使用`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="8fbf3-129">Application code to initialize a `DbContext` that uses `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="8fbf3-130">此方法不会将自身添加到测试，除非测试以完整的数据库为目标。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-130">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="8fbf3-131">使用依赖关系注入使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="8fbf3-131">Using DbContext with dependency injection</span></span>

<span data-ttu-id="8fbf3-132">EF Core 支持使用`DbContext`与依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-132">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="8fbf3-133">DbContext 类型可以通过使用添加到服务容器`AddDbContext<TContext>`方法。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-133">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="8fbf3-134">`AddDbContext<TContext>` 将这两个 DbContext 类型， `TContext`，并相应`DbContextOptions<TContext>`可用于从服务容器的注入。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-134">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="8fbf3-135">请参阅[多个读取](#more-reading)以下依赖关系注入的其他信息。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-135">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="8fbf3-136">添加`Dbcontext`依赖关系注入到：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-136">Adding the `Dbcontext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="8fbf3-137">这要求将添加[构造函数参数](#constructor-argument)到 DbContext 类型接受`DbContextOptions<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-137">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="8fbf3-138">上下文代码：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-138">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="8fbf3-139">（在 ASP.NET Core) 的应用程序代码：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-139">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="8fbf3-140">（服务提供商处直接使用，不太常见） 的应用程序代码：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-140">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="8fbf3-141">避免 DbContext 线程处理问题</span><span class="sxs-lookup"><span data-stu-id="8fbf3-141">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="8fbf3-142">Entity Framework Core 不支持在同一个正在运行的多个并行操作`DbContext`实例。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-142">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="8fbf3-143">这包括并行执行异步查询和任何显式的并发使用，从多个线程。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-143">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="8fbf3-144">因此，始终`await`异步调用立即，或使用不同`DbContext`并行执行的操作的实例。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-144">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="8fbf3-145">EF Core 时检测到尝试使用`DbContext`实例同时，您将看到`InvalidOperationException`与类似这样的消息：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-145">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span> 

> <span data-ttu-id="8fbf3-146">在此上下文中的上一个操作完成之前启动的第二个操作。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-146">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="8fbf3-147">这通常被由于由不同的线程使用 DbContext 的同一个实例，但是保证实例成员都不是线程安全。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-147">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="8fbf3-148">当未检测到出现的并发访问时，它可能导致未定义的行为，应用程序崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-148">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="8fbf3-149">可以在同一个 inadvernetly 原因并发访问的常见错误有`DbContext`实例：</span><span class="sxs-lookup"><span data-stu-id="8fbf3-149">There are common mistakes that can inadvernetly cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="8fbf3-150">忘记 await 的异步操作开始对相同 DbContext 的任何其他操作之前完成</span><span class="sxs-lookup"><span data-stu-id="8fbf3-150">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="8fbf3-151">异步方法使 EF Core 启动非阻塞方式访问数据库的操作。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-151">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="8fbf3-152">但是，如果调用方不等待完成的其中一个方法，并继续执行其他操作`DbContext`，则状态的`DbContext`则可以为 （而且很可能将） 已损坏。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-152">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span> 

<span data-ttu-id="8fbf3-153">始终立即等待 EF Core 异步方法。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-153">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="8fbf3-154">隐式在通过依赖关系注入的多个线程间共享 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="8fbf3-154">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="8fbf3-155">[ `AddDbContext` ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)扩展方法注册`DbContext`类型与[作用域的生存期](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)默认情况下。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-155">The [`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span> 

<span data-ttu-id="8fbf3-156">这是安全的 ASP.NET Core 应用程序中的并发访问问题，因为只有一个线程在给定时间执行每个客户端请求并且每个请求获取单独的依赖关系注入作用域 (并因此单独`DbContext`实例）。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-156">This is safe from concurrent access issues in ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span>

<span data-ttu-id="8fbf3-157">但是显式并行执行多个线程的任何代码应确保`DbContext`实例不是曾经 accesed 并发。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-157">However any code that explicitly executes multiple threads in paralell should ensure that `DbContext` instances aren't ever accesed concurrently.</span></span>

<span data-ttu-id="8fbf3-158">使用依赖关系注入，这可以通过实现作为其作用域内和创建作用域注册上下文 (使用`IServiceScopeFactory`) 为每个线程，或者注册`DbContext`为瞬态 (使用的重载`AddDbContext`接受`ServiceLifetime`参数)。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-158">Using dependency injection, this can be achieved by either registering the context as scoped and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="8fbf3-159">详细阅读</span><span class="sxs-lookup"><span data-stu-id="8fbf3-159">More reading</span></span>

* <span data-ttu-id="8fbf3-160">阅读 [ASP.NET Core 入门](../get-started/aspnetcore/index.md)，了解有关配合使用 ASP.NET Core 和 EF 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-160">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="8fbf3-161">阅读[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)，了解有关使用 DI 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-161">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
* <span data-ttu-id="8fbf3-162">阅读[测试](testing/index.md)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="8fbf3-162">Read [Testing](testing/index.md) for more information.</span></span>
