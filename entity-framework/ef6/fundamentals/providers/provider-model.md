---
title: 实体框架6提供程序模型-EF6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
ms.openlocfilehash: 8bda3f51e8934f2add862c30e60f1185f068c515
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181612"
---
# <a name="the-entity-framework-6-provider-model"></a><span data-ttu-id="d417b-102">实体框架6提供程序模型</span><span class="sxs-lookup"><span data-stu-id="d417b-102">The Entity Framework 6 provider model</span></span>

<span data-ttu-id="d417b-103">实体框架提供程序模型允许实体框架与不同类型的数据库服务器一起使用。</span><span class="sxs-lookup"><span data-stu-id="d417b-103">The Entity Framework provider model allows Entity Framework to be used with different types of database server.</span></span> <span data-ttu-id="d417b-104">例如，可以插入一个提供程序以允许对 Microsoft SQL Server 使用 EF，而另一个提供程序可以插入到中，以允许在 Microsoft SQL Server Compact 版本中使用 EF。</span><span class="sxs-lookup"><span data-stu-id="d417b-104">For example, one provider can be plugged in to allow EF to be used against Microsoft SQL Server, while another provider can be plugged into to allow EF to be used against Microsoft SQL Server Compact Edition.</span></span> <span data-ttu-id="d417b-105">[实体框架提供程序](~/ef6/fundamentals/providers/index.md)"页上提供了可识别的 EF6 提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-105">The providers for EF6 that we are aware of can be found on the [Entity Framework providers](~/ef6/fundamentals/providers/index.md) page.</span></span>

<span data-ttu-id="d417b-106">EF 与提供程序交互的方式需要进行某些更改，以允许在开源许可证下释放 EF。</span><span class="sxs-lookup"><span data-stu-id="d417b-106">Certain changes were required to the way EF interacts with providers to allow EF to be released under an open source license.</span></span> <span data-ttu-id="d417b-107">这些更改要求针对 EF6 程序集重新生成 EF 提供程序，同时提供用于注册提供程序的新机制。</span><span class="sxs-lookup"><span data-stu-id="d417b-107">These changes require rebuilding of EF providers against the EF6 assemblies together with new mechanisms for registration of the provider.</span></span>

## <a name="rebuilding"></a><span data-ttu-id="d417b-108">重新生成</span><span class="sxs-lookup"><span data-stu-id="d417b-108">Rebuilding</span></span>

<span data-ttu-id="d417b-109">在 EF6 中，先前 .NET Framework 的核心代码现在作为带外（OOB）程序集发货。</span><span class="sxs-lookup"><span data-stu-id="d417b-109">With EF6 the core code that was previously part of the .NET Framework is now being shipped as out-of-band (OOB) assemblies.</span></span> <span data-ttu-id="d417b-110">有关如何针对 EF6 生成应用程序的详细信息，请参阅[更新应用程序的 EF6](~/ef6/what-is-new/upgrading-to-ef6.md)页。</span><span class="sxs-lookup"><span data-stu-id="d417b-110">Details on how to build applications against EF6 can be found on the [Updating applications for EF6](~/ef6/what-is-new/upgrading-to-ef6.md) page.</span></span> <span data-ttu-id="d417b-111">还需要使用这些说明重新生成提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-111">Providers will also need to be rebuilt using these instructions.</span></span>

## <a name="provider-types-overview"></a><span data-ttu-id="d417b-112">提供程序类型概述</span><span class="sxs-lookup"><span data-stu-id="d417b-112">Provider types overview</span></span>

<span data-ttu-id="d417b-113">EF 提供程序实际上是由 CLR 类型定义的特定于提供程序的服务的集合，这些服务由这些服务扩展（用于基类）或实现（用于接口）。</span><span class="sxs-lookup"><span data-stu-id="d417b-113">An EF provider is really a collection of provider-specific services defined by CLR types that these services extend from (for a base class) or implement (for an interface).</span></span> <span data-ttu-id="d417b-114">其中的两个服务都是基本的功能，而 EF 是必需的。</span><span class="sxs-lookup"><span data-stu-id="d417b-114">Two of these services are fundamental and necessary for EF to function at all.</span></span> <span data-ttu-id="d417b-115">其他选项是可选的，仅在需要特定功能时才需要实现，并且/或者这些服务的默认实现对于目标特定数据库服务器不起作用。</span><span class="sxs-lookup"><span data-stu-id="d417b-115">Others are optional and only need to be implemented if specific functionality is required and/or the default implementations of these services does not work for the specific database server being targeted.</span></span>

## <a name="fundamental-provider-types"></a><span data-ttu-id="d417b-116">基本提供程序类型</span><span class="sxs-lookup"><span data-stu-id="d417b-116">Fundamental provider types</span></span>

### <a name="dbproviderfactory"></a><span data-ttu-id="d417b-117">DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="d417b-117">DbProviderFactory</span></span>

