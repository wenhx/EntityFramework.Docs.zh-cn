---
title: 从先前版本升级到 EF Core 2 - EF Core
description: 升级到 Entity Framework Core 2.0 的说明和注意事项
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: 2bd88c0c52bcef8d5a9777f2cbd546a2787d67a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620678"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>将应用程序从先前版本升级到 EF Core 2.0

我们利用这个机会在 2.0 中极大地改进了现有 API 和行为。 有一些改进需要修改现有应用程序代码，不过我们认为这对大多数应用程序的影响很小，在大多数情况下只需重新编译和进行极少的指导更改即可替换过时的 API。

将现有应用程序更新到 EF Core 2.0 可能需要完成以下事项：

1. 将应用程序的目标 .NET 实现升级到支持 .NET Standard 2.0 的实现。 请参阅[支持的 .NET 实现](xref:core/platforms/index)了解详细信息。

2. 确定与 EF Core 2.0 兼容的目标数据库的提供程序。 请参阅下面的 [EF Core 2.0 需要 2.0 数据库提供程序](#ef-core-20-requires-a-20-database-provider)。

3. 将所有 EF Core 包（运行时和工具）升级为 2.0。 请参阅[安装 EF Core](xref:core/get-started/install/index) 了解详细信息。

4. 进行任何必要的代码更改，以补充本文档其余部分所述的中断性变更。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core 目前包括 EF Core

面向 ASP.NET Core 2.0 的应用程序可以使用 EF Core 2.0，而不需要第三方数据库提供程序以外的其他依赖项。 但是，面向旧版 ASP.NET Core 的应用程序必须升级到 ASP.NET Core 2.0，才能使用 EF Core 2.0。 有关将 ASP.NET Core 应用程序升级到 2.0 的更多详细信息，请参阅[关于该主题的 ASP.NET Core 文档](/aspnet/core/migration/1x-to-2x/)。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>在 ASP.NET Core 中获取应用程序服务的新方法

针对 ASP.NET Core Web 应用程序的建议模式已针对 2.0 进行了更新，这打破了 EF Core 在 1.x 中使用的设计时逻辑。 以前在设计时，EF Core 会尝试直接调用 `Startup.ConfigureServices` 以访问应用程序的服务提供程序。 在 ASP.NET Core 2.0 中，将在 `Startup` 类之外初始化配置。 使用 EF Core 的应用程序通常从配置中访问其连接字符串，因此单是 `Startup` 就已经不够了。 如果升级 ASP.NET Core 1.x 应用程序，则使用 EF Core 工具时可能会收到以下错误。

> 未在“ApplicationContext”上找到任何无参数构造函数。 向“ApplicationContext”添加一个无参数构造函数，或者在与“ApplicationContext”所在的程序集中添加“IDesignTimeDbContextFactory&lt;ApplicationContext&gt;”的实现

ASP.NET Core 2.0 的默认模板中添加了新的设计时挂钩。 静态 `Program.BuildWebHost` 方法使 EF Core 能够在设计时访问应用程序的服务提供程序。 如果要升级 ASP.NET Core 1.x 应用程序，则需要将 `Program` 类更新为以下相似内容。

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

将应用程序更新到 2.0 时，强烈建议采用此新模式，Entity Framework Core 迁移等产品功能需要此模式才能正常运行。 另一种常见的替代方法是[实现 IDesignTimeDbContextFactory\<TContext>](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。

## <a name="idbcontextfactory-renamed"></a>已重命名 IDbContextFactory

为了支持不同的应用程序模式并使用户能够更好地控制其 `DbContext` 在设计时的使用方式，我们过去提供了 `IDbContextFactory<TContext>` 接口。 在设计时，EF Core 工具将在项目中发现此接口的实现，并使用它来创建 `DbContext` 对象。

此接口具有一个非常通用的名称，会误导某些用户尝试将其重复用于其他创建 `DbContext` 的场景。 当 EF 工具尝试在设计时使用他们的实现并导致 `Update-Database` 或 `dotnet ef database update` 等命令失败时，他们毫无准备。

为了传达此接口的强设计时语义，我们已将其重命名为 `IDesignTimeDbContextFactory<TContext>`。

对于 2.0 版本，`IDbContextFactory<TContext>` 仍存在，但被标记为已过时。

## <a name="dbcontextfactoryoptions-removed"></a>已删除 DbContextFactoryOptions

由于上述 ASP.NET Core 2.0 更改，我们发现新的 `IDesignTimeDbContextFactory<TContext>` 接口不再需要 `DbContextFactoryOptions`。 下面是你应使用的替代方法。

| DbContextFactoryOptions | 替代方法                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>已更改设计时工作目录

ASP.NET Core 2.0 更改还要求 `dotnet ef` 使用的工作目录与 Visual Studio 在运行应用程序时使用的工作目录保持一致。 此要求的一个明显副作用是，SQLite 文件名现在与项目目录相关，而不是像之前那样与输出目录相关。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 需要 2.0 数据库提供程序

对于 EF Core 2.0，我们在数据库提供程序的工作方式方面进行了许多简化和改进。 这意味着，1.0.x 和 1.1.x 提供程序将不再适用于 EF Core 2.0。

SQL Server 和 SQLite 提供程序由 EF 团队提供，2.0 版本将作为 2.0 发布的一部分提供。 [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL) 和 [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 的开源第三方提供程序已针对 2.0 进行了更新。 对于所有其他提供程序，请与提供程序作者联系。

## <a name="logging-and-diagnostics-events-have-changed"></a>日志记录和诊断事件已更改

注意：这些更改不应影响大部分应用程序代码。

发送给 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 的消息的事件 ID 在 2.0 中已更改。 现在，事件 ID 在 EF Core 代码内具有唯一性。 这些消息现在还遵循 MVC 等所用的结构化日志记录的标准模式。

记录器类别也已更改。 现提供通过 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 访问的熟知类别集。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件现使用与相应 `ILogger` 消息相同的事件 ID 名称。 事件负载是派生自 [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata) 的所有名义类型。

事件 ID、负载类型和类别记录在 [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) 和 [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) 类中。

ID 也从 Microsoft.EntityFrameworkCore.Infrastructure 移动到了新的 Microsoft.EntityFrameworkCore.Diagnostics 命名空间。

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core 关系元数据 API 更改

EF Core 2.0 现将对所用的每个不同提供程序生成不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。 这对应用程序而言通常是透明的。 这有助于简化较低级别的元数据 API，从而始终通过调用 `.Relational`（而不是 `.SqlServer`、`.Sqlite` 等）来访问常见关系元数据概念。例如，1.1.x 代码如下：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

现在应这样编写：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

现在可使用扩展方法（而不是 `ForSqlServerToTable` 方法）基于当前使用的提供程序来编写条件代码。 例如：

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

请注意，此更改仅适用于为所有关系提供程序定义的 API/元数据。 仅特定于单个提供程序时，API 和元数据保持不变。 例如，聚集索引特定于 SQL Server，因此仍必须使用 `ForSqlServerIsClustered` 和 `.SqlServer().IsClustered()`。

## <a name="dont-take-control-of-the-ef-service-provider"></a>不控制 EF 服务提供程序

EF Core 将内部 `IServiceProvider`（依赖关系注入容器）用于其内部实现。 应用程序应允许 EF Core 创建和管理此提供程序，特殊情况除外。 强烈建议删除对 `UseInternalServiceProvider` 的任何调用。 如果某应用程序确实需要调用 `UseInternalServiceProvider`，则请考虑[提交问题](https://github.com/dotnet/efcore/Issues)，这样我们就可以研究处理此情况的其他方法。

除非同时调用了 `UseInternalServiceProvider`，否则应用程序代码不需要调用 `AddEntityFramework`、`AddEntityFrameworkSqlServer` 等。 删除对 `AddEntityFramework` 或 `AddEntityFrameworkSqlServer` 等的任何现有调用之后，仍应按照之前的相同方式来使用 `AddDbContext`。

## <a name="in-memory-databases-must-be-named"></a>必须对内存中数据库进行命名

全局未命名内存中数据库已删除，而且必须对所有内存中数据库进行命名。 例如：

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

这将创建/使用名为“MyDatabase”的数据库。 如果使用同一名称再次调用 `UseInMemoryDatabase`，则将使用同一内存中数据库，以允许多个上下文实例共享它。

## <a name="read-only-api-changes"></a>只读 API 更改

`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave` 和 `IsStoreGeneratedAlways` 已弃用，并已替换为 [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) 和 [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。 这些行为适用于任何属性（不仅仅是存储生成的属性），可确定在插入到数据库行 (`BeforeSaveBehavior`) 或更新现有数据库行 (`AfterSaveBehavior`) 时应如何使用属性的值。

标记为 [OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated)（例如对于计算列）的属性将默认忽略当前在属性上设置的任何值。 这意味着，无论是否在跟踪的实体上设置或修改了任何值，都将始终获得存储生成的值。 通过设置其他 `Before\AfterSaveBehavior` 参数可以对此进行更改。

## <a name="new-clientsetnull-delete-behavior"></a>新的 ClientSetNull 删除行为

在以前的版本中，[DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) 具有针对上下文所跟踪实体的行为，该行为更接近于 `SetNull` 语义。 在 EF Core 2.0 中，引入了新的 `ClientSetNull` 行为作为可选关系的默认行为。 此行为具有针对跟踪的实体的 `SetNull` 语义和针对使用 EF Core 创建的数据库的 `Restrict` 行为。 根据我们的经验，这些是针对跟踪的实体和数据库的最需要/最有用的行为。 对可选关系进行设置时，现在 `DeleteBehavior.Restrict` 可用于跟踪的实体。

## <a name="provider-design-time-packages-removed"></a>已删除提供程序设计时包

已删除 `Microsoft.EntityFrameworkCore.Relational.Design` 包。 其内容已合并到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design` 中。

这会传播到提供程序设计时包。 这些包（`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design` 等）已删除，其内容已合并到主提供程序包中。

若要在 EF Core 2.0 中启用 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`，只需引用一个提供程序包：

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
