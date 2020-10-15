---
title: 设计时 DbContext 创建-EF Core
description: 使用 Entity Framework Core 创建设计时 DbContext 的策略
author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: a9d7da2bb76d60ca63eb0dc189f924df125f0a7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062004"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="fee7e-103">设计时 DbContext 创建</span><span class="sxs-lookup"><span data-stu-id="fee7e-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="fee7e-104">某些 EF Core 工具命令 (例如，) [迁移][1] 命令需要在 `DbContext` 设计时创建一个派生实例，以便收集有关该应用程序的实体类型及其如何映射到数据库架构的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fee7e-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="fee7e-105">在大多数情况下，最好 `DbContext` 按照类似的方式配置创建，使其在 [运行时配置][2]。</span><span class="sxs-lookup"><span data-stu-id="fee7e-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="fee7e-106">工具可通过多种方式来创建 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="fee7e-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="fee7e-107">从应用程序服务</span><span class="sxs-lookup"><span data-stu-id="fee7e-107">From application services</span></span>

<span data-ttu-id="fee7e-108">如果启动项目使用 [ASP.NET Core Web 主机][3] 或 [.Net Core 泛型主机][4]，则这些工具将尝试从应用程序的服务提供程序获取 DbContext 对象。</span><span class="sxs-lookup"><span data-stu-id="fee7e-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="fee7e-109">工具首先尝试通过调用 `Program.CreateHostBuilder()` 、调用 `Build()` ，然后访问属性来获取服务提供程序 `Services` 。</span><span class="sxs-lookup"><span data-stu-id="fee7e-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="fee7e-110">创建新的 ASP.NET Core 应用程序时，默认情况下会包括此挂接。</span><span class="sxs-lookup"><span data-stu-id="fee7e-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="fee7e-111">`DbContext`自身及其构造函数中的任何依赖项都需要在应用程序的服务提供程序中注册为服务。</span><span class="sxs-lookup"><span data-stu-id="fee7e-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="fee7e-112">通过在[将 `DbContext` 实例 `DbContextOptions<TContext>` 作为参数][5]并使用[ `AddDbContext<TContext>` 方法][6]的上，可以轻松实现这一点。</span><span class="sxs-lookup"><span data-stu-id="fee7e-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="fee7e-113">使用不带参数的构造函数</span><span class="sxs-lookup"><span data-stu-id="fee7e-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="fee7e-114">如果无法从应用程序服务提供程序获得 DbContext，则工具将查找 `DbContext` 项目中的派生类型。</span><span class="sxs-lookup"><span data-stu-id="fee7e-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="fee7e-115">然后，它们尝试使用不带参数的构造函数创建实例。</span><span class="sxs-lookup"><span data-stu-id="fee7e-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="fee7e-116">如果使用方法配置，则这可能是默认构造函数 `DbContext` [`OnConfiguring`][7] 。</span><span class="sxs-lookup"><span data-stu-id="fee7e-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="fee7e-117">从设计时工厂</span><span class="sxs-lookup"><span data-stu-id="fee7e-117">From a design-time factory</span></span>

<span data-ttu-id="fee7e-118">你还可以通过实现接口来告诉工具如何创建 DbContext `IDesignTimeDbContextFactory<TContext>` ：如果实现此接口的类在与派生的项目相同的项目中 `DbContext` 或在应用程序的启动项目中找到，则这些工具将绕过创建 DbContext 的其他方法，并改用设计时工厂。</span><span class="sxs-lookup"><span data-stu-id="fee7e-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="fee7e-119">在 EFCore 5.0 之前， `args` 未使用参数 () ，请参阅 [此问题][8] 。</span><span class="sxs-lookup"><span data-stu-id="fee7e-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="fee7e-120">这在 EFCore 5.0 中是固定的，任何其他设计时参数都将通过该参数传递到应用程序。</span><span class="sxs-lookup"><span data-stu-id="fee7e-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="fee7e-121">如果需要以不同于运行时的方式配置 DbContext 的设计时，则设计时工厂特别有用 `DbContext` 。如果构造函数采用其他参数，但未在 di 中注册，如果根本不使用 di，或者出于某种原因而不是使用 `BuildWebHost` ASP.NET Core 应用程序的类中的方法 `Main` 。</span><span class="sxs-lookup"><span data-stu-id="fee7e-121">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
