---
title: 设计时 DbContext 创建-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f83d4b16227d114a1cac1514667484a908fea4ac
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197574"
---
<a name="design-time-dbcontext-creation"></a>设计时 DbContext 创建
==============================
某些 EF Core 工具命令（例如，[迁移][1]命令）需要在设计时创建一个派生`DbContext`实例，以便收集有关该应用程序的实体类型及其如何映射到数据库架构的详细信息。 在大多数情况下， `DbContext`最好按照类似的方式配置创建，使其在[运行时配置][2]。

工具可通过多种方式来创建`DbContext`：

<a name="from-application-services"></a>从应用程序服务
-------------------------
如果启动项目使用[ASP.NET Core Web 主机][3]或[.Net Core 泛型主机][4]，则这些工具将尝试从应用程序的服务提供程序获取 DbContext 对象。

工具首先尝试通过调用`Program.CreateHostBuilder()`、调用`Build()`，然后访问`Services`属性来获取服务提供程序。

``` csharp
public class Program
{
    public static void Main(string[] args)
        => CreateHostBuilder(args).Build().Run();

    // EF Core uses this method at design time to access the DbContext
    public static IHostBuilder CreateHostBuilder(string[] args)
        => Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(
                webBuilder => webBuilder.UseStartup<Startup>());
}

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
        => services.AddDbContext<ApplicationDbContext>();
}

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

> [!NOTE]
> 创建新的 ASP.NET Core 应用程序时，默认情况下会包括此挂接。

自身`DbContext`及其构造函数中的任何依赖项都需要在应用程序的服务提供程序中注册为服务。 通过在[将`DbContext`实例`DbContextOptions<TContext>` ][5] [作为参数并使用方法的上，可以轻松实现这一点。`AddDbContext<TContext>` ][6]

<a name="using-a-constructor-with-no-parameters"></a>使用不带参数的构造函数
--------------------------------------
如果无法从应用程序服务提供程序获得 DbContext，则工具将查找项目中的`DbContext`派生类型。 然后，它们尝试使用不带参数的构造函数创建实例。 `DbContext` [如果`OnConfiguring`][7]使用方法配置，则这可能是默认构造函数。

<a name="from-a-design-time-factory"></a>从设计时工厂
--------------------------
还可以通过实现`IDesignTimeDbContextFactory<TContext>`接口来告诉工具如何创建 DbContext：如果在与派生`DbContext`的项目相同的项目中或在应用程序的启动项目中找到了实现此接口的类，则这些工具将绕过创建 DbContext 的其他方法并改用设计时工厂。

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

> [!NOTE]
> 当前`args`未使用该参数。 跟踪从工具中指定设计时参数的功能时出现[问题][8]。

如果需要以不同于运行时的方式配置 DbContext 的设计时， `DbContext`则设计时工厂特别有用。如果构造函数采用其他参数，则不会在 di 中注册，如果根本不使用 di，或者对于某些原因：不`BuildWebHost`希望在 ASP.NET Core 应用程序的`Main`类中使用方法。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
