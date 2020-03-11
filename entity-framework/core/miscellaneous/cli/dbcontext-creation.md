---
title: 设计时 DbContext 创建-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f44f0648678af5a70e5171d69692bde1c1d5e0eb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414217"
---
# <a name="design-time-dbcontext-creation"></a>设计时 DbContext 创建

某些 EF Core 工具命令（例如，[迁移][1]命令）需要在设计时创建一个派生的 `DbContext` 实例，以便收集有关应用程序的实体类型及其如何映射到数据库架构的详细信息。 在大多数情况下，希望创建的 `DbContext` 的配置方式类似于在[运行时][2]对其进行配置的方式。

工具可通过多种方式来创建 `DbContext`：

## <a name="from-application-services"></a>从应用程序服务

如果启动项目使用[ASP.NET Core Web 主机][3]或[.Net Core 泛型主机][4]，则这些工具将尝试从应用程序的服务提供程序获取 DbContext 对象。

工具首先尝试通过调用 `Program.CreateHostBuilder()`，调用 `Build()`，然后访问 `Services` 属性来获取服务提供程序。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> 创建新的 ASP.NET Core 应用程序时，默认情况下会包括此挂接。

在应用程序的服务提供程序中，需要将 `DbContext` 本身及其构造函数中的任何依赖项注册为服务。 为此，可以在[将 `DbContextOptions<TContext>` 实例作为参数][5]并使用[`AddDbContext<TContext>` 方法][6]的 `DbContext` 上使用构造函数。

## <a name="using-a-constructor-with-no-parameters"></a>使用不带参数的构造函数

如果无法从应用程序服务提供程序获得 DbContext，则工具会在项目中查找派生 `DbContext` 类型。 然后，它们尝试使用不带参数的构造函数创建实例。 如果 `DbContext` 是使用[`OnConfiguring`][7]方法配置的，则这可能是默认构造函数。

## <a name="from-a-design-time-factory"></a>从设计时工厂

还可以通过实现 `IDesignTimeDbContextFactory<TContext>` 接口告诉工具如何创建 DbContext：如果实现此接口的类在与派生 `DbContext` 相同的项目中或在应用程序的启动项目中找到，则这些工具将绕过其他创建 DbContext 的方法，而改用设计时工厂。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> `args` 参数当前未使用。 跟踪从工具中指定设计时参数的功能时出现[问题][8]。

如果需要在设计时与运行时不同的情况下配置 DbContext，则设计时工厂特别有用。如果 `DbContext` 构造函数采用其他参数，但未在 DI 中注册，则如果根本不使用 DI，或者出于某种原因而不希望在 ASP.NET Core 应用程序的 `Main` 类中使用 `BuildWebHost` 方法。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
