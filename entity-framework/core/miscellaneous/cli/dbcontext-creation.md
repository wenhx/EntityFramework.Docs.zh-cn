---
title: 设计时 DbContext 创建-EF Core
description: 使用 Entity Framework Core 创建设计时 DbContext 的策略
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: ef2eba93827e04a9731ba960c40e9a50168ca8ff
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619449"
---
# <a name="design-time-dbcontext-creation"></a>设计时 DbContext 创建

某些 EF Core 工具命令 (例如，) [迁移][1] 命令需要在 `DbContext` 设计时创建一个派生实例，以便收集有关该应用程序的实体类型及其如何映射到数据库架构的详细信息。 在大多数情况下，最好 `DbContext` 按照类似的方式配置创建，使其在 [运行时配置][2]。

工具可通过多种方式来创建 `DbContext` ：

## <a name="from-application-services"></a>从应用程序服务

如果启动项目使用 [ASP.NET Core Web 主机][3] 或 [.Net Core 泛型主机][4]，则这些工具将尝试从应用程序的服务提供程序获取 DbContext 对象。

工具首先尝试通过调用 `Program.CreateHostBuilder()` 、调用 `Build()` ，然后访问属性来获取服务提供程序 `Services` 。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> 创建新的 ASP.NET Core 应用程序时，默认情况下会包括此挂接。

`DbContext`自身及其构造函数中的任何依赖项都需要在应用程序的服务提供程序中注册为服务。 通过在[将 `DbContext` 实例 `DbContextOptions<TContext>` 作为参数][5]并使用[ `AddDbContext<TContext>` 方法][6]的上，可以轻松实现这一点。

## <a name="using-a-constructor-with-no-parameters"></a>使用不带参数的构造函数

如果无法从应用程序服务提供程序获得 DbContext，则工具将查找 `DbContext` 项目中的派生类型。 然后，它们尝试使用不带参数的构造函数创建实例。 如果使用方法配置，则这可能是默认构造函数 `DbContext` [`OnConfiguring`][7] 。

## <a name="from-a-design-time-factory"></a>从设计时工厂

你还可以通过实现接口来告诉工具如何创建 DbContext `IDesignTimeDbContextFactory<TContext>` ：如果实现此接口的类在与派生的项目相同的项目中 `DbContext` 或在应用程序的启动项目中找到，则这些工具将绕过创建 DbContext 的其他方法，并改用设计时工厂。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> 在 EFCore 5.0 之前， `args` 未使用参数 () ，请参阅 [此问题][8] 。
> 这在 EFCore 5.0 中是固定的，任何其他设计时参数都将通过该参数传递到应用程序。

如果需要以不同于运行时的方式配置 DbContext 的设计时，则设计时工厂特别有用 `DbContext` 。如果构造函数采用其他参数，但未在 di 中注册，如果根本不使用 di，或者出于某种原因而不是使用 `BuildWebHost` ASP.NET Core 应用程序的类中的方法 `Main` 。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
