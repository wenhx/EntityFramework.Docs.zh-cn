---
title: 实体框架6提供程序模型-EF6
description: 实体框架6中的实体框架6提供程序模型
author: ajcvickers
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: 15b5443ff05b5c8704f80d4f2f83b4ed20edd1c0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063187"
---
# <a name="the-entity-framework-6-provider-model"></a>实体框架6提供程序模型

实体框架提供程序模型允许实体框架与不同类型的数据库服务器一起使用。 例如，可以插入一个提供程序以允许对 Microsoft SQL Server 使用 EF，而另一个提供程序可以插入到中，以允许在 Microsoft SQL Server Compact 版本中使用 EF。 [实体框架提供程序](xref:ef6/fundamentals/providers/index)"页上提供了可识别的 EF6 提供程序。

EF 与提供程序交互的方式需要进行某些更改，以允许在开源许可证下释放 EF。 这些更改要求针对 EF6 程序集重新生成 EF 提供程序，同时提供用于注册提供程序的新机制。

## <a name="rebuilding"></a>重建

对于 EF6，以前 .NET Framework 的核心代码现在作为带外 (OOB) 程序集发货。 有关如何针对 EF6 生成应用程序的详细信息，请参阅 [更新应用程序的 EF6](xref:ef6/what-is-new/upgrading-to-ef6) 页。 还需要使用这些说明重新生成提供程序。

## <a name="provider-types-overview"></a>提供程序类型概述