<span data-ttu-id="d417b-118">EF 依赖于从[DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx)派生的类型，以执行所有低级别的数据库访问。</span><span class="sxs-lookup"><span data-stu-id="d417b-118">EF depends on having a type derived from [System.Data.Common.DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) for performing all low-level database access.</span></span> <span data-ttu-id="d417b-119">DbProviderFactory 实际上不是 EF 的一部分，而是 .NET Framework 中的一个类，它为 ADO.NET 提供程序的入口点提供服务，这些提供程序可由 EF、其他 O/RMs 或应用程序直接使用以获取连接实例、命令、参数和提供程序不可知的其他 ADO.NET 抽象方法。</span><span class="sxs-lookup"><span data-stu-id="d417b-119">DbProviderFactory is not actually part of EF but is instead a class in the .NET Framework that serves an entry point for ADO.NET providers that can be used by EF, other O/RMs or directly by an application to obtain instances of connections, commands, parameters and other ADO.NET abstractions in a provider agnostic way.</span></span> <span data-ttu-id="d417b-120">有关 DbProviderFactory 的详细信息，请参阅[ADO.NET 的 MSDN 文档](https://msdn.microsoft.com/library/a6cd7c08.aspx)。</span><span class="sxs-lookup"><span data-stu-id="d417b-120">More information about DbProviderFactory can be found in the [MSDN documentation for ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx).</span></span>

### <a name="dbproviderservices"></a><span data-ttu-id="d417b-121">DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="d417b-121">DbProviderServices</span></span>

<span data-ttu-id="d417b-122">EF 依赖于从 Dbproviderservices.createdatabase 派生的类型，以便在 ADO.NET 提供程序已提供的功能之上提供 EF 所需的其他功能。</span><span class="sxs-lookup"><span data-stu-id="d417b-122">EF depends on having a type derived from DbProviderServices for providing additional functionality needed by EF on top of the functionality already provided by the ADO.NET provider.</span></span> <span data-ttu-id="d417b-123">在 EF 的较早版本中，Dbproviderservices.createdatabase 类是 .NET Framework 的一部分，并且在 System.web 命名空间中找到。</span><span class="sxs-lookup"><span data-stu-id="d417b-123">In older versions of EF the DbProviderServices class was part of the .NET Framework and was found in the System.Data.Common namespace.</span></span> <span data-ttu-id="d417b-124">从 EF6 开始，此类现在是 EntityFramework 的一部分，并且位于命名空间中。</span><span class="sxs-lookup"><span data-stu-id="d417b-124">Starting with EF6 this class is now part of EntityFramework.dll and is in the System.Data.Entity.Core.Common namespace.</span></span>

<span data-ttu-id="d417b-125">可在[MSDN](https://msdn.microsoft.com/library/ee789835.aspx)上找到有关 dbproviderservices.createdatabase 实现的基本功能的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="d417b-125">More details about the fundamental functionality of a DbProviderServices implementation can be found on [MSDN](https://msdn.microsoft.com/library/ee789835.aspx).</span></span> <span data-ttu-id="d417b-126">但请注意，在撰写本文时，尽管大部分概念仍然有效，但不会对 EF6 进行更新。</span><span class="sxs-lookup"><span data-stu-id="d417b-126">However, note that as of the time of writing this information is not updated for EF6 although most of the concepts are still valid.</span></span> <span data-ttu-id="d417b-127">Dbproviderservices.createdatabase 的 SQL Server 和 SQL Server Compact 实现也签入到[开源基本代码](https://github.com/aspnet/EntityFramework6/)，并可用作其他实现的有用引用。</span><span class="sxs-lookup"><span data-stu-id="d417b-127">The SQL Server and SQL Server Compact implementations of DbProviderServices are also checked into to the [open-source codebase](https://github.com/aspnet/EntityFramework6/) and can serve as useful references for other implementations.</span></span>

<span data-ttu-id="d417b-128">在早期版本的 EF 中，Dbproviderservices.createdatabase 实现直接从 ADO.NET 提供程序获取。</span><span class="sxs-lookup"><span data-stu-id="d417b-128">In older versions of EF the DbProviderServices implementation to use was obtained directly from an ADO.NET provider.</span></span> <span data-ttu-id="d417b-129">这是通过将 DbProviderFactory 强制转换为 IServiceProvider 并调用 GetService 方法来完成的。</span><span class="sxs-lookup"><span data-stu-id="d417b-129">This was done by casting DbProviderFactory to IServiceProvider and calling the GetService method.</span></span> <span data-ttu-id="d417b-130">这会将 EF 提供程序紧密耦合到 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-130">This tightly coupled the EF provider to the DbProviderFactory.</span></span> <span data-ttu-id="d417b-131">此耦合阻止的 EF 被移出 .NET Framework，因此对于 EF6，此紧密耦合已被移除，Dbproviderservices.createdatabase 的实现现在直接在应用程序的配置文件或基于代码中注册有关详细信息，请参阅下面的_注册 dbproviderservices.createdatabase_部分。</span><span class="sxs-lookup"><span data-stu-id="d417b-131">This coupling blocked EF from being moved out of the .NET Framework and therefore for EF6 this tight coupling has been removed and an implementation of DbProviderServices is now registered directly in the application’s configuration file or in code-based configuration as described in more detail the _Registering DbProviderServices_ section below.</span></span>

## <a name="additional-services"></a><span data-ttu-id="d417b-132">其他服务</span><span class="sxs-lookup"><span data-stu-id="d417b-132">Additional services</span></span>

<span data-ttu-id="d417b-133">除了上面所述的基本服务外，EF 还使用许多其他服务，它们始终或有时是特定于提供程序的服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-133">In addition to the fundamental services described above there are also many other services used by EF which are either always or sometimes provider-specific.</span></span> <span data-ttu-id="d417b-134">这些服务的默认特定于提供程序的实现可由 Dbproviderservices.createdatabase 实现提供。</span><span class="sxs-lookup"><span data-stu-id="d417b-134">Default provider-specific implementations of these services can be supplied by a DbProviderServices implementation.</span></span> <span data-ttu-id="d417b-135">应用程序还可以覆盖这些服务的实现，或在 Dbproviderservices.createdatabase 类型不提供默认值时提供实现。</span><span class="sxs-lookup"><span data-stu-id="d417b-135">Applications can also override the implementations of these services, or provide implementations when a DbProviderServices type does not provide a default.</span></span> <span data-ttu-id="d417b-136">下面更详细地介绍了_这一情况_。</span><span class="sxs-lookup"><span data-stu-id="d417b-136">This is described in more detail in the _Resolving additional services_ section below.</span></span>

<span data-ttu-id="d417b-137">下面列出了提供程序可能对提供程序感兴趣的其他服务类型。</span><span class="sxs-lookup"><span data-stu-id="d417b-137">The additional service types that a provider may be of interest to a provider are listed below.</span></span> <span data-ttu-id="d417b-138">有关每种服务类型的更多详细信息，请参阅 API 文档。</span><span class="sxs-lookup"><span data-stu-id="d417b-138">More details about each of these service types can be found in the API documentation.</span></span>

### <a name="idbexecutionstrategy"></a><span data-ttu-id="d417b-139">IDbExecutionStrategy</span><span class="sxs-lookup"><span data-stu-id="d417b-139">IDbExecutionStrategy</span></span>

<span data-ttu-id="d417b-140">这是一种可选的服务，当对数据库执行查询和命令时，此服务允许提供程序实现重试或其他行为。</span><span class="sxs-lookup"><span data-stu-id="d417b-140">This is an optional service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="d417b-141">如果未提供实现，则 EF 只会执行命令并传播引发的任何异常。</span><span class="sxs-lookup"><span data-stu-id="d417b-141">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="d417b-142">对于 SQL Server 此服务用于提供重试策略，这在对基于云的数据库服务器（如 SQL Azure）运行时特别有用。</span><span class="sxs-lookup"><span data-stu-id="d417b-142">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>

### <a name="idbconnectionfactory"></a><span data-ttu-id="d417b-143">IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="d417b-143">IDbConnectionFactory</span></span>

<span data-ttu-id="d417b-144">这是一种可选服务，允许提供程序在只给定数据库名称的情况下按约定创建 DbConnection 对象。</span><span class="sxs-lookup"><span data-stu-id="d417b-144">This is an optional service that allows a provider to create DbConnection objects by convention when given only a database name.</span></span> <span data-ttu-id="d417b-145">请注意，虽然此服务可以通过 Dbproviderservices.createdatabase 实现进行解析，但它已存在于 EF 4.1，并且还可以在配置文件或代码中显式设置。</span><span class="sxs-lookup"><span data-stu-id="d417b-145">Note that while this service can be resolved by a DbProviderServices implementation it has been present since EF 4.1 and can also be explicitly set in either the config file or in code.</span></span> <span data-ttu-id="d417b-146">如果已将此服务注册为默认提供程序（请参阅下面_的默认提供程序_），并且未在其他位置设置默认的连接工厂，则提供程序将只有机会解析此服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-146">The provider will only get a chance to resolve this service if it registered as the default provider (see _The default provider_ below) and if a default connection factory has not been set elsewhere.</span></span>

### <a name="dbspatialservices"></a><span data-ttu-id="d417b-147">DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="d417b-147">DbSpatialServices</span></span>

<span data-ttu-id="d417b-148">这是一种可选服务，允许提供商添加对地理空间类型和几何空间类型的支持。</span><span class="sxs-lookup"><span data-stu-id="d417b-148">This is an optional services that allows a provider to add support for geography and geometry spatial types.</span></span> <span data-ttu-id="d417b-149">必须提供此服务的实现，应用程序才能将 EF 与空间类型一起使用。</span><span class="sxs-lookup"><span data-stu-id="d417b-149">An implementation of this service must be supplied in order for an application to use EF with spatial types.</span></span> <span data-ttu-id="d417b-150">将以两种方式请求 DbSptialServices。</span><span class="sxs-lookup"><span data-stu-id="d417b-150">DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="d417b-151">首先，使用 DbProviderInfo 对象（其中包含固定名称和清单标记）作为密钥请求提供程序特定的空间服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-151">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as key.</span></span> <span data-ttu-id="d417b-152">其次，无关键字就可以要求 DbSpatialServices。</span><span class="sxs-lookup"><span data-stu-id="d417b-152">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="d417b-153">这用于解析在创建独立的 DbGeography 或 DbGeometry 类型时使用的 "全局空间提供程序"。</span><span class="sxs-lookup"><span data-stu-id="d417b-153">This is used to resolve the “global spatial provider” that is used when creating stand-alone DbGeography or DbGeometry types.</span></span>

### <a name="migrationsqlgenerator"></a><span data-ttu-id="d417b-154">MigrationSqlGenerator</span><span class="sxs-lookup"><span data-stu-id="d417b-154">MigrationSqlGenerator</span></span>

<span data-ttu-id="d417b-155">这是一项可选的服务，它允许将 EF 迁移用于通过 Code First 创建和修改数据库架构时使用的 SQL 生成。</span><span class="sxs-lookup"><span data-stu-id="d417b-155">This is an optional service that allows EF Migrations to be used for the generation of SQL used in creating and modifying database schemas by Code First.</span></span> <span data-ttu-id="d417b-156">需要实现才能支持迁移。</span><span class="sxs-lookup"><span data-stu-id="d417b-156">An implementation is required in order to support Migrations.</span></span> <span data-ttu-id="d417b-157">如果提供了实现，则在使用数据库初始值设定项或 Database. Create 方法创建数据库时，也将使用它。</span><span class="sxs-lookup"><span data-stu-id="d417b-157">If an implementation is provided then it will also be used when databases are created using database initializers or the Database.Create method.</span></span>

### <a name="funcdbconnection-string-historycontextfactory"></a><span data-ttu-id="d417b-158">Func < DbConnection，string，HistoryContextFactory ></span><span class="sxs-lookup"><span data-stu-id="d417b-158">Func<DbConnection, string, HistoryContextFactory></span></span>

<span data-ttu-id="d417b-159">这是一项可选的服务，可让提供程序配置 HistoryContext 与 EF 迁移使用的 @no__t 0 表的映射。</span><span class="sxs-lookup"><span data-stu-id="d417b-159">This is an optional service that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="d417b-160">HistoryContext 是一个 Code First DbContext，可以使用普通 Fluent API 进行配置，以更改表名称和列映射规范等项。</span><span class="sxs-lookup"><span data-stu-id="d417b-160">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span> <span data-ttu-id="d417b-161">如果该提供程序支持所有默认表和列映射，则由 EF 为所有提供程序返回的此服务的默认实现可能适用于给定的数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="d417b-161">The default implementation of this service returned by EF for all providers may work for a given database server if all the default table and column mappings are supported by that provider.</span></span> <span data-ttu-id="d417b-162">在这种情况下，提供程序不需要提供此服务的实现。</span><span class="sxs-lookup"><span data-stu-id="d417b-162">In such a case the provider does not need to supply an implementation of this service.</span></span>

### <a name="idbproviderfactoryresolver"></a><span data-ttu-id="d417b-163">IDbProviderFactoryResolver</span><span class="sxs-lookup"><span data-stu-id="d417b-163">IDbProviderFactoryResolver</span></span>

<span data-ttu-id="d417b-164">这是一种可选的服务，用于从给定的 DbConnection 对象中获取正确的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-164">This is an optional service for obtaining the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="d417b-165">此服务的默认实现由 EF 为所有提供程序返回，旨在适用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-165">The default implementation of this service returned by EF for all providers is intended to work for all providers.</span></span> <span data-ttu-id="d417b-166">但是，在 .NET 4 上运行时，如果 DbProviderFactory 的 DbConnections，则无法从一个公共访问。</span><span class="sxs-lookup"><span data-stu-id="d417b-166">However, when running on .NET 4, the DbProviderFactory is not publicly accessible from one if its DbConnections.</span></span> <span data-ttu-id="d417b-167">因此，EF 使用一些试探法搜索已注册的提供程序以找到匹配项。</span><span class="sxs-lookup"><span data-stu-id="d417b-167">Therefore, EF uses some heuristics to search the registered providers to find a match.</span></span> <span data-ttu-id="d417b-168">某些提供程序的这种试探方法可能会失败，并且在这种情况下，提供程序应提供新的实现。</span><span class="sxs-lookup"><span data-stu-id="d417b-168">It is possible that for some providers these heuristics will fail and in such situations the provider should supply a new implementation.</span></span>

## <a name="registering-dbproviderservices"></a><span data-ttu-id="d417b-169">注册 Dbproviderservices.createdatabase</span><span class="sxs-lookup"><span data-stu-id="d417b-169">Registering DbProviderServices</span></span>

<span data-ttu-id="d417b-170">要使用的 Dbproviderservices.createdatabase 实现可在应用程序的配置文件（app.config 或 web.config）或使用基于代码的配置中注册。</span><span class="sxs-lookup"><span data-stu-id="d417b-170">The DbProviderServices implementation to use can be registered either in the application’s configuration file (app.config or web.config) or using code-based configuration.</span></span> <span data-ttu-id="d417b-171">在任一情况下，注册都使用提供程序的 "固定名称" 作为密钥。</span><span class="sxs-lookup"><span data-stu-id="d417b-171">In either case the registration uses the provider’s “invariant name” as a key.</span></span> <span data-ttu-id="d417b-172">这允许在单个应用程序中注册和使用多个提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-172">This allows multiple providers to be registered and used in a single application.</span></span> <span data-ttu-id="d417b-173">用于 EF 注册的固定名称与用于 ADO.NET 提供程序注册和连接字符串的固定名称相同。</span><span class="sxs-lookup"><span data-stu-id="d417b-173">The invariant name used for EF registrations is the same as the invariant name used for ADO.NET provider registration and connection strings.</span></span> <span data-ttu-id="d417b-174">例如，对于 SQL Server 使用固定名称 "SqlClient"。</span><span class="sxs-lookup"><span data-stu-id="d417b-174">For example, for SQL Server the invariant name “System.Data.SqlClient” is used.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="d417b-175">配置文件注册</span><span class="sxs-lookup"><span data-stu-id="d417b-175">Config file registration</span></span>

<span data-ttu-id="d417b-176">要使用的 Dbproviderservices.createdatabase 类型在应用程序配置文件的 entityFramework 节的提供程序列表中注册为提供程序元素。</span><span class="sxs-lookup"><span data-stu-id="d417b-176">The DbProviderServices type to use is registered as a provider element in the providers list of the entityFramework section of the application’s config file.</span></span> <span data-ttu-id="d417b-177">例如：</span><span class="sxs-lookup"><span data-stu-id="d417b-177">For example:</span></span>

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

<span data-ttu-id="d417b-178">_类型_字符串必须是要使用的 dbproviderservices.createdatabase 实现的程序集限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="d417b-178">The _type_ string must be the assembly-qualified type name of the DbProviderServices implementation to use.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="d417b-179">基于代码的注册</span><span class="sxs-lookup"><span data-stu-id="d417b-179">Code-based registration</span></span>

<span data-ttu-id="d417b-180">从 EF6 提供程序开始，还可以使用代码注册。</span><span class="sxs-lookup"><span data-stu-id="d417b-180">Starting with EF6 providers can also be registered using code.</span></span> <span data-ttu-id="d417b-181">这允许使用 EF 提供程序，而无需对应用程序的配置文件进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="d417b-181">This allows an EF provider to be used without any change to the application’s configuration file.</span></span> <span data-ttu-id="d417b-182">若要使用基于代码的配置，应用程序应按照[基于代码的配置文档](https://msdn.com/data/jj680699)中所述创建 DbConfiguration 类。</span><span class="sxs-lookup"><span data-stu-id="d417b-182">To use code-based configuration an application should create a DbConfiguration class as described in the [code-based configuration documentation](https://msdn.com/data/jj680699).</span></span> <span data-ttu-id="d417b-183">然后，DbConfiguration 类的构造函数应调用 SetProviderServices 来注册 EF 提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-183">The constructor of the DbConfiguration class should then call SetProviderServices to register the EF provider.</span></span> <span data-ttu-id="d417b-184">例如：</span><span class="sxs-lookup"><span data-stu-id="d417b-184">For example:</span></span>

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a><span data-ttu-id="d417b-185">解析其他服务</span><span class="sxs-lookup"><span data-stu-id="d417b-185">Resolving additional services</span></span>

<span data-ttu-id="d417b-186">如前所述，在 "_提供程序类型概述_" 部分中，还可以使用 dbproviderservices.createdatabase 类来解析其他服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-186">As mentioned above in the _Provider types overview_ section, a DbProviderServices class can also be used to resolve additional services.</span></span> <span data-ttu-id="d417b-187">这是可能的，因为 Dbproviderservices.createdatabase 实现了 IDbDependencyResolver，并且每个注册的 Dbproviderservices.createdatabase 类型都添加为 "默认冲突解决程序"。</span><span class="sxs-lookup"><span data-stu-id="d417b-187">This is possible because DbProviderServices implements IDbDependencyResolver and each registered DbProviderServices type is added as a “default resolver”.</span></span> <span data-ttu-id="d417b-188">[依赖项解析](~/ef6/fundamentals/configuring/dependency-resolution.md)中更详细地介绍了 IDbDpendencyResolver 机制。</span><span class="sxs-lookup"><span data-stu-id="d417b-188">The IDbDpendencyResolver mechanism is described in more detail in [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md).</span></span> <span data-ttu-id="d417b-189">但是，不需要了解此规范中的所有概念来解析提供程序中的其他服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-189">However, it is not necessary to understand all the concepts in this specification to resolve additional services in a provider.</span></span>

<span data-ttu-id="d417b-190">提供程序解析附加服务的最常见方法是为 Dbproviderservices.createdatabase 类的构造函数中的每个服务调用 Dbproviderservices.createdatabase。</span><span class="sxs-lookup"><span data-stu-id="d417b-190">The most common way for a provider to resolve additional services is to call DbProviderServices.AddDependencyResolver for each service in the constructor of the DbProviderServices class.</span></span> <span data-ttu-id="d417b-191">例如，SqlProviderServices （用于 SQL Server 的 EF 提供程序）具有类似于以下内容的代码：</span><span class="sxs-lookup"><span data-stu-id="d417b-191">For example, SqlProviderServices (the EF provider for SQL Server) has code similar to this for initialization:</span></span>

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

<span data-ttu-id="d417b-192">此构造函数使用以下帮助器类：</span><span class="sxs-lookup"><span data-stu-id="d417b-192">This constructor uses the following helper classes:</span></span>

*   <span data-ttu-id="d417b-193">SingletonDependencyResolver：提供一种解析单独服务的简单方法，即每次调用 GetService 时都为其返回同一个实例的服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-193">SingletonDependencyResolver: provides a simple way to resolve Singleton services—that is, services for which the same instance is returned each time that GetService is called.</span></span> <span data-ttu-id="d417b-194">暂时性服务通常注册为一个单一实例，将用于按需创建暂时性实例。</span><span class="sxs-lookup"><span data-stu-id="d417b-194">Transient services are often registered as a singleton factory that will be used to create transient instances on demand.</span></span>
*   <span data-ttu-id="d417b-195">ExecutionStrategyResolver：特定于返回 IExecutionStrategy 实现的解析程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-195">ExecutionStrategyResolver: a resolver specific to returning IExecutionStrategy implementations.</span></span>

<span data-ttu-id="d417b-196">还可以重写 Dbproviderservices.createdatabase 并直接解析附加服务，而不是使用 Dbproviderservices.createdatabase。 AddDependencyResolver。</span><span class="sxs-lookup"><span data-stu-id="d417b-196">Instead of using DbProviderServices.AddDependencyResolver it is also possible to override DbProviderServices.GetService and resolve additional services directly.</span></span> <span data-ttu-id="d417b-197">当 EF 需要由特定类型定义的服务时，将调用此方法，在某些情况下，将调用此方法作为给定的键。</span><span class="sxs-lookup"><span data-stu-id="d417b-197">This method will be called when EF needs a service defined by a certain type and, in some cases, for a given key.</span></span> <span data-ttu-id="d417b-198">如果服务可以返回服务，则该方法应返回它，或者返回 null 以选择退出该服务，而另一个类可以解决它。</span><span class="sxs-lookup"><span data-stu-id="d417b-198">The method should return the service if it can, or return null to opt-out of returning the service and instead allow another class to resolve it.</span></span> <span data-ttu-id="d417b-199">例如，若要解析默认的连接工厂，GetService 中的代码可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="d417b-199">For example, to resolve the default connection factory the code in GetService might look something like this:</span></span>

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

### <a name="registration-order"></a><span data-ttu-id="d417b-200">注册顺序</span><span class="sxs-lookup"><span data-stu-id="d417b-200">Registration order</span></span>

<span data-ttu-id="d417b-201">在应用程序的配置文件中注册多个 Dbproviderservices.createdatabase 实现时，它们将按照它们列出的顺序添加为辅助解析程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-201">When multiple DbProviderServices implementations are registered in an application’s config file they will be added as secondary resolvers in the order that they are listed.</span></span> <span data-ttu-id="d417b-202">由于解析程序始终添加到辅助解析程序链的顶部，这意味着，列表末尾的提供程序将有机会解析其他依赖项。</span><span class="sxs-lookup"><span data-stu-id="d417b-202">Since resolvers are always added to the top of the secondary resolver chain this means that the provider at the end of the list will get a chance to resolve dependencies before the others.</span></span> <span data-ttu-id="d417b-203">（这在最初看起来可能比较直观，但如果你想象你将每个提供程序移出列表，并将其堆积在现有的提供商之上，这会很有帮助。）</span><span class="sxs-lookup"><span data-stu-id="d417b-203">(This can seem a little counter-intuitive at first, but it makes sense if you imagine taking each provider out of the list and stacking it on top of the existing providers.)</span></span>

<span data-ttu-id="d417b-204">此顺序通常并不重要，因为大多数提供程序服务都是特定于提供程序的，并使用提供程序固定名称进行键控。</span><span class="sxs-lookup"><span data-stu-id="d417b-204">This ordering usually doesn’t matter because most provider services are provider-specific and keyed by provider invariant name.</span></span> <span data-ttu-id="d417b-205">但是，对于不是由提供程序固定名称或其他特定于提供程序的密钥进行键控的服务，将根据此顺序解析服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-205">However, for services that are not keyed by provider invariant name or some other provider-specific key the service will be resolved based on this ordering.</span></span> <span data-ttu-id="d417b-206">例如，如果未在其他任何位置以不同方式显式设置，则默认连接工厂将来自链中最顶层的提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-206">For example, if it is not explicitly set differently somewhere else, then the default connection factory will come from the topmost provider in the chain.</span></span>

## <a name="additional-config-file-registrations"></a><span data-ttu-id="d417b-207">其他配置文件注册</span><span class="sxs-lookup"><span data-stu-id="d417b-207">Additional config file registrations</span></span>

<span data-ttu-id="d417b-208">可以直接在应用程序的配置文件中直接注册上述部分所述的其他提供程序服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-208">It is possible to explicitly register some of the additional provider services described above directly in an application’s config file.</span></span> <span data-ttu-id="d417b-209">完成此操作后，将使用配置文件中的注册，而不是 Dbproviderservices.createdatabase 实现的 GetService 方法返回的任何内容。</span><span class="sxs-lookup"><span data-stu-id="d417b-209">When this is done the registration in the config file will be used instead of anything returned by the GetService method of the DbProviderServices implementation.</span></span>

### <a name="registering-the-default-connection-factory"></a><span data-ttu-id="d417b-210">注册默认的连接工厂</span><span class="sxs-lookup"><span data-stu-id="d417b-210">Registering the default connection factory</span></span>

<span data-ttu-id="d417b-211">从 EF5 开始，EntityFramework NuGet 包自动在配置文件中注册 SQL Express 连接工厂或 LocalDb 连接工厂。</span><span class="sxs-lookup"><span data-stu-id="d417b-211">Starting with EF5 the EntityFramework NuGet package automatically registered either the SQL Express connection factory or the LocalDb connection factory in the config file.</span></span>

<span data-ttu-id="d417b-212">例如：</span><span class="sxs-lookup"><span data-stu-id="d417b-212">For example:</span></span>

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

<span data-ttu-id="d417b-213">_类型_为默认连接工厂的程序集限定类型名称，该名称必须实现 IDbConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-213">The _type_ is the assembly-qualified type name for the default connection factory, which must implement IDbConnectionFactory.</span></span>

<span data-ttu-id="d417b-214">建议提供程序 NuGet 包在安装时以这种方式设置默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="d417b-214">It is recommended that a provider NuGet package set the default connection factory in this way when installed.</span></span> <span data-ttu-id="d417b-215">请参阅下面_的提供程序的 NuGet 包_。</span><span class="sxs-lookup"><span data-stu-id="d417b-215">See _NuGet Packages for providers_ below.</span></span>

## <a name="additional-ef6-provider-changes"></a><span data-ttu-id="d417b-216">其他 EF6 提供程序更改</span><span class="sxs-lookup"><span data-stu-id="d417b-216">Additional EF6 provider changes</span></span>

### <a name="spatial-provider-changes"></a><span data-ttu-id="d417b-217">空间提供程序更改</span><span class="sxs-lookup"><span data-stu-id="d417b-217">Spatial provider changes</span></span>

<span data-ttu-id="d417b-218">支持空间类型的提供程序现在必须在派生自 DbSpatialDataReader 的类上实现一些附加方法：</span><span class="sxs-lookup"><span data-stu-id="d417b-218">Providers that support spatial types must now implement some additional methods on classes deriving from DbSpatialDataReader:</span></span>

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

<span data-ttu-id="d417b-219">此外，还提供了一些新的异步版本，建议将它们作为默认的实现委托给同步方法，并因此不会以异步方式执行：</span><span class="sxs-lookup"><span data-stu-id="d417b-219">There are also new asynchronous versions of existing methods that are recommended to be overridden as the default implementations delegate to the synchronous methods and therefore do not execute asynchronously:</span></span>

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a><span data-ttu-id="d417b-220">对可枚举的本机支持。包含</span><span class="sxs-lookup"><span data-stu-id="d417b-220">Native support for Enumerable.Contains</span></span>

<span data-ttu-id="d417b-221">EF6 引入了一个新的表达式类型 DbInExpression，此类型已添加到解决有关使用可枚举的的性能问题。包含在 LINQ 查询中。</span><span class="sxs-lookup"><span data-stu-id="d417b-221">EF6 introduces a new expression type, DbInExpression, which was added to address performance issues around use of Enumerable.Contains in LINQ queries.</span></span> <span data-ttu-id="d417b-222">DbProviderManifest 类具有新的虚方法 SupportsInExpression，该方法由 EF 调用来确定提供程序是否处理新的表达式类型。</span><span class="sxs-lookup"><span data-stu-id="d417b-222">The DbProviderManifest class has a new virtual method, SupportsInExpression, which is called by EF to determine if a provider handles the new expression type.</span></span> <span data-ttu-id="d417b-223">为了与现有提供程序实现兼容，此方法返回 false。</span><span class="sxs-lookup"><span data-stu-id="d417b-223">For compatibility with existing provider implementations the method returns false.</span></span> <span data-ttu-id="d417b-224">为了从此改进中获益，EF6 提供程序可添加代码来处理 DbInExpression，并覆盖 SupportsInExpression 以返回 true。</span><span class="sxs-lookup"><span data-stu-id="d417b-224">To benefit from this improvement, an EF6 provider can add code to handle DbInExpression and override SupportsInExpression to return true.</span></span> <span data-ttu-id="d417b-225">可以通过调用 DbExpressionBuilder.In 方法创建 DbInExpression 的实例。</span><span class="sxs-lookup"><span data-stu-id="d417b-225">An instance of DbInExpression can be created by calling the DbExpressionBuilder.In method.</span></span> <span data-ttu-id="d417b-226">DbInExpression 实例由 DbExpression 组成，通常表示表列，以及用于测试匹配项的 DbConstantExpression 列表。</span><span class="sxs-lookup"><span data-stu-id="d417b-226">A DbInExpression instance is composed of a DbExpression, usually representing a table column, and a list of DbConstantExpression to test for a match.</span></span>

## <a name="nuget-packages-for-providers"></a><span data-ttu-id="d417b-227">提供程序的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="d417b-227">NuGet packages for providers</span></span>

<span data-ttu-id="d417b-228">使 EF6 提供程序可用的一种方法是将其发布为 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d417b-228">One way to make an EF6 provider available is to release it as a NuGet package.</span></span> <span data-ttu-id="d417b-229">使用 NuGet 包具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="d417b-229">Using a NuGet package has the following advantages:</span></span>

*   <span data-ttu-id="d417b-230">可以轻松地将提供程序注册到应用程序的配置文件中，使用 NuGet</span><span class="sxs-lookup"><span data-stu-id="d417b-230">It is easy to use NuGet to add the provider registration to the application’s config file</span></span>
*   <span data-ttu-id="d417b-231">可以对配置文件进行其他更改，以设置默认的连接工厂，以便约定建立的连接将使用已注册的提供程序</span><span class="sxs-lookup"><span data-stu-id="d417b-231">Additional changes can be made to the config file to set the default connection factory so that connections made by convention will use the registered provider</span></span>
*   <span data-ttu-id="d417b-232">NuGet 处理添加绑定重定向，以便 EF6 提供程序在新的 EF 包发布后仍可继续工作</span><span class="sxs-lookup"><span data-stu-id="d417b-232">NuGet handles adding binding redirects so that the EF6 provider should continue to work even after a new EF package is released</span></span>

<span data-ttu-id="d417b-233">下面是一个包含在[开源代码库](https://github.com/aspnet/entityframework6)中的 EntityFramework SqlServerCompact 包。</span><span class="sxs-lookup"><span data-stu-id="d417b-233">An example of this is the EntityFramework.SqlServerCompact package which is included in the [open source codebase](https://github.com/aspnet/entityframework6).</span></span> <span data-ttu-id="d417b-234">此包提供了一个用于创建 EF 提供程序 NuGet 包的好模板。</span><span class="sxs-lookup"><span data-stu-id="d417b-234">This package provides a good template for creating EF provider NuGet packages.</span></span>

### <a name="powershell-commands"></a><span data-ttu-id="d417b-235">PowerShell 命令</span><span class="sxs-lookup"><span data-stu-id="d417b-235">PowerShell commands</span></span>

<span data-ttu-id="d417b-236">安装 EntityFramework NuGet 包时，它会注册一个包含两个命令的 PowerShell 模块，这些命令对于提供程序包非常有用：</span><span class="sxs-lookup"><span data-stu-id="d417b-236">When the EntityFramework NuGet package is installed it registers a PowerShell module that contains two commands that are very useful for provider packages:</span></span>

*   <span data-ttu-id="d417b-237">EFProvider 在目标项目的配置文件中为该提供程序添加了一个新的实体，并确保其位于已注册提供程序列表的末尾。</span><span class="sxs-lookup"><span data-stu-id="d417b-237">Add-EFProvider adds a new entity for the provider in the target project’s configuration file and makes sure it is at the end of the list of registered providers.</span></span>
*   <span data-ttu-id="d417b-238">EFDefaultConnectionFactory 添加或更新目标项目的配置文件中的 defaultConnectionFactory 注册。</span><span class="sxs-lookup"><span data-stu-id="d417b-238">Add-EFDefaultConnectionFactory either adds or updates the defaultConnectionFactory registration in the target project’s configuration file.</span></span>

<span data-ttu-id="d417b-239">这两个命令都负责将 entityFramework 节添加到配置文件中，并在必要时添加提供程序集合。</span><span class="sxs-lookup"><span data-stu-id="d417b-239">Both these commands take care of adding an entityFramework section to the config file and adding a providers collection if necessary.</span></span>

<span data-ttu-id="d417b-240">这是为了从安装. ps1 NuGet 脚本中调用这些命令。</span><span class="sxs-lookup"><span data-stu-id="d417b-240">It is intended that these commands be called from the install.ps1 NuGet script.</span></span> <span data-ttu-id="d417b-241">例如，安装. ps1 for SQL Compact 提供程序如下所示：</span><span class="sxs-lookup"><span data-stu-id="d417b-241">For example, install.ps1 for the SQL Compact provider looks similar to this:</span></span>

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

<span data-ttu-id="d417b-242">可以在 "包管理器控制台" 窗口中使用 get-help 获取有关这些命令的详细信息。</span><span class="sxs-lookup"><span data-stu-id="d417b-242">More information about these commands can be obtained by using get-help in the Package Manager Console window.</span></span>

## <a name="wrapping-providers"></a><span data-ttu-id="d417b-243">包装提供程序</span><span class="sxs-lookup"><span data-stu-id="d417b-243">Wrapping providers</span></span>

<span data-ttu-id="d417b-244">包装提供程序是一个 EF 和/或 ADO.NET 提供程序，它包装现有提供程序以使用其他功能（如分析或跟踪功能）对其进行扩展。</span><span class="sxs-lookup"><span data-stu-id="d417b-244">A wrapping provider is an EF and/or ADO.NET provider that wraps an existing provider to extend it with other functionality such as profiling or tracing capabilities.</span></span> <span data-ttu-id="d417b-245">包装提供程序可以通过正常方式进行注册，但在运行时通过拦截与提供程序相关的服务的解决方法，在运行时设置包装提供程序通常更为方便。</span><span class="sxs-lookup"><span data-stu-id="d417b-245">Wrapping providers can be registered in the normal way, but it is often more convenient to setup the wrapping provider at runtime by intercepting the resolution of provider-related services.</span></span> <span data-ttu-id="d417b-246">DbConfiguration 类上的静态事件 OnLockingConfiguration 可用于执行此操作。</span><span class="sxs-lookup"><span data-stu-id="d417b-246">The static event OnLockingConfiguration on the DbConfiguration class can be used to do this.</span></span>

<span data-ttu-id="d417b-247">在 EF 确定了将从其获取应用程序域的所有 EF 配置之后，但在锁定以供使用之前，将调用 OnLockingConfiguration。</span><span class="sxs-lookup"><span data-stu-id="d417b-247">OnLockingConfiguration is called after EF has determined where all EF configuration for the app domain will be obtained from but before it is locked for use.</span></span> <span data-ttu-id="d417b-248">在应用启动时（使用 EF 之前），应用应为此事件注册事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-248">At app startup (before EF is used) the app should register an event handler for this event.</span></span> <span data-ttu-id="d417b-249">（我们正在考虑在配置文件中添加对注册此处理程序的支持，但目前尚不支持此项。）然后，事件处理程序应为需要包装的每个服务调用 ReplaceService。</span><span class="sxs-lookup"><span data-stu-id="d417b-249">(We are considering adding support for registering this handler in the config file but this is not yet supported.) The event handler should then make a call to ReplaceService for every service that needs to be wrapped.</span></span>  

<span data-ttu-id="d417b-250">例如，若要将 IDbConnectionFactory 和 DbProviderService 进行包装，应注册类似于以下内容的处理程序：</span><span class="sxs-lookup"><span data-stu-id="d417b-250">For example, to wrap IDbConnectionFactory and DbProviderService, a handler something like this should be registered:</span></span>

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

<span data-ttu-id="d417b-251">已解决的服务现在应与用于解析服务的密钥一起打包到处理程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-251">The service that has been resolved and should now be wrapped together with the key that was used to resolve the service are passed to the handler.</span></span> <span data-ttu-id="d417b-252">然后，处理程序可以包装此服务，并将返回的服务替换为包装的版本。</span><span class="sxs-lookup"><span data-stu-id="d417b-252">The handler can then wrap this service and replace the returned service with the wrapped version.</span></span>

## <a name="resolving-a-dbproviderfactory-with-ef"></a><span data-ttu-id="d417b-253">使用 EF 解析 DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="d417b-253">Resolving a DbProviderFactory with EF</span></span>

<span data-ttu-id="d417b-254">DbProviderFactory 是 EF 所需的一种基本提供程序类型，如上面的 "_提供程序类型概述_" 部分所述。</span><span class="sxs-lookup"><span data-stu-id="d417b-254">DbProviderFactory is one of the fundamental provider types needed by EF as described in the _Provider types overview_ section above.</span></span> <span data-ttu-id="d417b-255">如上所述，它不是 EF 类型，注册通常不是 EF 配置的一部分，而是在 machine.config 文件和/或应用程序的配置文件中注册的正常 ADO.NET 提供程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-255">As already mentioned, It is not an EF type and registration is usually not part of EF configuration, but is instead the normal ADO.NET provider registration in the machine.config file and/or application’s config file.</span></span>

<span data-ttu-id="d417b-256">尽管在查找要使用的 DbProviderFactory 时，此 EF 仍使用其常规依赖项解析机制。</span><span class="sxs-lookup"><span data-stu-id="d417b-256">Despite this EF still uses its normal dependency resolution mechanism when looking for a DbProviderFactory to use.</span></span> <span data-ttu-id="d417b-257">默认冲突解决程序使用配置文件中的常规 ADO.NET 注册，因此这通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="d417b-257">The default resolver uses the normal ADO.NET registration in the config files and so this is usually transparent.</span></span> <span data-ttu-id="d417b-258">但由于使用的是正常的依赖项解析机制，这意味着即使未完成普通 ADO.NET 注册，也可以使用 IDbDependencyResolver 来解析 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-258">But because of the normal dependency resolution mechanism is used it means that an IDbDependencyResolver can be used to resolve a DbProviderFactory even when normal ADO.NET registration has not been done.</span></span>

<span data-ttu-id="d417b-259">以这种方式解析 DbProviderFactory 有几个含义：</span><span class="sxs-lookup"><span data-stu-id="d417b-259">Resolving DbProviderFactory in this way has several implications:</span></span>

*   <span data-ttu-id="d417b-260">使用基于代码的配置的应用程序可在其 DbConfiguration 类中添加调用来注册相应的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-260">An application using code-based configuration can add calls in their DbConfiguration class to register the appropriate DbProviderFactory.</span></span> <span data-ttu-id="d417b-261">这对于不希望（或无法）使用任何基于文件的配置的应用程序特别有用。</span><span class="sxs-lookup"><span data-stu-id="d417b-261">This is especially useful for applications that do not want to (or cannot) make use of any file-based configuration at all.</span></span>
*   <span data-ttu-id="d417b-262">该服务可以使用 ReplaceService 进行包装或替换，如以上_包装提供商_部分所述</span><span class="sxs-lookup"><span data-stu-id="d417b-262">The service can be wrapped or replaced using ReplaceService as described in the _Wrapping providers_ section above</span></span>
*   <span data-ttu-id="d417b-263">理论上，Dbproviderservices.createdatabase 实现可以解析 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-263">Theoretically, a DbProviderServices implementation could resolve a DbProviderFactory.</span></span>

<span data-ttu-id="d417b-264">执行以上任一操作时需要注意的重要一点是，它们只会影响按 EF 查找 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-264">The important point to note about doing any of these things is that they will only affect the lookup of DbProviderFactory by EF.</span></span> <span data-ttu-id="d417b-265">其他非 EF 代码可能仍会预期 ADO.NET 提供程序以正常方式注册，如果找不到注册，可能会失败。</span><span class="sxs-lookup"><span data-stu-id="d417b-265">Other non-EF code may still expect the ADO.NET provider to be registered in the normal way and may fail if the registration is not found.</span></span> <span data-ttu-id="d417b-266">出于此原因，通过正常的 ADO.NET 方式注册 DbProviderFactory 通常更好。</span><span class="sxs-lookup"><span data-stu-id="d417b-266">For this reason it is normally better for a DbProviderFactory to be registered in the normal ADO.NET way.</span></span>

### <a name="related-services"></a><span data-ttu-id="d417b-267">相关服务</span><span class="sxs-lookup"><span data-stu-id="d417b-267">Related services</span></span>

<span data-ttu-id="d417b-268">如果使用 EF 解析 DbProviderFactory，则它还应该解析 IProviderInvariantName 和 IDbProviderFactoryResolver 服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-268">If EF is used to resolve a DbProviderFactory, then it should also resolve the IProviderInvariantName and IDbProviderFactoryResolver services.</span></span>

<span data-ttu-id="d417b-269">IProviderInvariantName 是一种服务，用于确定给定类型 DbProviderFactory 的提供程序固定名称。</span><span class="sxs-lookup"><span data-stu-id="d417b-269">IProviderInvariantName is a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="d417b-270">此服务的默认实现使用 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="d417b-270">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="d417b-271">这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-271">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span> <span data-ttu-id="d417b-272">请注意，使用 DbConfiguration. SetProviderFactory 方法时，会自动添加此服务的解析程序。</span><span class="sxs-lookup"><span data-stu-id="d417b-272">Note that a resolver for this service is automatically added when using the DbConfiguration.SetProviderFactory method.</span></span>

<span data-ttu-id="d417b-273">如上面的 "_提供程序类型概述_" 部分所述，IDbProviderFactoryResolver 用于从给定的 DbConnection 对象获取正确的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="d417b-273">As described in the _Provider types overview_ section above, the IDbProviderFactoryResolver is used to obtain the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="d417b-274">此服务在 .NET 4 上运行时的默认实现使用 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="d417b-274">The default implementation of this service when running on .NET 4 uses the ADO.NET provider registration.</span></span> <span data-ttu-id="d417b-275">这意味着，如果 ADO.NET 提供程序未以正常方式注册，因为 DbProviderFactory 正在由 EF 解析，则还需要解析此服务。</span><span class="sxs-lookup"><span data-stu-id="d417b-275">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>
