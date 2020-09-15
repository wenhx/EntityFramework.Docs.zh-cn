---
title: 依赖项解析-EF6
description: 实体框架6中的依赖项解析
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 24e932646f0c44f642bff3f6db321590abae1c8d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070544"
---
# <a name="dependency-resolution"></a>依赖项解析
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

从 EF6 开始，实体框架包含用于获取所需服务实现的通用机制。 也就是说，当 EF 使用某些接口或基类的实例时，它将要求使用接口或基类的具体实现。 这是通过使用 IDbDependencyResolver 接口实现的：  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

GetService 方法通常由 EF 调用，由 EF 或应用程序提供的 IDbDependencyResolver 的实现进行处理。 调用时，类型参数是所请求服务的接口或基类类型，并且密钥对象为 null 或提供有关所请求服务的上下文信息的对象。  

除非另有说明，否则返回的任何对象都必须是线程安全的，因为它可用作单一实例。 在许多情况下，返回的对象是一个工厂，在这种情况下，工厂本身必须是线程安全的，但从工厂返回的对象不需要是线程安全的，因为每次使用都从工厂请求了一个新实例。

本文不包含有关如何实现 IDbDependencyResolver 的完整详细信息，而是充当服务类型的参考 (也就是说，) 的接口和基类类型，EF 将为这些调用调用 GetService 和密钥对象的语义。

## <a name="systemdataentityidatabaseinitializertcontext"></a>IDatabaseInitializer<TContext （）\>  

**引入的版本**： ef 6.0。0  

**返回的对象**：给定上下文类型的数据库初始值设定项  

**键**：未使用;将为 null  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>函数函数的 Func<MigrationSqlGenerator\>  

**引入的版本**： ef 6.0。0

**返回的对象**：用于创建 SQL 生成器的工厂，该生成器可用于迁移和导致创建数据库的其他操作（如使用数据库初始值设定项创建数据库）。  

**Key**：包含 ADO.NET 提供程序固定名称的字符串，该名称指定要为其生成 SQL 的数据库的类型。 例如，对于密钥 "SqlClient"，将返回 SQL Server SQL 生成器。  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="systemdataentitycorecommondbproviderservices"></a>Dbproviderservices.createdatabase 的数据的实体。  

**引入的版本**： ef 6.0。0  

**返回的对象**：用于给定提供程序固定名称的 EF 提供程序  

**Key**：包含 ADO.NET 提供程序固定名称的字符串，用于指定需要提供程序的数据库类型。 例如，对于密钥 "SqlClient"，将返回 SQL Server 提供程序。  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>"IDbConnectionFactory"。  

**引入的版本**： ef 6.0。0  

**返回的对象**：当 EF 按约定创建数据库连接时将使用的连接工厂。 也就是说，如果不向 EF 提供连接或连接字符串，并且在 app.config 或 web.config 中找不到连接字符串，则使用此服务按约定创建连接。 默认情况下，更改连接工厂可以允许 EF 使用不同类型的数据库 (例如，SQL Server Compact Edition) 。  

**键**：未使用;将为 null  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>"IManifestTokenService"。  

**引入的版本**： ef 6.0。0  

**返回的对象**：一种服务，可从连接生成提供程序清单标记。 此服务通常以两种方式使用。 首先，可以使用它来避免 Code First 在生成模型时连接到数据库。 其次，它可以用于强制 Code First 为特定的数据库版本生成模型-例如，即使使用 SQL Server 2008，也可以强制对 SQL Server 2005 的模型进行强制。  

**对象生存期**：单一实例--相同的对象可以多次使用，并且可以同时由不同的线程使用  

**键**：未使用;将为 null  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>"IDbProviderFactoryService"。  

**引入的版本**： ef 6.0。0  

**返回的对象**：可以从给定连接获得提供程序工厂的服务。 在 .NET 4.5 中，提供程序可以从连接中公开访问。 在 .NET 4 中，此服务的默认实现使用一些试探法来查找匹配的提供程序。 如果这些失败，则可以注册此服务的新实现以提供适当的解决方法。  

**键**：未使用;将为 null  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func<DbContext，IDbModelCacheKey。\>  

**引入的版本**： ef 6.0。0  

**返回的对象**：将为给定上下文生成模型缓存键的工厂。 默认情况下，EF 按每个提供程序的 DbContext 类型缓存一个模型。 此服务的不同实现可用于向缓存键添加其他信息，如架构名称。  

**键**：未使用;将为 null  

## <a name="systemdataentityspatialdbspatialservices"></a>DbSpatialServices..。  

**引入的版本**： ef 6.0。0  