EF 提供程序实际上是由 CLR 类型定义的特定于提供程序的服务的集合，这些服务通过基类的 (扩展) 或实现接口)  (。 其中的两个服务都是基本的功能，而 EF 是必需的。 其他选项是可选的，仅在需要特定功能时才需要实现，并且/或者这些服务的默认实现对于目标特定数据库服务器不起作用。

## <a name="fundamental-provider-types"></a>基本提供程序类型

### <a name="dbproviderfactory"></a>DbProviderFactory

EF 依赖于从 [DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) 派生的类型，以执行所有低级别的数据库访问。 DbProviderFactory 实际上不是 EF 的一部分，而是 .NET Framework 中的一个类，它为 ADO.NET 提供程序提供了一个入口点，这些提供程序可由 EF、其他 O/RMs 或应用程序直接使用以获取连接的实例、命令、参数和其他 ADO.NET 抽象的实例。 有关 DbProviderFactory 的详细信息，请参阅 [ADO.NET 的 MSDN 文档](https://msdn.microsoft.com/library/a6cd7c08.aspx)。

### <a name="dbproviderservices"></a>Dbproviderservices.createdatabase

EF 依赖于从 Dbproviderservices.createdatabase 派生的类型，以便在 ADO.NET 提供程序已提供的功能之上提供 EF 所需的其他功能。 在 EF 的较早版本中，Dbproviderservices.createdatabase 类是 .NET Framework 的一部分，并且在 System.web 命名空间中找到。 从 EF6 开始，此类现在是 EntityFramework.dll 的一部分，并且位于命名空间中。

可在 [MSDN](https://msdn.microsoft.com/library/ee789835.aspx)上找到有关 dbproviderservices.createdatabase 实现的基本功能的更多详细信息。 但请注意，在撰写本文时，尽管大部分概念仍然有效，但不会对 EF6 进行更新。 Dbproviderservices.createdatabase 的 SQL Server 和 SQL Server Compact 实现也签入到 [开源基本代码](https://github.com/aspnet/EntityFramework6/) ，并可用作其他实现的有用引用。

在早期版本的 EF 中，Dbproviderservices.createdatabase 实现直接从 ADO.NET 提供程序获取。 这是通过将 DbProviderFactory 强制转换为 IServiceProvider 并调用 GetService 方法来完成的。 这会将 EF 提供程序紧密耦合到 DbProviderFactory。 此耦合阻止的 EF 被移出 .NET Framework，因此对于 EF6，已删除此紧密耦合，并且 Dbproviderservices.createdatabase 的实现现在直接在应用程序的配置文件或基于代码的配置中注册，如下面的 _注册 dbproviderservices.createdatabase_ 部分中所述。

## <a name="additional-services"></a>其他服务

除了上面所述的基本服务外，EF 还使用许多其他服务，它们始终或有时是特定于提供程序的服务。 这些服务的默认特定于提供程序的实现可由 Dbproviderservices.createdatabase 实现提供。 应用程序还可以覆盖这些服务的实现，或在 Dbproviderservices.createdatabase 类型不提供默认值时提供实现。 下面更详细地介绍了 _这一情况_ 。

下面列出了提供程序可能对提供程序感兴趣的其他服务类型。 有关每种服务类型的更多详细信息，请参阅 API 文档。

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

这是一种可选的服务，当对数据库执行查询和命令时，此服务允许提供程序实现重试或其他行为。 如果未提供实现，则 EF 只会执行命令并传播引发的任何异常。 对于 SQL Server 此服务用于提供重试策略，这在对基于云的数据库服务器（如 SQL Azure）运行时特别有用。

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

这是一种可选服务，允许提供程序在只给定数据库名称的情况下按约定创建 DbConnection 对象。 请注意，虽然此服务可以通过 Dbproviderservices.createdatabase 实现进行解析，但它已存在于 EF 4.1，并且还可以在配置文件或代码中显式设置。 如果该提供程序注册为默认提供程序，则该提供程序仅有机会解析此服务 () 下面 _的默认提供程序中查看默认提供程序_ ，以及是否未在其他位置设置默认的连接工厂。

### <a name="dbspatialservices"></a>DbSpatialServices

这是一种可选服务，允许提供商添加对地理空间类型和几何空间类型的支持。 必须提供此服务的实现，应用程序才能将 EF 与空间类型一起使用。 将以两种方式请求 DbSptialServices。 首先，使用 DbProviderInfo 对象请求特定于提供程序的空间服务 (该对象包含固定名称和清单标记) 为密钥。 其次，无关键字就可以要求 DbSpatialServices。 这用于解析在创建独立的 DbGeography 或 DbGeometry 类型时使用的 "全局空间提供程序"。

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

这是一项可选的服务，它允许将 EF 迁移用于通过 Code First 创建和修改数据库架构时使用的 SQL 生成。 需要实现才能支持迁移。 如果提供了实现，则在使用数据库初始值设定项或 Database. Create 方法创建数据库时，也将使用它。

### <a name="funcdbconnection-string-historycontextfactory"></a>Func<DbConnection，string，HistoryContextFactory>

这是一种可选的服务，它允许提供程序配置 HistoryContext 与 `__MigrationHistory` EF 迁移使用的表之间的映射。 HistoryContext 是一个 Code First DbContext，可以使用普通 Fluent API 进行配置，以更改表名称和列映射规范等项。 如果该提供程序支持所有默认表和列映射，则由 EF 为所有提供程序返回的此服务的默认实现可能适用于给定的数据库服务器。 在这种情况下，提供程序不需要提供此服务的实现。

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

这是一种可选的服务，用于从给定的 DbConnection 对象中获取正确的 DbProviderFactory。 此服务的默认实现由 EF 为所有提供程序返回，旨在适用于所有提供程序。 但是，在 .NET 4 上运行时，如果 DbProviderFactory 的 DbConnections，则无法从一个公共访问。 因此，EF 使用一些试探法搜索已注册的提供程序以找到匹配项。 某些提供程序的这种试探方法可能会失败，并且在这种情况下，提供程序应提供新的实现。

## <a name="registering-dbproviderservices"></a>注册 Dbproviderservices.createdatabase

Dbproviderservices.createdatabase 实现可在应用程序的配置文件中注册 ( # A0 或 web.config) 或使用基于代码的配置。 在任一情况下，注册都使用提供程序的 "固定名称" 作为密钥。 这允许在单个应用程序中注册和使用多个提供程序。 用于 EF 注册的固定名称与用于 ADO.NET 提供程序注册和连接字符串的固定名称相同。 例如，对于 SQL Server 使用固定名称 "SqlClient"。

### <a name="config-file-registration"></a>配置文件注册

要使用的 Dbproviderservices.createdatabase 类型在应用程序配置文件的 entityFramework 节的提供程序列表中注册为提供程序元素。 例如：

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

_类型_字符串必须是要使用的 dbproviderservices.createdatabase 实现的程序集限定的类型名称。

### <a name="code-based-registration"></a>基于代码的注册

从 EF6 提供程序开始，还可以使用代码注册。 这允许使用 EF 提供程序，而无需对应用程序的配置文件进行任何更改。 若要使用基于代码的配置，应用程序应按照 [基于代码的配置文档](https://msdn.com/data/jj680699)中所述创建 DbConfiguration 类。 然后，DbConfiguration 类的构造函数应调用 SetProviderServices 来注册 EF 提供程序。 例如：

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>解析其他服务

如前所述，在 " _提供程序类型概述_ " 部分中，还可以使用 dbproviderservices.createdatabase 类来解析其他服务。 这是可能的，因为 Dbproviderservices.createdatabase 实现了 IDbDependencyResolver，并且每个注册的 Dbproviderservices.createdatabase 类型都添加为 "默认冲突解决程序"。 [依赖项解析](xref:ef6/fundamentals/configuring/dependency-resolution)中更详细地介绍了 IDbDpendencyResolver 机制。 但是，不需要了解此规范中的所有概念来解析提供程序中的其他服务。

提供程序解析附加服务的最常见方法是为 Dbproviderservices.createdatabase 类的构造函数中的每个服务调用 Dbproviderservices.createdatabase。 例如，SqlProviderServices (EF provider for SQL Server) 具有类似于以下内容的代码进行初始化：

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

此构造函数使用以下帮助器类：

*   SingletonDependencyResolver：提供一种解析单独服务的简单方法，即每次调用 GetService 时都为其返回同一个实例的服务。 暂时性服务通常注册为一个单一实例，将用于按需创建暂时性实例。
*   ExecutionStrategyResolver：特定于返回 IExecutionStrategy 实现的解析程序。

还可以重写 Dbproviderservices.createdatabase 并直接解析附加服务，而不是使用 Dbproviderservices.createdatabase。 AddDependencyResolver。 当 EF 需要由特定类型定义的服务时，将调用此方法，在某些情况下，将调用此方法作为给定的键。 如果服务可以返回服务，则该方法应返回它，或者返回 null 以选择退出该服务，而另一个类可以解决它。 例如，若要解析默认的连接工厂，GetService 中的代码可能如下所示：

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>注册顺序

在应用程序的配置文件中注册多个 Dbproviderservices.createdatabase 实现时，它们将按照它们列出的顺序添加为辅助解析程序。 由于解析程序始终添加到辅助解析程序链的顶部，这意味着，列表末尾的提供程序将有机会解析其他依赖项。 首先， (这可能是一个非常直观的示例，但如果你认为每个提供程序都不在列表中，并将它堆积在现有的提供商之上，则这一点非常有意义。 ) 

此顺序通常并不重要，因为大多数提供程序服务都是特定于提供程序的，并使用提供程序固定名称进行键控。 但是，对于不是由提供程序固定名称或其他特定于提供程序的密钥进行键控的服务，将根据此顺序解析服务。 例如，如果未在其他任何位置以不同方式显式设置，则默认连接工厂将来自链中最顶层的提供程序。

## <a name="additional-config-file-registrations"></a>其他配置文件注册

可以直接在应用程序的配置文件中直接注册上述部分所述的其他提供程序服务。 完成此操作后，将使用配置文件中的注册，而不是 Dbproviderservices.createdatabase 实现的 GetService 方法返回的任何内容。

### <a name="registering-the-default-connection-factory"></a>注册默认的连接工厂

从 EF5 开始，EntityFramework NuGet 包自动在配置文件中注册 SQL Express 连接工厂或 LocalDb 连接工厂。

例如：

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

_类型_为默认连接工厂的程序集限定类型名称，该名称必须实现 IDbConnectionFactory。

建议提供程序 NuGet 包在安装时以这种方式设置默认连接工厂。 请参阅下面 _的提供程序的 NuGet 包_ 。

## <a name="additional-ef6-provider-changes"></a>其他 EF6 提供程序更改

### <a name="spatial-provider-changes"></a>空间提供程序更改

支持空间类型的提供程序现在必须在派生自 DbSpatialDataReader 的类上实现一些附加方法：

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

此外，还提供了一些新的异步版本，建议将它们作为默认的实现委托给同步方法，并因此不会以异步方式执行：

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>对可枚举的本机支持。包含

EF6 引入了一个新的表达式类型 DbInExpression，此类型已添加到解决有关使用可枚举的的性能问题。包含在 LINQ 查询中。 DbProviderManifest 类具有新的虚方法 SupportsInExpression，该方法由 EF 调用来确定提供程序是否处理新的表达式类型。 为了与现有提供程序实现兼容，此方法返回 false。 为了从此改进中获益，EF6 提供程序可添加代码来处理 DbInExpression，并覆盖 SupportsInExpression 以返回 true。 可以通过调用 DbExpressionBuilder.In 方法创建 DbInExpression 的实例。 DbInExpression 实例由 DbExpression 组成，通常表示表列，以及用于测试匹配项的 DbConstantExpression 列表。

## <a name="nuget-packages-for-providers"></a>提供程序的 NuGet 包

使 EF6 提供程序可用的一种方法是将其发布为 NuGet 包。 使用 NuGet 包具有以下优势：

*   可以轻松地将提供程序注册到应用程序的配置文件中，使用 NuGet
*   可以对配置文件进行其他更改，以设置默认的连接工厂，以便约定建立的连接将使用已注册的提供程序
*   NuGet 处理添加绑定重定向，以便 EF6 提供程序在新的 EF 包发布后仍可继续工作

下面是一个包含在 [开源代码库](https://github.com/aspnet/entityframework6)中的 EntityFramework SqlServerCompact 包。 此包提供了一个用于创建 EF 提供程序 NuGet 包的好模板。

### <a name="powershell-commands"></a>PowerShell 命令

安装 EntityFramework NuGet 包时，它会注册一个包含两个命令的 PowerShell 模块，这些命令对于提供程序包非常有用：

*   Add-EFProvider 在目标项目的配置文件中添加提供程序的新实体，并确保其位于已注册提供程序列表的末尾。
*   Add-EFDefaultConnectionFactory 在目标项目的配置文件中添加或更新 defaultConnectionFactory 注册。

这两个命令都负责将 entityFramework 节添加到配置文件中，并在必要时添加提供程序集合。

这是为了从 install.ps1 的 NuGet 脚本中调用这些命令。 例如，SQL Compact 提供程序 install.ps1 如下所示：

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

可以在 "包管理器控制台" 窗口中使用 get-help 获取有关这些命令的详细信息。

## <a name="wrapping-providers"></a>包装提供程序

包装提供程序是一个 EF 和/或 ADO.NET 提供程序，它包装现有提供程序以使用其他功能（如分析或跟踪功能）对其进行扩展。 包装提供程序可以通过正常方式进行注册，但在运行时通过拦截与提供程序相关的服务的解决方法，在运行时设置包装提供程序通常更为方便。 DbConfiguration 类上的静态事件 OnLockingConfiguration 可用于执行此操作。

在 EF 确定了将从其获取应用程序域的所有 EF 配置之后，但在锁定以供使用之前，将调用 OnLockingConfiguration。 在应用启动 (在使用 EF 之前) 应用应为此事件注册事件处理程序。  (我们正在考虑添加对在配置文件中注册此处理程序的支持，但目前尚不支持。 ) 事件处理程序应为需要包装的每个服务调用 ReplaceService。  

例如，若要将 IDbConnectionFactory 和 DbProviderService 进行包装，应注册类似于以下内容的处理程序：

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

已解决的服务现在应与用于解析服务的密钥一起打包到处理程序。 然后，处理程序可以包装此服务，并将返回的服务替换为包装的版本。

## <a name="resolving-a-dbproviderfactory-with-ef"></a>使用 EF 解析 DbProviderFactory

DbProviderFactory 是 EF 所需的一种基本提供程序类型，如上面的 " _提供程序类型概述_ " 部分所述。 如上所述，它不是 EF 类型，注册通常不是 EF 配置的一部分，而是 machine.config 文件和/或应用程序的配置文件中的常规 ADO.NET 提供程序注册。

尽管在查找要使用的 DbProviderFactory 时，此 EF 仍使用其常规依赖项解析机制。 默认冲突解决程序使用配置文件中的常规 ADO.NET 注册，因此这通常是透明的。 但由于使用的是正常的依赖项解析机制，这意味着即使未完成普通 ADO.NET 注册，也可以使用 IDbDependencyResolver 来解析 DbProviderFactory。

以这种方式解析 DbProviderFactory 有几个含义：

*   使用基于代码的配置的应用程序可在其 DbConfiguration 类中添加调用来注册相应的 DbProviderFactory。 这对于不想 (或无法) 使用任何基于文件的配置的应用程序特别有用。
*   该服务可以使用 ReplaceService 进行包装或替换，如以上 _包装提供商_ 部分所述
*   理论上，Dbproviderservices.createdatabase 实现可以解析 DbProviderFactory。

执行以上任一操作时需要注意的重要一点是，它们只会影响按 EF 查找 DbProviderFactory。 其他非 EF 代码可能仍会预期 ADO.NET 提供程序以正常方式注册，如果找不到注册，可能会失败。 出于此原因，通过正常的 ADO.NET 方式注册 DbProviderFactory 通常更好。

### <a name="related-services"></a>相关服务

如果使用 EF 解析 DbProviderFactory，则它还应该解析 IProviderInvariantName 和 IDbProviderFactoryResolver 服务。

IProviderInvariantName 是一种服务，用于确定给定类型 DbProviderFactory 的提供程序固定名称。 此服务的默认实现使用 ADO.NET 提供程序注册。 这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。 请注意，使用 DbConfiguration. SetProviderFactory 方法时，会自动添加此服务的解析程序。

如上面的 " _提供程序类型概述_ " 部分所述，IDbProviderFactoryResolver 用于从给定的 DbConnection 对象获取正确的 DbProviderFactory。 此服务在 .NET 4 上运行时的默认实现使用 ADO.NET 提供程序注册。 这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。
