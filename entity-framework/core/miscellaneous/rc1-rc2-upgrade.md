---
title: 从 EF Core 1.0 RC1 升级到 RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370521"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>从 EF Core 1.0 RC1 升级到 1.0 RC2

本文介绍如何将使用 RC1 包生成的应用程序移动到 RC2。

## <a name="package-names-and-versions"></a>包名称和版本

在 RC1 和 RC2 之间，我们将从 "实体框架 7" 改为 "Entity Framework Core"。 可以[通过 Scott Hanselman 详细了解此帖子](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)中的更改原因。 由于此更改，我们的包名称将从更改 `EntityFramework.*` 为 `Microsoft.EntityFrameworkCore.*` ，从 `7.0.0-rc1-final` 到 `1.0.0-rc2-final` （或 `1.0.0-preview1-final` 用于工具）的版本。

**需要完全删除 RC1 包，然后安装 RC2 包。** 下面是一些常用包的映射。

| RC1 包                                               | RC2 等效项                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework. Microsoft sql server 7.0.0-rc1-final | Microsoft.entityframeworkcore 1.0.0-rc2-最终      |
| EntityFramework 7.0.0 版-rc1-最终 | Microsoft.entityframeworkcore 1.0.0-rc2-最终      |
| EntityFramework7. Npgsql 3.1.0-rc1     | NpgSql. Microsoft.entityframeworkcore. Postgres<to be advised>      |
| EntityFramework. SqlServerCompact35 7.0.0-rc1-final | Microsoft.entityframeworkcore. SqlServerCompact35 1.0.0-rc2-最终      |
| EntityFramework. SqlServerCompact40 7.0.0-rc1-final | Microsoft.entityframeworkcore. SqlServerCompact40 1.0.0-rc2-最终      |
| EntityFramework 7.0.0 版-rc1-最终 | Microsoft.entityframeworkcore 1.0.0-rc2-最终      |
| EntityFramework. IBMDataServer 7.0.0-beta1     | 尚不适用于 RC2                                            |
| EntityFramework rc1-final | Microsoft.entityframeworkcore 1.0.0-预览版 1-final |
| EntityFramework Microsoft sql server-rc1-final | Microsoft.entityframeworkcore 1.0.0-rc2-最终      |

## <a name="namespaces"></a>命名空间

与包名称一起，命名空间从更改 `Microsoft.Data.Entity.*` 为 `Microsoft.EntityFrameworkCore.*` 。 您可以使用的 "查找/替换" 来处理此更改 `using Microsoft.Data.Entity` `using Microsoft.EntityFrameworkCore` 。

## <a name="table-naming-convention-changes"></a>表命名约定更改

RC2 中的一个重要功能更改是将 `DbSet<TEntity>` 给定实体的属性名称用作其映射到的表名称，而不只是类名。 有关此更改的详细信息，请参阅[相关公告问题](https://github.com/aspnet/Announcements/issues/167)。

对于现有的 RC1 应用程序，我们建议将以下代码添加到方法的开头， `OnModelCreating` 以保留 RC1 命名策略：

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

如果要采用新的命名策略，我们建议成功完成其余的升级步骤，并删除代码并创建迁移，以应用表重命名。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext/Startup.cs 更改（仅 ASP.NET Core 项目）

在 RC1 中，你必须将实体框架服务添加到应用程序服务提供程序中 `Startup.ConfigureServices(...)` ：

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

在 RC2 中，你可以删除对、等的调用 `AddEntityFramework()` `AddSqlServer()` ：

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

还需要向派生上下文添加一个构造函数，该构造函数采用上下文选项，并将其传递到基构造函数。 这是必需的，因为我们消除了在幕后 snuck 它们的一些可怕的神奇之处：

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>传入 IServiceProvider

如果你有将传递给上下文的 RC1 代码 `IServiceProvider` ，则此代码现在已移到 `DbContextOptions` ，而不是单独的构造函数参数。 使用 `DbContextOptionsBuilder.UseInternalServiceProvider(...)` 设置服务提供程序。

### <a name="testing"></a>测试

执行此操作的最常见方案是在测试时控制 InMemory 数据库的范围。 有关使用 RC2 执行此操作的示例，请参阅更新的[测试](testing/index.md)文章。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>从应用程序服务提供程序解析内部服务（仅 ASP.NET Core 项目）

如果你有一个 ASP.NET Core 应用程序，并且你希望 EF 解析应用程序服务提供程序中的内部服务，则有一个的重载， `AddDbContext` 它允许你配置以下内容：

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> 建议允许 EF 内部管理其自己的服务，除非您有理由将内部 EF 服务合并到您的应用程序服务提供程序中。 你可能想要执行此操作的主要原因是使用应用程序服务提供程序来替换 EF 在内部使用的服务

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a>DNX 命令 => .NET Core CLI （仅 ASP.NET Core 项目）

如果以前使用过 `dnx ef` ASP.NET 5 项目的命令，则这些命令现在已移动到 `dotnet ef` 命令。 相同的命令语法仍适用。 您可以使用 `dotnet ef --help` 来提供语法信息。

命令注册的方式在 RC2 中发生了更改，因为 DNX 被 .NET Core CLI 替换。 现在，命令在中的一个 `tools` 部分注册 `project.json` ：

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> 如果使用 Visual Studio，现在可以使用程序包管理器控制台来运行 ASP.NET Core 项目的 EF 命令（在 RC1 中不支持此操作）。 你仍需要注册的部分中的命令 `tools` `project.json` 来执行此操作。

## <a name="package-manager-commands-require-powershell-5"></a>程序包管理器命令需要 PowerShell 5

如果你在 Visual Studio 中的包管理器控制台中使用实体框架命令，则需要确保已安装 PowerShell 5。 这是将在下一版本中删除的临时要求（有关详细信息，请参阅[问题 #5327](https://github.com/aspnet/EntityFramework/issues/5327) ）。

## <a name="using-imports-in-projectjson"></a>使用 project.js上的 "导入"

某些 EF Core 的依赖项尚不支持 .NET Standard。 .NET Standard 和 .NET Core 项目中的 EF Core 可能需要将 "导入" 添加到 project.js上以作为临时解决方法。

添加 EF 时，NuGet 还原将显示以下错误消息：

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

解决方法是手动导入可移植配置文件 "net451 + win8"。 这会强制 NuGet 将与此提供的二进制文件相匹配的二进制文件视为与 .NET Standard 兼容的框架，即使它们不是这样。 尽管 "net451 + win8" 与 .NET Standard 的兼容性不是100%，但其兼容性足以用于从 PCL 转换到 .NET Standard。 当 EF 的依赖项最终升级到 .NET Standard 时，可以删除导入。

可以在数组语法中将多个框架添加到 "导入"。 如果将其他库添加到项目，则可能需要其他导入。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

请参阅[问题 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。
