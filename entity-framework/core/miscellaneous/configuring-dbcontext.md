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
# <a name="configuring-a-dbcontext"></a>配置 DbContext

本文介绍了 `DbContext` 如何通过将配置为 `DbContextOptions` 使用特定的 EF Core 提供程序和可选行为连接到数据库的基本模式。

## <a name="design-time-dbcontext-configuration"></a>设计时 DbContext 配置

EF Core 的设计时 [工具需要能够](xref:core/managing-schemas/migrations/index) 发现和创建类型的工作实例，以便 `DbContext` 收集有关应用程序实体类型的详细信息以及如何映射到数据库架构。 此过程可以是自动的，只要该工具可以轻松地创建，这样就可以 `DbContext` 将其配置为类似于运行时的配置方式。

尽管提供所需的配置信息的任何模式 `DbContext` 可在运行时工作，但在设计时需要使用的工具 `DbContext` 只能处理有限数量的模式。 [设计时上下文创建](xref:core/miscellaneous/cli/dbcontext-creation)部分更详细地介绍了这些内容。

## <a name="configuring-dbcontextoptions"></a>配置 DbContextOptions

`DbContext` 必须具有的实例 `DbContextOptions` ，才能执行任何工作。 `DbContextOptions`实例携带如下配置信息：

- 要使用的数据库提供程序，通常通过调用方法（如或）进行选择 `UseSqlServer` `UseSqlite` 。 这些扩展方法需要相应的提供程序包，如 `Microsoft.EntityFrameworkCore.SqlServer` 或 `Microsoft.EntityFrameworkCore.Sqlite` 。 方法在命名空间中定义 `Microsoft.EntityFrameworkCore` 。
- 任何必需的数据库实例的连接字符串或标识符，通常作为参数传递给上面提到的提供者选择方法
- 任何提供程序级别的可选行为选择器，通常还链接到对提供程序选择方法的调用中
- 任何常规 EF Core 行为选择器，通常在提供程序选择器方法之后或之前链接

下面的示例将配置 `DbContextOptions` 为使用 SQL Server 提供程序、变量中包含的连接 `connectionString` 、提供程序级别的命令超时和默认情况下执行所有查询的 EF Core 行为选择器 `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) ：

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> 提供程序选择器方法和上面提到的其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类的扩展方法。 若要访问这些扩展方法，你可能需要在范围内 (通常) 命名空间 `Microsoft.EntityFrameworkCore` ，并在项目中包含附加包依赖项。

`DbContextOptions` `DbContext` 通过使用 `OnConfiguring` 构造函数参数重写方法或外部，可以向提供。

如果同时使用这两个， `OnConfiguring` 则最后应用并可以覆盖提供给构造函数参数的选项。

### <a name="constructor-argument"></a>构造函数参数

构造函数只需接受 `DbContextOptions` ，如下所示：

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
> DbContext 的基构造函数还接受的非泛型版本 `DbContextOptions` ，但对于具有多个上下文类型的应用程序，不建议使用非泛型版本。

应用程序现在可以 `DbContextOptions` 在实例化上下文时传递，如下所示：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

您还可以在 `DbContextOptions` 上下文自身内初始化。 虽然你可以将此方法用于基本配置，但你通常仍需要从外部获取某些配置详细信息，例如数据库连接字符串。 可以使用配置 API 或其他任何方法来完成此操作。

若要在 `DbContextOptions` 上下文中进行初始化，请重写 `OnConfiguring` 方法，并对提供的调用方法 `DbContextOptionsBuilder` ：

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

应用程序可以简单地实例化此类上下文，而无需将任何内容传递给构造函数：

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 此方法不会对其进行测试，除非测试以完整数据库为目标。

### <a name="using-dbcontext-with-dependency-injection"></a>将 DbContext 与依赖关系注入一起使用

EF Core 支持 `DbContext` 将与依赖关系注入容器一起使用。 可以使用方法将 DbContext 类型添加到服务容器中 `AddDbContext<TContext>` 。

`AddDbContext<TContext>` 会将 DbContext 类型、 `TContext` 和对应的 `DbContextOptions<TContext>` 可用于从服务容器中注入。

有关依赖关系注入的其他信息 [，请参阅](#more-reading) 下文。

将添加 `DbContext` 到依赖关系注入：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

这要求向接受的 DbContext 类型添加 [构造函数参数](#constructor-argument) `DbContextOptions<TContext>` 。

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

ASP.NET Core) 中的应用程序代码 (：

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

应用程序代码 (直接使用 ServiceProvider，不太常见) ：

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbContext 线程问题

Entity Framework Core 不支持在同一实例上运行多个并行操作 `DbContext` 。 这包括异步查询的并行执行以及从多个线程进行的任何显式并发使用。 因此，应 `await` 立即异步调用，或 `DbContext` 对并行执行的操作使用单独的实例。

当 EF Core 检测到并行使用某个 `DbContext` 实例时，您将看到一 `InvalidOperationException` 条消息，如下所示：

> 在上一个操作完成之前，在此上下文上启动的第二个操作。 这通常是由使用同一个 DbContext 实例的不同线程引起的，但不保证实例成员是线程安全的。

并发访问未被检测时，可能会导致未定义的行为、应用程序崩溃和数据损坏。

存在一些常见错误，这些错误可能会无意中导致对同一实例的并发访问 `DbContext` ：

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>在对同一 DbContext 启动任何其他操作之前，忘记等待异步操作完成

使用异步方法，EF Core 可以启动以非阻止方式访问数据库的操作。 但是，如果调用方不等待其中一种方法完成，并继续对执行其他操作，则的 `DbContext` 状态 `DbContext` 可以为， (并且很可能会) 损坏。

始终等待立即 EF Core 异步方法。

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>通过依赖关系注入在多个线程之间隐式共享 DbContext 实例

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) `DbContext` 默认情况下，扩展方法使用[范围生存期](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)注册类型。

这对于大多数 ASP.NET Core 应用程序中的并发访问问题是安全的，因为在给定的时间只有一个线程在执行每个客户端请求，因此，每个请求都将获取单独的依赖项注入范围 (，因而) 单独的 `DbContext` 实例。 对于 Blazor 服务器托管模型，使用一个逻辑请求来维护 Blazor 用户线路，因此，如果使用默认注入作用域，则每个用户线路只能提供一个作用域内 DbContext 实例。

任何并行执行多个线程的代码都应确保 `DbContext` 不会同时访问实例。

使用依赖关系注入，可以通过以下方式实现此目的：将上下文注册为作用域，并使用每个线程的) 创建作用域 (`IServiceScopeFactory` ，或者 `DbContext` 使用的重载将 `AddDbContext` 参数) 注册为暂时性 (`ServiceLifetime` 。

## <a name="more-reading"></a>阅读更多

- 有关使用 DI 的详细信息，请参阅 [依赖关系注入](/aspnet/core/fundamentals/dependency-injection) 。
- 有关详细信息，请参阅 [测试](xref:core/miscellaneous/testing/index) 。
