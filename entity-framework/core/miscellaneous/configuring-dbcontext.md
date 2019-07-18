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
# <a name="configuring-a-dbcontext"></a>配置 DbContext

本文演示通过 `DbContextOptions` 配置 `DbContext` 以使用特定的 EF Core 提供程序和可选行为来连接到数据库的基本模式。

## <a name="design-time-dbcontext-configuration"></a>设计时 DbContext 配置

EF Core 设计时工具（如[迁移](xref:core/managing-schemas/migrations/index)）需要能够发现和创建 `DbContext` 类型的工作实例，以便收集有关应用程序实体类型及其如何映射到数据库架构的详细信息。 此过程可以自动执行，只要该工具可以轻松创建 `DbContext` 并使其与在运行时采用相似的配置方式。

尽管为 `DbContext` 提供必要配置信息的任何模式都可以在运行时工作，但需要在设计时使用 `DbContext`的工具仅适用于有限数量的模式。“设计时上下文创建”部分包含对这些内容的详细介绍。           [“设计时上下文创建”](xref:core/miscellaneous/cli/dbcontext-creation)部分包含对这些内容的详细介绍。

## <a name="configuring-dbcontextoptions"></a>配置 DbContextOptions

`DbContext` 必须具有 `DbContextOptions` 的实例才能执行工作。           `DbContextOptions` 实例包含如下配置信息：

- 要使用的数据库提供程序, 通常通过调用方法 (如`UseSqlServer`或`UseSqlite`) 进行选择。 这些扩展方法需要相应的提供程序包, 如`Microsoft.EntityFrameworkCore.SqlServer`或`Microsoft.EntityFrameworkCore.Sqlite`。 方法在`Microsoft.EntityFrameworkCore`命名空间中定义。
- 任何必需的数据库实例的连接字符串或标识符, 通常作为参数传递给上面提到的提供者选择方法
- 任何提供程序级别的可选行为选择器, 通常还链接到对提供程序选择方法的调用中
- 任何常规 EF Core 行为选择器，通常链接之后或之前提供程序选择器方法

下面的示例将配置`DbContextOptions`若要使用 SQL Server 提供程序，在连接包含`connectionString`变量、 提供程序级别的命令超时，以及可使在中执行的所有查询 EF Core 行为选择器`DbContext`[否跟踪](xref:core/querying/tracking#no-tracking-queries)默认情况下：

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 上面提到的提供程序选择器方法和其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类上的扩展方法。 若要访问这些扩展方法，可能需要具有定义域内的命名空间（通常为 `Microsoft.EntityFrameworkCore`）并在项目中包含其他包依赖关系。

通过使用构造函数参数重写`OnConfiguring`方法或外部,可以向提供。`DbContextOptions` `DbContext`

如果同时使用这两`OnConfiguring`个, 则最后应用并可以覆盖提供给构造函数参数的选项。

### <a name="constructor-argument"></a>构造函数参数

具有构造函数的上下文代码:

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
> DbContext 的基构造函数还接受的非泛型版本`DbContextOptions`, 但对于具有多个上下文类型的应用程序, 不建议使用非泛型版本。

要从构造函数参数中初始化的应用程序代码:

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

上下文代码`OnConfiguring`:

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

用于初始化`DbContext`的应用程序代码, `OnConfiguring`该代码使用:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 此方法不会对其进行测试, 除非测试以完整数据库为目标。

### <a name="using-dbcontext-with-dependency-injection"></a>将 DbContext 与依赖关系注入一起使用

EF Core 支持使用`DbContext`与依赖关系注入容器。 可以使用`AddDbContext<TContext>`方法将 DbContext 类型添加到服务容器中。

`AddDbContext<TContext>`会将 DbContext 类型、 `TContext`和对应`DbContextOptions<TContext>`的可用于从服务容器中注入。

有关依赖关系注入的其他信息[, 请参阅](#more-reading)下文。

将添加`DbContext`到依赖关系注入:

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

这要求向接受`DbContextOptions<TContext>`的 DbContext 类型添加[构造函数参数](#constructor-argument)。

上下文代码:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

（在 ASP.NET Core) 的应用程序代码：

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

应用程序代码 (直接使用 ServiceProvider, 不太常见):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbContext 线程问题

Entity Framework Core 不支持在同一`DbContext`实例上运行多个并行操作。 这包括异步查询的并行执行以及从多个线程进行的任何显式并发使用。 因此, 应`await`立即异步调用, 或对并行`DbContext`执行的操作使用单独的实例。

当 EF Core 检测到并行使用某个`DbContext`实例时, 您将`InvalidOperationException`看到一条消息, 如下所示: 

> 在上一个操作完成之前, 在此上下文上启动的第二个操作。 这通常是由使用同一个 DbContext 实例的不同线程引起的, 但不保证实例成员是线程安全的。

并发访问未被检测时, 可能会导致未定义的行为、应用程序崩溃和数据损坏。

存在一些常见错误, 这些错误可能会无意中导致对`DbContext`同一实例的并发访问:

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>在对同一 DbContext 启动任何其他操作之前, 忘记等待异步操作完成

使用异步方法, EF Core 可以启动以非阻止方式访问数据库的操作。 但是, 如果调用方不等待其中一种方法完成, 并继续在上`DbContext`执行其他操作, 则的`DbContext`状态可能会损坏。 

始终等待立即 EF Core 异步方法。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>通过依赖关系注入在多个线程之间隐式共享 DbContext 实例

默认[`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)情况下, `DbContext`扩展方法使用[范围生存期](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)注册类型。 

由于在给定的时间只有一个线程在执行每个客户端请求, 而且每个请求都获取了单独的依赖项注入范围 (因此单独`DbContext`的 ASP.NET Core实例)。

不过, 任何并行显式执行多个线程的代码都应该`DbContext`确保不会同时访问实例。

使用依赖关系注入, 这可以通过以下方式实现: 将上下文注册为作用域, 并`IServiceScopeFactory`为每个线程创建作用域 (使用`DbContext` ), 或注册为暂时性`AddDbContext` (使用的重载, 该重载采用`ServiceLifetime`参数)。

## <a name="more-reading"></a>阅读更多

* 阅读 [ASP.NET Core 入门](../get-started/aspnetcore/index.md)，了解有关配合使用 ASP.NET Core 和 EF 的详细信息。
* 阅读[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)，了解有关使用 DI 的详细信息。
* 阅读[测试](testing/index.md)了解详细信息。
