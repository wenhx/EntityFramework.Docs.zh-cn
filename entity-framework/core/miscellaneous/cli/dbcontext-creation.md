---
title: 设计时 DbContext 创建-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f44f0648678af5a70e5171d69692bde1c1d5e0eb
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655529"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="41d4d-102">设计时 DbContext 创建</span><span class="sxs-lookup"><span data-stu-id="41d4d-102">Design-time DbContext Creation</span></span>

<span data-ttu-id="41d4d-103">某些 EF Core 工具命令（例如，[迁移][1]命令）需要在设计时创建一个派生的 `DbContext` 实例，以便收集有关应用程序的实体类型及其如何映射到数据库架构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="41d4d-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="41d4d-104">在大多数情况下，希望创建的 `DbContext` 的配置方式类似于在[运行时][2]对其进行配置的方式。</span><span class="sxs-lookup"><span data-stu-id="41d4d-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="41d4d-105">工具可通过多种方式来创建 `DbContext`：</span><span class="sxs-lookup"><span data-stu-id="41d4d-105">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="41d4d-106">从应用程序服务</span><span class="sxs-lookup"><span data-stu-id="41d4d-106">From application services</span></span>

<span data-ttu-id="41d4d-107">如果启动项目使用[ASP.NET Core Web 主机][3]或[.Net Core 泛型主机][4]，则这些工具将尝试从应用程序的服务提供程序获取 DbContext 对象。</span><span class="sxs-lookup"><span data-stu-id="41d4d-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="41d4d-108">工具首先尝试通过调用 `Program.CreateHostBuilder()`，调用 `Build()`，然后访问 `Services` 属性来获取服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="41d4d-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="41d4d-109">创建新的 ASP.NET Core 应用程序时，默认情况下会包括此挂接。</span><span class="sxs-lookup"><span data-stu-id="41d4d-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="41d4d-110">在应用程序的服务提供程序中，需要将 `DbContext` 本身及其构造函数中的任何依赖项注册为服务。</span><span class="sxs-lookup"><span data-stu-id="41d4d-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="41d4d-111">为此，可以在[将 `DbContextOptions<TContext>` 实例作为参数][5]并使用[`AddDbContext<TContext>` 方法][6]的 `DbContext` 上使用构造函数。</span><span class="sxs-lookup"><span data-stu-id="41d4d-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="41d4d-112">使用不带参数的构造函数</span><span class="sxs-lookup"><span data-stu-id="41d4d-112">Using a constructor with no parameters</span></span>

<span data-ttu-id="41d4d-113">如果无法从应用程序服务提供程序获得 DbContext，则工具会在项目中查找派生 `DbContext` 类型。</span><span class="sxs-lookup"><span data-stu-id="41d4d-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="41d4d-114">然后，它们尝试使用不带参数的构造函数创建实例。</span><span class="sxs-lookup"><span data-stu-id="41d4d-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="41d4d-115">如果 `DbContext` 是使用[`OnConfiguring`][7]方法配置的，则这可能是默认构造函数。</span><span class="sxs-lookup"><span data-stu-id="41d4d-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="41d4d-116">从设计时工厂</span><span class="sxs-lookup"><span data-stu-id="41d4d-116">From a design-time factory</span></span>

<span data-ttu-id="41d4d-117">你还可以通过实现 `IDesignTimeDbContextFactory<TContext>` 接口告诉工具如何创建 DbContext：如果实现此接口的类在与派生的 `DbContext` 相同的项目中或在应用程序的启动项目中找到，则这些工具将绕过其他工具创建 DbContext 并改用设计时工厂的方式。</span><span class="sxs-lookup"><span data-stu-id="41d4d-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="41d4d-118">`args` 参数当前未使用。</span><span class="sxs-lookup"><span data-stu-id="41d4d-118">The `args` parameter is currently unused.</span></span> <span data-ttu-id="41d4d-119">跟踪从工具中指定设计时参数的功能时出现[问题][8]。</span><span class="sxs-lookup"><span data-stu-id="41d4d-119">There is [an issue][8] tracking the ability to specify design-time arguments from the tools.</span></span>

<span data-ttu-id="41d4d-120">如果需要在设计时与运行时不同的情况下配置 DbContext，则设计时工厂特别有用。如果 `DbContext` 构造函数采用其他参数，但未在 DI 中注册，如果根本不使用 DI，或者出于某种原因而你不愿在 ASP.NET Core 应用程序的 `Main` 类中使用 `BuildWebHost` 方法。</span><span class="sxs-lookup"><span data-stu-id="41d4d-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