**返回的对象**：一个 EF 空间提供程序，它添加对 geography 和 geometry 空间类型的基本 EF 提供程序的支持。  

**键**：以两种方式请求 DbSptialServices。 首先，使用 DbProviderInfo 对象请求特定于提供程序的空间服务 (该对象包含固定名称和清单标记) 为密钥。 其次，无关键字就可以要求 DbSpatialServices。 这用于解析在创建独立的 DbGeography 或 DbGeometry 类型时使用的 "全局空间提供程序"。  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>函数函数的 Func<IDbExecutionStrategy\>  

**引入的版本**： ef 6.0。0  

**返回的对象**：一个工厂，用于创建一个服务，该服务允许提供程序在针对数据库执行查询和命令时实现重试或其他行为。 如果未提供实现，则 EF 只会执行命令并传播引发的任何异常。 对于 SQL Server 此服务用于提供重试策略，这在对基于云的数据库服务器（如 SQL Azure）运行时特别有用。  

**Key**：包含提供程序固定名称的 ExecutionStrategyKey 对象，还可以选择要对其使用执行策略的服务器名称。  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func<DbConnection，string，HistoryContext。\>  

**引入的版本**： ef 6.0。0  

**返回的对象**：允许提供程序配置 HISTORYCONTEXT 与 EF 迁移使用的表的映射的工厂 `__MigrationHistory` 。 HistoryContext 是一个 Code First DbContext，可以使用普通 Fluent API 进行配置，以更改表名称和列映射规范等项。  

**键**：未使用;将为 null  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="systemdatacommondbproviderfactory"></a>DbProviderFactory。  

**引入的版本**： ef 6.0。0  

**返回的对象**：要用于给定提供程序固定名称的 ADO.NET 提供程序。  

**Key**：包含 ADO.NET 提供程序固定名称的字符串  

>[!NOTE]
> 此服务通常不会直接更改，因为默认实现使用普通的 ADO.NET 提供程序注册。 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>"IProviderInvariantName"。  

**引入的版本**： ef 6.0。0  

**返回的对象**：用于确定给定类型的 DbProviderFactory 的提供程序固定名称的服务。 此服务的默认实现使用 ADO.NET 提供程序注册。 这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。  

**Key**：需要固定名称的 DbProviderFactory 实例。  

>[!NOTE]
> 有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>ViewGeneration. IViewAssemblyCache 的数据。  

**引入的版本**： ef 6.0。0  

**返回的对象**：包含预先生成的视图的程序集的缓存。 替换通常用于让 EF 知道哪些程序集包含预生成的视图，而无需执行任何发现。  

**键**：未使用;将为 null  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>复数形式... IPluralizationService

**引入的版本**： ef 6.0。0  

**返回的对象**：由 EF 用于复数形式和确定所名称的服务。 默认情况下，使用英语复数形式服务。  

**键**：未使用;将为 null  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>IDbInterceptor 的数据的元数据  

**引入的版本**： ef 6.0。0

**返回的对象**：在应用程序启动时应注册的所有侦听器。 请注意，这些对象是使用 GetServices 调用请求的，所有依赖关系解析程序返回的所有拦截器都将进行注册。

**键**：未使用;将为 null。  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func<DbContext，Action<string，DatabaseLogFormatter 的字符串，该字符串 \> 的数据\>  

**引入的版本**： ef 6.0。0  

**返回的对象**：一个工厂，用于创建将在上下文时使用的数据库日志格式化程序。在给定的上下文中设置了数据库. 日志属性。  

**键**：未使用;将为 null。  

## <a name="funcsystemdataentitydbcontext"></a>函数 Func<DbContext\>  

**引入的版本**： ef 6.1。0  

**返回的对象**：当上下文不具有可访问的无参数构造函数时，将用于创建用于迁移的上下文实例的工厂。  

**Key**：需要工厂的派生 DbContext 的类型的类型对象。  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>函数 IMetadataAnnotationSerializer 的函数函数的<函数。\>  

**引入的版本**： ef 6.1。0  

**返回的对象**：一个工厂，用于创建用于序列化强类型自定义批注的序列化程序，以便可以将其序列化并 DESTERILIZED 到 XML 中以便在 Code First 迁移中使用。  

**键**：要序列化或反序列化的批注的名称。  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>函数函数的 Func<TransactionHandler\>  

**引入的版本**： ef 6.1。0  

**返回的对象**：将用于为事务创建处理程序的工厂，以便在处理提交失败的情况下可以应用特殊处理。  

**Key**：包含提供程序固定名称的 ExecutionStrategyKey 对象，还可以选择要对其使用事务处理程序的服务器名称。  
