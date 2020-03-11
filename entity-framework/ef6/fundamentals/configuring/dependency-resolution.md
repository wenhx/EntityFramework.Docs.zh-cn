---
title: 依赖项解析-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
ms.openlocfilehash: 6082124481f5795bbcb62fff2bb6a58ecdcb48e4
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414793"
---
# <a name="dependency-resolution"></a><span data-ttu-id="e1085-102">依赖项解析</span><span class="sxs-lookup"><span data-stu-id="e1085-102">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="e1085-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="e1085-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e1085-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="e1085-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e1085-105">从 EF6 开始，实体框架包含用于获取所需服务实现的通用机制。</span><span class="sxs-lookup"><span data-stu-id="e1085-105">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="e1085-106">也就是说，当 EF 使用某些接口或基类的实例时，它将要求使用接口或基类的具体实现。</span><span class="sxs-lookup"><span data-stu-id="e1085-106">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="e1085-107">这是通过使用 IDbDependencyResolver 接口实现的：</span><span class="sxs-lookup"><span data-stu-id="e1085-107">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="e1085-108">GetService 方法通常由 EF 调用，由 EF 或应用程序提供的 IDbDependencyResolver 的实现进行处理。</span><span class="sxs-lookup"><span data-stu-id="e1085-108">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="e1085-109">调用时，类型参数是所请求服务的接口或基类类型，并且密钥对象为 null 或提供有关所请求服务的上下文信息的对象。</span><span class="sxs-lookup"><span data-stu-id="e1085-109">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="e1085-110">除非另有说明，否则返回的任何对象都必须是线程安全的，因为它可用作单一实例。</span><span class="sxs-lookup"><span data-stu-id="e1085-110">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="e1085-111">在许多情况下，返回的对象是一个工厂，在这种情况下，工厂本身必须是线程安全的，但从工厂返回的对象不需要是线程安全的，因为每次使用都从工厂请求了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="e1085-111">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="e1085-112">本文不包含有关如何实现 IDbDependencyResolver 的完整详细信息，而是充当 EF 为其调用 GetService 的服务类型（即接口和基类类型）的参考和其中每个对象的密钥对象的语义拨号.</span><span class="sxs-lookup"><span data-stu-id="e1085-112">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="e1085-113">IDatabaseInitializer < TContext\></span><span class="sxs-lookup"><span data-stu-id="e1085-113">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="e1085-114">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-114">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-115">**返回的对象**：给定上下文类型的数据库初始值设定项</span><span class="sxs-lookup"><span data-stu-id="e1085-115">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="e1085-116">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-116">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="e1085-117">MigrationSqlGenerator\> 的函数的 Func <</span><span class="sxs-lookup"><span data-stu-id="e1085-117">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="e1085-118">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-118">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="e1085-119">**返回的对象**：用于创建 SQL 生成器的工厂，该生成器可用于迁移和导致创建数据库的其他操作（如使用数据库初始值设定项创建数据库）。</span><span class="sxs-lookup"><span data-stu-id="e1085-119">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="e1085-120">**Key**：包含 ADO.NET 提供程序固定名称的字符串，该名称指定要为其生成 SQL 的数据库的类型。</span><span class="sxs-lookup"><span data-stu-id="e1085-120">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="e1085-121">例如，对于密钥 "SqlClient"，将返回 SQL Server SQL 生成器。</span><span class="sxs-lookup"><span data-stu-id="e1085-121">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-122">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-122">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="e1085-123">Dbproviderservices.createdatabase 的数据的实体。</span><span class="sxs-lookup"><span data-stu-id="e1085-123">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="e1085-124">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-124">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-125">**返回的对象**：用于给定提供程序固定名称的 EF 提供程序</span><span class="sxs-lookup"><span data-stu-id="e1085-125">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="e1085-126">**Key**：包含 ADO.NET 提供程序固定名称的字符串，用于指定需要提供程序的数据库类型。</span><span class="sxs-lookup"><span data-stu-id="e1085-126">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="e1085-127">例如，对于密钥 "SqlClient"，将返回 SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="e1085-127">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-128">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-128">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="e1085-129">"IDbConnectionFactory"。</span><span class="sxs-lookup"><span data-stu-id="e1085-129">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="e1085-130">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-130">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-131">**返回的对象**：当 EF 按约定创建数据库连接时将使用的连接工厂。</span><span class="sxs-lookup"><span data-stu-id="e1085-131">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="e1085-132">也就是说，如果不向 EF 提供连接或连接字符串，并且在 app.config 或 web.config 中找不到连接字符串，则将使用此服务按约定创建连接。</span><span class="sxs-lookup"><span data-stu-id="e1085-132">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="e1085-133">默认情况下，更改连接工厂可以允许 EF 使用其他类型的数据库（例如，SQL Server Compact Edition）。</span><span class="sxs-lookup"><span data-stu-id="e1085-133">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="e1085-134">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-134">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-135">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-135">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="e1085-136">"IManifestTokenService"。</span><span class="sxs-lookup"><span data-stu-id="e1085-136">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="e1085-137">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-137">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-138">**返回的对象**：一种服务，可从连接生成提供程序清单标记。</span><span class="sxs-lookup"><span data-stu-id="e1085-138">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="e1085-139">此服务通常以两种方式使用。</span><span class="sxs-lookup"><span data-stu-id="e1085-139">This service is typically used in two ways.</span></span> <span data-ttu-id="e1085-140">首先，可以使用它来避免 Code First 在生成模型时连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="e1085-140">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="e1085-141">其次，它可以用于强制 Code First 为特定的数据库版本生成模型-例如，即使使用 SQL Server 2008，也可以强制对 SQL Server 2005 的模型进行强制。</span><span class="sxs-lookup"><span data-stu-id="e1085-141">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="e1085-142">**对象生存期**：单一实例--相同的对象可以多次使用，并且可以同时由不同的线程使用</span><span class="sxs-lookup"><span data-stu-id="e1085-142">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="e1085-143">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-143">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="e1085-144">"IDbProviderFactoryService"。</span><span class="sxs-lookup"><span data-stu-id="e1085-144">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="e1085-145">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-145">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-146">**返回的对象**：可以从给定连接获得提供程序工厂的服务。</span><span class="sxs-lookup"><span data-stu-id="e1085-146">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="e1085-147">在 .NET 4.5 中，提供程序可以从连接中公开访问。</span><span class="sxs-lookup"><span data-stu-id="e1085-147">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="e1085-148">在 .NET 4 中，此服务的默认实现使用一些试探法来查找匹配的提供程序。</span><span class="sxs-lookup"><span data-stu-id="e1085-148">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="e1085-149">如果这些失败，则可以注册此服务的新实现以提供适当的解决方法。</span><span class="sxs-lookup"><span data-stu-id="e1085-149">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="e1085-150">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-150">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="e1085-151">Func < DbContext，IDbModelCacheKey 的\></span><span class="sxs-lookup"><span data-stu-id="e1085-151">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="e1085-152">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-152">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-153">**返回的对象**：将为给定上下文生成模型缓存键的工厂。</span><span class="sxs-lookup"><span data-stu-id="e1085-153">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="e1085-154">默认情况下，EF 按每个提供程序的 DbContext 类型缓存一个模型。</span><span class="sxs-lookup"><span data-stu-id="e1085-154">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="e1085-155">此服务的不同实现可用于向缓存键添加其他信息，如架构名称。</span><span class="sxs-lookup"><span data-stu-id="e1085-155">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="e1085-156">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-156">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="e1085-157">DbSpatialServices..。</span><span class="sxs-lookup"><span data-stu-id="e1085-157">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="e1085-158">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-158">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-159">**返回的对象**：一个 EF 空间提供程序，它添加对 geography 和 geometry 空间类型的基本 EF 提供程序的支持。</span><span class="sxs-lookup"><span data-stu-id="e1085-159">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="e1085-160">**键**：以两种方式请求 DbSptialServices。</span><span class="sxs-lookup"><span data-stu-id="e1085-160">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="e1085-161">首先，使用 DbProviderInfo 对象（其中包含固定名称和清单标记）来请求特定于提供程序的空间服务作为密钥。</span><span class="sxs-lookup"><span data-stu-id="e1085-161">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="e1085-162">其次，无关键字就可以要求 DbSpatialServices。</span><span class="sxs-lookup"><span data-stu-id="e1085-162">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="e1085-163">这用于解析在创建独立的 DbGeography 或 DbGeometry 类型时使用的 "全局空间提供程序"。</span><span class="sxs-lookup"><span data-stu-id="e1085-163">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-164">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-164">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="e1085-165">函数函数的 Func <\> IDbExecutionStrategy</span><span class="sxs-lookup"><span data-stu-id="e1085-165">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="e1085-166">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-166">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-167">**返回的对象**：一个工厂，用于创建一个服务，该服务允许提供程序在针对数据库执行查询和命令时实现重试或其他行为。</span><span class="sxs-lookup"><span data-stu-id="e1085-167">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="e1085-168">如果未提供实现，则 EF 只会执行命令并传播引发的任何异常。</span><span class="sxs-lookup"><span data-stu-id="e1085-168">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="e1085-169">对于 SQL Server 此服务用于提供重试策略，这在对基于云的数据库服务器（如 SQL Azure）运行时特别有用。</span><span class="sxs-lookup"><span data-stu-id="e1085-169">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="e1085-170">**Key**：包含提供程序固定名称的 ExecutionStrategyKey 对象，还可以选择要对其使用执行策略的服务器名称。</span><span class="sxs-lookup"><span data-stu-id="e1085-170">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-171">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-171">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="e1085-172">Func < DbConnection，string，HistoryContext，\></span><span class="sxs-lookup"><span data-stu-id="e1085-172">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="e1085-173">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-173">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-174">**返回的对象**：允许提供程序配置 HISTORYCONTEXT 与 EF 迁移使用的 `__MigrationHistory` 表的映射的工厂。</span><span class="sxs-lookup"><span data-stu-id="e1085-174">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="e1085-175">HistoryContext 是一个 Code First DbContext，可以使用普通 Fluent API 进行配置，以更改表名称和列映射规范等项。</span><span class="sxs-lookup"><span data-stu-id="e1085-175">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="e1085-176">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-176">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-177">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-177">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="e1085-178">DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="e1085-178">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="e1085-179">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-179">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-180">**返回的对象**：要用于给定提供程序固定名称的 ADO.NET 提供程序。</span><span class="sxs-lookup"><span data-stu-id="e1085-180">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="e1085-181">**Key**：包含 ADO.NET 提供程序固定名称的字符串</span><span class="sxs-lookup"><span data-stu-id="e1085-181">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-182">此服务通常不会直接更改，因为默认实现使用普通的 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="e1085-182">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="e1085-183">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-183">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="e1085-184">"IProviderInvariantName"。</span><span class="sxs-lookup"><span data-stu-id="e1085-184">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="e1085-185">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-185">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-186">**返回的对象**：用于确定给定类型的 DbProviderFactory 的提供程序固定名称的服务。</span><span class="sxs-lookup"><span data-stu-id="e1085-186">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="e1085-187">此服务的默认实现使用 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="e1085-187">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="e1085-188">这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。</span><span class="sxs-lookup"><span data-stu-id="e1085-188">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="e1085-189">**Key**：需要固定名称的 DbProviderFactory 实例。</span><span class="sxs-lookup"><span data-stu-id="e1085-189">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="e1085-190">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅[EF6 提供程序模型](~/ef6/fundamentals/providers/provider-model.md)部分。</span><span class="sxs-lookup"><span data-stu-id="e1085-190">For more details on provider-related services in EF6 see the [EF6 provider model](~/ef6/fundamentals/providers/provider-model.md) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="e1085-191">ViewGeneration. IViewAssemblyCache 的数据。</span><span class="sxs-lookup"><span data-stu-id="e1085-191">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="e1085-192">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-192">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-193">**返回的对象**：包含预先生成的视图的程序集的缓存。</span><span class="sxs-lookup"><span data-stu-id="e1085-193">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="e1085-194">替换通常用于让 EF 知道哪些程序集包含预生成的视图，而无需执行任何发现。</span><span class="sxs-lookup"><span data-stu-id="e1085-194">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="e1085-195">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-195">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="e1085-196">复数形式... IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="e1085-196">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="e1085-197">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-197">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-198">**返回的对象**：由 EF 用于复数形式和确定所名称的服务。</span><span class="sxs-lookup"><span data-stu-id="e1085-198">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="e1085-199">默认情况下，使用英语复数形式服务。</span><span class="sxs-lookup"><span data-stu-id="e1085-199">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="e1085-200">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="e1085-200">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="e1085-201">IDbInterceptor 的数据的元数据</span><span class="sxs-lookup"><span data-stu-id="e1085-201">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="e1085-202">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-202">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="e1085-203">**返回的对象**：在应用程序启动时应注册的所有侦听器。</span><span class="sxs-lookup"><span data-stu-id="e1085-203">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="e1085-204">请注意，这些对象是使用 GetServices 调用请求的，所有依赖关系解析程序返回的所有拦截器都将进行注册。</span><span class="sxs-lookup"><span data-stu-id="e1085-204">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="e1085-205">**键**：未使用;将为 null。</span><span class="sxs-lookup"><span data-stu-id="e1085-205">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="e1085-206">Func < DbContext，Action < string\>，DatabaseLogFormatter\> 的操作中执行的操作</span><span class="sxs-lookup"><span data-stu-id="e1085-206">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="e1085-207">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="e1085-207">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="e1085-208">**返回的对象**：一个工厂，用于创建将在上下文时使用的数据库日志格式化程序。在给定的上下文中设置了数据库. 日志属性。</span><span class="sxs-lookup"><span data-stu-id="e1085-208">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="e1085-209">**键**：未使用;将为 null。</span><span class="sxs-lookup"><span data-stu-id="e1085-209">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="e1085-210">Func < DbContext\></span><span class="sxs-lookup"><span data-stu-id="e1085-210">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="e1085-211">**引入的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="e1085-211">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="e1085-212">**返回的对象**：当上下文不具有可访问的无参数构造函数时，将用于创建用于迁移的上下文实例的工厂。</span><span class="sxs-lookup"><span data-stu-id="e1085-212">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="e1085-213">**Key**：需要工厂的派生 DbContext 的类型的类型对象。</span><span class="sxs-lookup"><span data-stu-id="e1085-213">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="e1085-214">函数函数 IMetadataAnnotationSerializer\> 的 < 函数（）</span><span class="sxs-lookup"><span data-stu-id="e1085-214">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="e1085-215">**引入的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="e1085-215">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="e1085-216">**返回的对象**：一个工厂，用于创建用于序列化强类型自定义批注的序列化程序，以便可以将其序列化并 DESTERILIZED 到 XML 中以便在 Code First 迁移中使用。</span><span class="sxs-lookup"><span data-stu-id="e1085-216">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="e1085-217">**键**：要序列化或反序列化的批注的名称。</span><span class="sxs-lookup"><span data-stu-id="e1085-217">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="e1085-218">函数函数的 Func <\> TransactionHandler</span><span class="sxs-lookup"><span data-stu-id="e1085-218">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="e1085-219">**引入的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="e1085-219">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="e1085-220">**返回的对象**：将用于为事务创建处理程序的工厂，以便在处理提交失败的情况下可以应用特殊处理。</span><span class="sxs-lookup"><span data-stu-id="e1085-220">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="e1085-221">**Key**：包含提供程序固定名称的 ExecutionStrategyKey 对象，还可以选择要对其使用事务处理程序的服务器名称。</span><span class="sxs-lookup"><span data-stu-id="e1085-221">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
