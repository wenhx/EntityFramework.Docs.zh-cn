---
title: 多个提供程序的迁移-EF Core
description: 在将多个数据库提供程序定位到 Entity Framework Core 时使用迁移来管理数据库架构
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429775"
---
# <a name="migrations-with-multiple-providers"></a>迁移多个提供程序

[EF Core 工具](xref:core/cli/index)仅基架活动提供程序的迁移。 但有时，您可能希望使用多个提供程序 (例如 Microsoft SQL Server 和 SQLite) DbContext。 通过维护多个迁移集来处理此情况-每个提供程序一个用于每个模型更改，并向每个集合添加一个迁移。

## <a name="using-multiple-context-types"></a>使用多种上下文类型

创建多个迁移集的一种方法是为每个提供程序使用一个 DbContext 类型。

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

添加新迁移时指定上下文类型。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> 无需指定输出目录即可进行后续迁移，因为它们是以同级方式创建的。

## <a name="using-one-context-type"></a>使用一个上下文类型

还可以使用一种 DbContext 类型。 目前需要将迁移迁移到单独的程序集中。 有关设置项目的说明，请参阅 [使用单独的迁移项目](xref:core/managing-schemas/migrations/projects) 。

> [!TIP]
> 可在 [GitHub 示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations)中查看此文章的示例。

从 EF Core 5.0 开始，可以从工具将参数传递到应用程序。 这可以实现更简单的工作流，使其在运行工具时无需对项目进行手动更改。

下面是一种在使用 [泛型宿主](/dotnet/core/extensions/generic-host)时良好运行的模式。

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

由于默认主机生成器从命令行参数读取配置，因此您可以在运行工具时指定提供程序。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> 该 `--` 令牌指示 `dotnet ef` 将后面的所有内容视为一个参数，而不会尝试将它们作为选项进行分析。 不使用的任何额外参数 `dotnet ef` 都将转发到应用程序。

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> EF Core 5.0 中添加了指定应用的其他参数的功能。 如果使用的是较旧版本，请改用环境变量指定配置值。
