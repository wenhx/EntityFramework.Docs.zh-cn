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
# <a name="dependency-resolution"></a><span data-ttu-id="0661b-103">依赖项解析</span><span class="sxs-lookup"><span data-stu-id="0661b-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="0661b-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="0661b-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="0661b-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="0661b-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="0661b-106">从 EF6 开始，实体框架包含用于获取所需服务实现的通用机制。</span><span class="sxs-lookup"><span data-stu-id="0661b-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="0661b-107">也就是说，当 EF 使用某些接口或基类的实例时，它将要求使用接口或基类的具体实现。</span><span class="sxs-lookup"><span data-stu-id="0661b-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="0661b-108">这是通过使用 IDbDependencyResolver 接口实现的：</span><span class="sxs-lookup"><span data-stu-id="0661b-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="0661b-109">GetService 方法通常由 EF 调用，由 EF 或应用程序提供的 IDbDependencyResolver 的实现进行处理。</span><span class="sxs-lookup"><span data-stu-id="0661b-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="0661b-110">调用时，类型参数是所请求服务的接口或基类类型，并且密钥对象为 null 或提供有关所请求服务的上下文信息的对象。</span><span class="sxs-lookup"><span data-stu-id="0661b-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="0661b-111">除非另有说明，否则返回的任何对象都必须是线程安全的，因为它可用作单一实例。</span><span class="sxs-lookup"><span data-stu-id="0661b-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="0661b-112">在许多情况下，返回的对象是一个工厂，在这种情况下，工厂本身必须是线程安全的，但从工厂返回的对象不需要是线程安全的，因为每次使用都从工厂请求了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="0661b-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="0661b-113">本文不包含有关如何实现 IDbDependencyResolver 的完整详细信息，而是充当服务类型的参考 (也就是说，) 的接口和基类类型，EF 将为这些调用调用 GetService 和密钥对象的语义。</span><span class="sxs-lookup"><span data-stu-id="0661b-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="0661b-114">IDatabaseInitializer<TContext （）\></span><span class="sxs-lookup"><span data-stu-id="0661b-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="0661b-115">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-116">**返回的对象**：给定上下文类型的数据库初始值设定项</span><span class="sxs-lookup"><span data-stu-id="0661b-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="0661b-117">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="0661b-118">函数函数的 Func<MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="0661b-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="0661b-119">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="0661b-120">**返回的对象**：用于创建 SQL 生成器的工厂，该生成器可用于迁移和导致创建数据库的其他操作（如使用数据库初始值设定项创建数据库）。</span><span class="sxs-lookup"><span data-stu-id="0661b-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="0661b-121">**Key**：包含 ADO.NET 提供程序固定名称的字符串，该名称指定要为其生成 SQL 的数据库的类型。</span><span class="sxs-lookup"><span data-stu-id="0661b-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="0661b-122">例如，对于密钥 "SqlClient"，将返回 SQL Server SQL 生成器。</span><span class="sxs-lookup"><span data-stu-id="0661b-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-123">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="0661b-124">Dbproviderservices.createdatabase 的数据的实体。</span><span class="sxs-lookup"><span data-stu-id="0661b-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="0661b-125">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-126">**返回的对象**：用于给定提供程序固定名称的 EF 提供程序</span><span class="sxs-lookup"><span data-stu-id="0661b-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="0661b-127">**Key**：包含 ADO.NET 提供程序固定名称的字符串，用于指定需要提供程序的数据库类型。</span><span class="sxs-lookup"><span data-stu-id="0661b-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="0661b-128">例如，对于密钥 "SqlClient"，将返回 SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="0661b-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-129">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="0661b-130">"IDbConnectionFactory"。</span><span class="sxs-lookup"><span data-stu-id="0661b-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="0661b-131">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-132">**返回的对象**：当 EF 按约定创建数据库连接时将使用的连接工厂。</span><span class="sxs-lookup"><span data-stu-id="0661b-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="0661b-133">也就是说，如果不向 EF 提供连接或连接字符串，并且在 app.config 或 web.config 中找不到连接字符串，则使用此服务按约定创建连接。</span><span class="sxs-lookup"><span data-stu-id="0661b-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="0661b-134">默认情况下，更改连接工厂可以允许 EF 使用不同类型的数据库 (例如，SQL Server Compact Edition) 。</span><span class="sxs-lookup"><span data-stu-id="0661b-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="0661b-135">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-136">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="0661b-137">"IManifestTokenService"。</span><span class="sxs-lookup"><span data-stu-id="0661b-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="0661b-138">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-139">**返回的对象**：一种服务，可从连接生成提供程序清单标记。</span><span class="sxs-lookup"><span data-stu-id="0661b-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="0661b-140">此服务通常以两种方式使用。</span><span class="sxs-lookup"><span data-stu-id="0661b-140">This service is typically used in two ways.</span></span> <span data-ttu-id="0661b-141">首先，可以使用它来避免 Code First 在生成模型时连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="0661b-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="0661b-142">其次，它可以用于强制 Code First 为特定的数据库版本生成模型-例如，即使使用 SQL Server 2008，也可以强制对 SQL Server 2005 的模型进行强制。</span><span class="sxs-lookup"><span data-stu-id="0661b-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="0661b-143">**对象生存期**：单一实例--相同的对象可以多次使用，并且可以同时由不同的线程使用</span><span class="sxs-lookup"><span data-stu-id="0661b-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="0661b-144">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="0661b-145">"IDbProviderFactoryService"。</span><span class="sxs-lookup"><span data-stu-id="0661b-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="0661b-146">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-147">**返回的对象**：可以从给定连接获得提供程序工厂的服务。</span><span class="sxs-lookup"><span data-stu-id="0661b-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="0661b-148">在 .NET 4.5 中，提供程序可以从连接中公开访问。</span><span class="sxs-lookup"><span data-stu-id="0661b-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="0661b-149">在 .NET 4 中，此服务的默认实现使用一些试探法来查找匹配的提供程序。</span><span class="sxs-lookup"><span data-stu-id="0661b-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="0661b-150">如果这些失败，则可以注册此服务的新实现以提供适当的解决方法。</span><span class="sxs-lookup"><span data-stu-id="0661b-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="0661b-151">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="0661b-152">Func<DbContext，IDbModelCacheKey。\></span><span class="sxs-lookup"><span data-stu-id="0661b-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="0661b-153">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-154">**返回的对象**：将为给定上下文生成模型缓存键的工厂。</span><span class="sxs-lookup"><span data-stu-id="0661b-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="0661b-155">默认情况下，EF 按每个提供程序的 DbContext 类型缓存一个模型。</span><span class="sxs-lookup"><span data-stu-id="0661b-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="0661b-156">此服务的不同实现可用于向缓存键添加其他信息，如架构名称。</span><span class="sxs-lookup"><span data-stu-id="0661b-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="0661b-157">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="0661b-158">DbSpatialServices..。</span><span class="sxs-lookup"><span data-stu-id="0661b-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="0661b-159">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-160">**返回的对象**：一个 EF 空间提供程序，它添加对 geography 和 geometry 空间类型的基本 EF 提供程序的支持。</span><span class="sxs-lookup"><span data-stu-id="0661b-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="0661b-161">**键**：以两种方式请求 DbSptialServices。</span><span class="sxs-lookup"><span data-stu-id="0661b-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="0661b-162">首先，使用 DbProviderInfo 对象请求特定于提供程序的空间服务 (该对象包含固定名称和清单标记) 为密钥。</span><span class="sxs-lookup"><span data-stu-id="0661b-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="0661b-163">其次，无关键字就可以要求 DbSpatialServices。</span><span class="sxs-lookup"><span data-stu-id="0661b-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="0661b-164">这用于解析在创建独立的 DbGeography 或 DbGeometry 类型时使用的 "全局空间提供程序"。</span><span class="sxs-lookup"><span data-stu-id="0661b-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-165">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="0661b-166">函数函数的 Func<IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="0661b-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="0661b-167">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-168">**返回的对象**：一个工厂，用于创建一个服务，该服务允许提供程序在针对数据库执行查询和命令时实现重试或其他行为。</span><span class="sxs-lookup"><span data-stu-id="0661b-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="0661b-169">如果未提供实现，则 EF 只会执行命令并传播引发的任何异常。</span><span class="sxs-lookup"><span data-stu-id="0661b-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="0661b-170">对于 SQL Server 此服务用于提供重试策略，这在对基于云的数据库服务器（如 SQL Azure）运行时特别有用。</span><span class="sxs-lookup"><span data-stu-id="0661b-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="0661b-171">**Key**：包含提供程序固定名称的 ExecutionStrategyKey 对象，还可以选择要对其使用执行策略的服务器名称。</span><span class="sxs-lookup"><span data-stu-id="0661b-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-172">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="0661b-173">Func<DbConnection，string，HistoryContext。\></span><span class="sxs-lookup"><span data-stu-id="0661b-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="0661b-174">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-175">**返回的对象**：允许提供程序配置 HISTORYCONTEXT 与 EF 迁移使用的表的映射的工厂 `__MigrationHistory` 。</span><span class="sxs-lookup"><span data-stu-id="0661b-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="0661b-176">HistoryContext 是一个 Code First DbContext，可以使用普通 Fluent API 进行配置，以更改表名称和列映射规范等项。</span><span class="sxs-lookup"><span data-stu-id="0661b-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="0661b-177">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-178">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="0661b-179">DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="0661b-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="0661b-180">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-181">**返回的对象**：要用于给定提供程序固定名称的 ADO.NET 提供程序。</span><span class="sxs-lookup"><span data-stu-id="0661b-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="0661b-182">**Key**：包含 ADO.NET 提供程序固定名称的字符串</span><span class="sxs-lookup"><span data-stu-id="0661b-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-183">此服务通常不会直接更改，因为默认实现使用普通的 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="0661b-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="0661b-184">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="0661b-185">"IProviderInvariantName"。</span><span class="sxs-lookup"><span data-stu-id="0661b-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="0661b-186">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-187">**返回的对象**：用于确定给定类型的 DbProviderFactory 的提供程序固定名称的服务。</span><span class="sxs-lookup"><span data-stu-id="0661b-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="0661b-188">此服务的默认实现使用 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="0661b-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="0661b-189">这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。</span><span class="sxs-lookup"><span data-stu-id="0661b-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="0661b-190">**Key**：需要固定名称的 DbProviderFactory 实例。</span><span class="sxs-lookup"><span data-stu-id="0661b-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="0661b-191">有关 EF6 中与提供程序相关的服务的更多详细信息，请参阅 [EF6 提供程序模型](xref:ef6/fundamentals/providers/provider-model) 部分。</span><span class="sxs-lookup"><span data-stu-id="0661b-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="0661b-192">ViewGeneration. IViewAssemblyCache 的数据。</span><span class="sxs-lookup"><span data-stu-id="0661b-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="0661b-193">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-194">**返回的对象**：包含预先生成的视图的程序集的缓存。</span><span class="sxs-lookup"><span data-stu-id="0661b-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="0661b-195">替换通常用于让 EF 知道哪些程序集包含预生成的视图，而无需执行任何发现。</span><span class="sxs-lookup"><span data-stu-id="0661b-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="0661b-196">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="0661b-197">复数形式... IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="0661b-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="0661b-198">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-199">**返回的对象**：由 EF 用于复数形式和确定所名称的服务。</span><span class="sxs-lookup"><span data-stu-id="0661b-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="0661b-200">默认情况下，使用英语复数形式服务。</span><span class="sxs-lookup"><span data-stu-id="0661b-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="0661b-201">**键**：未使用;将为 null</span><span class="sxs-lookup"><span data-stu-id="0661b-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="0661b-202">IDbInterceptor 的数据的元数据</span><span class="sxs-lookup"><span data-stu-id="0661b-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="0661b-203">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="0661b-204">**返回的对象**：在应用程序启动时应注册的所有侦听器。</span><span class="sxs-lookup"><span data-stu-id="0661b-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="0661b-205">请注意，这些对象是使用 GetServices 调用请求的，所有依赖关系解析程序返回的所有拦截器都将进行注册。</span><span class="sxs-lookup"><span data-stu-id="0661b-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="0661b-206">**键**：未使用;将为 null。</span><span class="sxs-lookup"><span data-stu-id="0661b-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="0661b-207">Func<DbContext，Action<string，DatabaseLogFormatter 的字符串，该字符串 \> 的数据\></span><span class="sxs-lookup"><span data-stu-id="0661b-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="0661b-208">**引入的版本**： ef 6.0。0</span><span class="sxs-lookup"><span data-stu-id="0661b-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="0661b-209">**返回的对象**：一个工厂，用于创建将在上下文时使用的数据库日志格式化程序。在给定的上下文中设置了数据库. 日志属性。</span><span class="sxs-lookup"><span data-stu-id="0661b-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="0661b-210">**键**：未使用;将为 null。</span><span class="sxs-lookup"><span data-stu-id="0661b-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="0661b-211">函数 Func<DbContext\></span><span class="sxs-lookup"><span data-stu-id="0661b-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="0661b-212">**引入的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="0661b-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="0661b-213">**返回的对象**：当上下文不具有可访问的无参数构造函数时，将用于创建用于迁移的上下文实例的工厂。</span><span class="sxs-lookup"><span data-stu-id="0661b-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="0661b-214">**Key**：需要工厂的派生 DbContext 的类型的类型对象。</span><span class="sxs-lookup"><span data-stu-id="0661b-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="0661b-215">函数 IMetadataAnnotationSerializer 的函数函数的<函数。\></span><span class="sxs-lookup"><span data-stu-id="0661b-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="0661b-216">**引入的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="0661b-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="0661b-217">**返回的对象**：一个工厂，用于创建用于序列化强类型自定义批注的序列化程序，以便可以将其序列化并 DESTERILIZED 到 XML 中以便在 Code First 迁移中使用。</span><span class="sxs-lookup"><span data-stu-id="0661b-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="0661b-218">**键**：要序列化或反序列化的批注的名称。</span><span class="sxs-lookup"><span data-stu-id="0661b-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="0661b-219">函数函数的 Func<TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="0661b-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="0661b-220">**引入的版本**： ef 6.1。0</span><span class="sxs-lookup"><span data-stu-id="0661b-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="0661b-221">**返回的对象**：将用于为事务创建处理程序的工厂，以便在处理提交失败的情况下可以应用特殊处理。</span><span class="sxs-lookup"><span data-stu-id="0661b-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="0661b-222">**Key**：包含提供程序固定名称的 ExecutionStrategyKey 对象，还可以选择要对其使用事务处理程序的服务器名称。</span><span class="sxs-lookup"><span data-stu-id="0661b-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
