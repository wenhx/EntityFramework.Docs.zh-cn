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
# <a name="configuring-a-dbcontext"></a>配置 DbContext

本文介绍了通过 `DbContextOptions` 配置 `DbContext` 的基本模式，以使用特定的 EF Core 提供程序和可选行为连接到数据库。

## <a name="design-time-dbcontext-configuration"></a>设计时 DbContext 配置

EF Core 设计时工具（如[迁移](xref:core/managing-schemas/migrations/index)）需要能够发现并创建 `DbContext` 类型的工作实例，以收集有关应用程序的实体类型及其如何映射到数据库架构的详细信息。 只要该工具可以通过与在运行时的配置方式类似的方式轻松地创建 `DbContext`，就可以自动执行此过程。

虽然向 `DbContext` 提供必要配置信息的任何模式都可以在运行时正常运行，但在设计时需要使用 `DbContext` 的工具只能使用有限数量的模式。 [设计时上下文创建](xref:core/cli/dbcontext-creation)部分中包含更多详细信息。

## <a name="configuring-dbcontextoptions"></a>配置 DbContextOptions

`DbContext` 必须具有 `DbContextOptions` 的实例才能执行任何工作。 `DbContextOptions` 实例包含如下配置信息：

- 要使用的数据库提供程序，通常通过调用 `UseSqlServer` 或 `UseSqlite` 等方法进行选择。 这些扩展方法需要相应的提供程序包，如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite`。 这些方法在 `Microsoft.EntityFrameworkCore` 命名空间中定义。
- 数据库实例的任何必要的连接字符串或标识符，通常作为参数传递给上面提到的提供程序选择方法
- 任何提供程序级别的可选行为选择器，通常还在对提供程序选择方法的调用中链接
- 任何常规 EF Core 行为选择器，通常在提供程序选择器方法之后或之前链接

下面的示例将 `DbContextOptions` 配置为使用 SQL Server 提供程序、包含在 `connectionString` 变量中的连接、提供程序级别的命令超时以及使所有查询默认在 `DbContext` [非跟踪](xref:core/querying/tracking#no-tracking-queries)中执行的 EF Core 行为选择器：

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> 上面提到的提供程序选择器方法和其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类的扩展方法。 若要访问这些扩展方法，你可能需要在范围内具有命名空间（通常为 `Microsoft.EntityFrameworkCore`），并在项目中包含附加包依赖项。

可以通过重写 `OnConfiguring` 方法或通过构造函数参数从外部向 `DbContext` 提供 `DbContextOptions`。

如果同时使用这两种方式，则 `OnConfiguring` 最后应用，并且可以覆盖提供给构造函数参数的选项。

### <a name="constructor-argument"></a>构造函数参数

构造函数可以仅接受 `DbContextOptions`，如下所示：

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
> DbContext 的基本构造函数还接受 `DbContextOptions` 的非泛型版本，但对于具有多个上下文类型的应用程序，不建议使用非泛型版本。

应用程序现在可以在实例化上下文时传递 `DbContextOptions`，如下所示：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

还可以在上下文本身中初始化 `DbContextOptions`。 虽然你可以将此技术用于基本配置，但通常仍需要从外部获取某些配置详细信息，例如数据库连接字符串。 可以使用配置 API 或任何其他方法实现此目的。

若要在上下文中初始化 `DbContextOptions`，请重写 `OnConfiguring` 方法，并对提供的 `DbContextOptionsBuilder` 调用方法：

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

应用程序无需将任何内容传递给构造函数，即可实例化此类上下文：

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 此方法不能用于测试，除非测试面向完整数据库。

### <a name="using-dbcontext-with-dependency-injection"></a>将 DbContext 与依赖关系注入一起使用

EF Core 支持将 `DbContext` 与依赖关系注入容器一起使用。 可以使用 `AddDbContext<TContext>` 方法将 DbContext 类型添加到服务容器中。

`AddDbContext<TContext>` 将使 DbContext 类型 `TContext` 和相应的 `DbContextOptions<TContext>` 可用于从服务容器中注入。

有关依赖关系注入的其他信息，请参阅下面的[进一步阅读](#more-reading)。

将 `DbContext` 添加到依赖关系注入：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

这需要将[构造函数参数](#constructor-argument)添加到接受 `DbContextOptions<TContext>` 的 DbContext 类型。

上下文代码：

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

应用程序代码（在 ASP.NET Core 中）：

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

应用程序代码（直接使用 ServiceProvider，不太常见）：

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbContext 线程处理问题

Entity Framework Core 不支持在同一 `DbContext` 实例上运行多个并行操作。 这包括异步查询的并行执行以及从多个线程进行的任何显式并发使用。 因此，始终立即 `await` 异步调用，或对并行执行的操作使用单独的 `DbContext` 实例。

当 EF Core 检测到尝试同时使用 `DbContext` 实例的情况时，你将看到 `InvalidOperationException`，其中包含类似于以下内容的消息：

> 在上一个操作完成之前，第二个操作已在此上下文中启动。 这通常是由使用同一个 DbContext 实例的不同线程引起的，但不保证实例成员是线程安全的。

检测不到并发访问时，可能会导致未定义的行为、应用程序崩溃和数据损坏。

一些常见错误可能会无意中导致并发访问同一 `DbContext` 实例：

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>在对同一 DbContext 启动任何其他操作之前，忘记等待异步操作完成

使用异步方法，EF Core 可以启动以非阻挡式访问数据库的操作。 但是，如果调用方不等待其中一个方法完成，而是继续对 `DbContext` 执行其他操作，则 `DbContext` 的状态可能会（并且很可能会）损坏。

始终立即等待 EF Core 异步方法。

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>通过依赖关系注入在多个线程之间隐式共享 DbContext 实例

默认情况下 [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 扩展方法使用[范围内生存期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)来注册 `DbContext` 类型。

这样可以避免在大多数 ASP.NET Core 应用程序中出现并发访问问题，因为在给定时间内只有一个线程在执行每个客户端请求，并且每个请求都有单独的依赖关系注入范围（因此有单独的 `DbContext` 实例）。 对于 Blazor Server 托管模型，一个逻辑请求用来维护 Blazor 用户线路，因此，如果使用默认注入范围，则每个用户线路只能提供一个范围内的 DbContext 实例。

任何并行显式执行多个线程的代码都应确保 `DbContext` 实例不会同时访问。

使用依赖关系注入可以通过以下方式实现：将上下文注册为范围内，并为每个线程创建范围（使用 `IServiceScopeFactory`），或将 `DbContext` 注册为暂时性（使用采用 `ServiceLifetime` 参数的 `AddDbContext` 的重载）。

## <a name="more-reading"></a>进一步阅读

- 请阅读[依赖关系注入](/aspnet/core/fundamentals/dependency-injection)以了解有关使用 DI 的详细信息。
- 请阅读[测试](xref:core/testing/index)以了解详细信息。
