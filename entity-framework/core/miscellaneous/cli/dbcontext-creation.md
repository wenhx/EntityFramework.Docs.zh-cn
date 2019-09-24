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
<a name="design-time-dbcontext-creation"></a><span data-ttu-id="3b7db-102">设计时 DbContext 创建</span><span class="sxs-lookup"><span data-stu-id="3b7db-102">Design-time DbContext Creation</span></span>
==============================
<span data-ttu-id="3b7db-103">某些 EF Core 工具命令（例如，[迁移][1]命令）需要在设计时创建一个派生`DbContext`实例，以便收集有关该应用程序的实体类型及其如何映射到数据库架构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="3b7db-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="3b7db-104">在大多数情况下， `DbContext`最好按照类似的方式配置创建，使其在[运行时配置][2]。</span><span class="sxs-lookup"><span data-stu-id="3b7db-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="3b7db-105">工具可通过多种方式来创建`DbContext`：</span><span class="sxs-lookup"><span data-stu-id="3b7db-105">There are various ways the tools try to create the `DbContext`:</span></span>

<a name="from-application-services"></a><span data-ttu-id="3b7db-106">从应用程序服务</span><span class="sxs-lookup"><span data-stu-id="3b7db-106">From application services</span></span>
-------------------------
<span data-ttu-id="3b7db-107">如果启动项目使用[ASP.NET Core Web 主机][3]或[.Net Core 泛型主机][4]，则这些工具将尝试从应用程序的服务提供程序获取 DbContext 对象。</span><span class="sxs-lookup"><span data-stu-id="3b7db-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="3b7db-108">工具首先尝试通过调用`Program.CreateHostBuilder()`、调用`Build()`，然后访问`Services`属性来获取服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="3b7db-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

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
> <span data-ttu-id="3b7db-109">创建新的 ASP.NET Core 应用程序时，默认情况下会包括此挂接。</span><span class="sxs-lookup"><span data-stu-id="3b7db-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="3b7db-110">自身`DbContext`及其构造函数中的任何依赖项都需要在应用程序的服务提供程序中注册为服务。</span><span class="sxs-lookup"><span data-stu-id="3b7db-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="3b7db-111">通过在[将`DbContext`实例`DbContextOptions<TContext>` ][5] [作为参数并使用方法的上，可以轻松实现这一点。`AddDbContext<TContext>` ][6]</span><span class="sxs-lookup"><span data-stu-id="3b7db-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

<a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="3b7db-112">使用不带参数的构造函数</span><span class="sxs-lookup"><span data-stu-id="3b7db-112">Using a constructor with no parameters</span></span>
--------------------------------------
<span data-ttu-id="3b7db-113">如果无法从应用程序服务提供程序获得 DbContext，则工具将查找项目中的`DbContext`派生类型。</span><span class="sxs-lookup"><span data-stu-id="3b7db-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="3b7db-114">然后，它们尝试使用不带参数的构造函数创建实例。</span><span class="sxs-lookup"><span data-stu-id="3b7db-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="3b7db-115">`DbContext` [如果`OnConfiguring`][7]使用方法配置，则这可能是默认构造函数。</span><span class="sxs-lookup"><span data-stu-id="3b7db-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

<a name="from-a-design-time-factory"></a><span data-ttu-id="3b7db-116">从设计时工厂</span><span class="sxs-lookup"><span data-stu-id="3b7db-116">From a design-time factory</span></span>
--------------------------
<span data-ttu-id="3b7db-117">还可以通过实现`IDesignTimeDbContextFactory<TContext>`接口来告诉工具如何创建 DbContext：如果在与派生`DbContext`的项目相同的项目中或在应用程序的启动项目中找到了实现此接口的类，则这些工具将绕过创建 DbContext 的其他方法并改用设计时工厂。</span><span class="sxs-lookup"><span data-stu-id="3b7db-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

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
> <span data-ttu-id="3b7db-118">当前`args`未使用该参数。</span><span class="sxs-lookup"><span data-stu-id="3b7db-118">The `args` parameter is currently unused.</span></span> <span data-ttu-id="3b7db-119">跟踪从工具中指定设计时参数的功能时出现[问题][8]。</span><span class="sxs-lookup"><span data-stu-id="3b7db-119">There is [an issue][8] tracking the ability to specify design-time arguments from the tools.</span></span>

<span data-ttu-id="3b7db-120">如果需要以不同于运行时的方式配置 DbContext 的设计时， `DbContext`则设计时工厂特别有用。如果构造函数采用其他参数，则不会在 di 中注册，如果根本不使用 di，或者对于某些原因：不`BuildWebHost`希望在 ASP.NET Core 应用程序的`Main`类中使用方法。</span><span class="sxs-lookup"><span data-stu-id="3b7db-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
