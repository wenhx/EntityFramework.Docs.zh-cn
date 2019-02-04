---
title: Entity Framework 6 提供程序模型的 EF6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
ms.openlocfilehash: 8cbf6f87e0936f374c3d8a0c15a0e1d9c828f764
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668747"
---
# <a name="the-entity-framework-6-provider-model"></a><span data-ttu-id="77b57-102">Entity Framework 6 提供程序模型</span><span class="sxs-lookup"><span data-stu-id="77b57-102">The Entity Framework 6 provider model</span></span>

<span data-ttu-id="77b57-103">实体框架提供程序模型允许实体框架与不同类型的数据库服务器一起使用。</span><span class="sxs-lookup"><span data-stu-id="77b57-103">The Entity Framework provider model allows Entity Framework to be used with different types of database server.</span></span> <span data-ttu-id="77b57-104">例如，一个提供程序可以插入，若要允许另一个提供程序可以插入到，以允许使用针对 Microsoft SQL Server Compact Edition EF 时对 Microsoft SQL Server，使用 EF。</span><span class="sxs-lookup"><span data-stu-id="77b57-104">For example, one provider can be plugged in to allow EF to be used against Microsoft SQL Server, while another provider can be plugged into to allow EF to be used against Microsoft SQL Server Compact Edition.</span></span> <span data-ttu-id="77b57-105">我们已经注意到 EF6 的提供程序，可[实体框架提供程序](~/ef6/fundamentals/providers/index.md)页。</span><span class="sxs-lookup"><span data-stu-id="77b57-105">The providers for EF6 that we are aware of can be found on the [Entity Framework providers](~/ef6/fundamentals/providers/index.md) page.</span></span>

<span data-ttu-id="77b57-106">某些更改而需要向 EF 与提供程序以允许 EF 根据开放源许可发布的交互的方式。</span><span class="sxs-lookup"><span data-stu-id="77b57-106">Certain changes were required to the way EF interacts with providers to allow EF to be released under an open source license.</span></span> <span data-ttu-id="77b57-107">这些更改需要重新生成的 EF 提供者针对与提供程序注册的新机制的 EF6 程序集。</span><span class="sxs-lookup"><span data-stu-id="77b57-107">These changes require rebuilding of EF providers against the EF6 assemblies together with new mechanisms for registration of the provider.</span></span>

## <a name="rebuilding"></a><span data-ttu-id="77b57-108">重新生成</span><span class="sxs-lookup"><span data-stu-id="77b57-108">Rebuilding</span></span>

<span data-ttu-id="77b57-109">与 EF6 现在发送以前是.NET Framework 的一部分的核心代码，以带外 (OOB) 程序集。</span><span class="sxs-lookup"><span data-stu-id="77b57-109">With EF6 the core code that was previously part of the .NET Framework is now being shipped as out-of-band (OOB) assemblies.</span></span> <span data-ttu-id="77b57-110">可以上找到有关如何构建针对 EF6 应用程序的详细信息[更新应用程序的 EF6](~/ef6/what-is-new/upgrading-to-ef6.md)页。</span><span class="sxs-lookup"><span data-stu-id="77b57-110">Details on how to build applications against EF6 can be found on the [Updating applications for EF6](~/ef6/what-is-new/upgrading-to-ef6.md) page.</span></span> <span data-ttu-id="77b57-111">提供程序还需要使用这些说明重新生成。</span><span class="sxs-lookup"><span data-stu-id="77b57-111">Providers will also need to be rebuilt using these instructions.</span></span>

## <a name="provider-types-overview"></a><span data-ttu-id="77b57-112">提供程序类型概述</span><span class="sxs-lookup"><span data-stu-id="77b57-112">Provider types overview</span></span>

<span data-ttu-id="77b57-113">EF 提供程序实际上是特定于提供程序的服务定义的这些服务将从扩展 （适用于基类） 或 （对于接口） 实现的 CLR 类型的集合。</span><span class="sxs-lookup"><span data-stu-id="77b57-113">An EF provider is really a collection of provider-specific services defined by CLR types that these services extend from (for a base class) or implement (for an interface).</span></span> <span data-ttu-id="77b57-114">两个这些服务是基本和 EF 才能完全正常的必要条件。</span><span class="sxs-lookup"><span data-stu-id="77b57-114">Two of these services are fundamental and necessary for EF to function at all.</span></span> <span data-ttu-id="77b57-115">其他人是可选的只需在特定的功能是必需的和/或这些服务的默认实现不适合作为目标的特定数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="77b57-115">Others are optional and only need to be implemented if specific functionality is required and/or the default implementations of these services does not work for the specific database server being targeted.</span></span>

## <a name="fundamental-provider-types"></a><span data-ttu-id="77b57-116">基本提供程序类型</span><span class="sxs-lookup"><span data-stu-id="77b57-116">Fundamental provider types</span></span>

### <a name="dbproviderfactory"></a><span data-ttu-id="77b57-117">DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="77b57-117">DbProviderFactory</span></span>

<span data-ttu-id="77b57-118">EF 依赖于具有某一类型派生自[System.Data.Common.DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx)执行的所有低级数据库访问。</span><span class="sxs-lookup"><span data-stu-id="77b57-118">EF depends on having a type derived from [System.Data.Common.DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) for performing all low-level database access.</span></span> <span data-ttu-id="77b57-119">DbProviderFactory 不是实际的 EF 的一部分，但却为 ADO.NET 提供程序提供的入口点的.NET Framework 中的类可用于通过 EF，其他 O/Rm 或直接由应用程序获取实例的连接、 命令、 参数和提供程序中的其他 ADO.NET 抽象无关的方式。</span><span class="sxs-lookup"><span data-stu-id="77b57-119">DbProviderFactory is not actually part of EF but is instead a class in the .NET Framework that serves an entry point for ADO.NET providers that can be used by EF, other O/RMs or directly by an application to obtain instances of connections, commands, parameters and other ADO.NET abstractions in a provider agnostic way.</span></span> <span data-ttu-id="77b57-120">中可以找到有关 DbProviderFactory 详细信息[ADO.NET 的 MSDN 文档](https://msdn.microsoft.com/library/a6cd7c08.aspx)。</span><span class="sxs-lookup"><span data-stu-id="77b57-120">More information about DbProviderFactory can be found in the [MSDN documentation for ADO.NET](https://msdn.microsoft.com/library/a6cd7c08.aspx).</span></span>

### <a name="dbproviderservices"></a><span data-ttu-id="77b57-121">DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="77b57-121">DbProviderServices</span></span>

<span data-ttu-id="77b57-122">EF 依赖于具有某一类型派生自 DbProviderServices 提供所需的 EF 基于 ADO.NET 提供程序已提供的功能的其他功能。</span><span class="sxs-lookup"><span data-stu-id="77b57-122">EF depends on having a type derived from DbProviderServices for providing additional functionality needed by EF on top of the functionality already provided by the ADO.NET provider.</span></span> <span data-ttu-id="77b57-123">在较旧版本的 EF DbProviderServices 类是.NET Framework 的一部分，未找到 System.Data.Common 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="77b57-123">In older versions of EF the DbProviderServices class was part of the .NET Framework and was found in the System.Data.Common namespace.</span></span> <span data-ttu-id="77b57-124">此类从 EF6 现在是 EntityFramework.dll 的一部分并 System.Data.Entity.Core.Common 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="77b57-124">Starting with EF6 this class is now part of EntityFramework.dll and is in the System.Data.Entity.Core.Common namespace.</span></span>

<span data-ttu-id="77b57-125">可以上找到有关 DbProviderServices 实现的基本功能的更多详细信息[MSDN](https://msdn.microsoft.com/library/ee789835.aspx)。</span><span class="sxs-lookup"><span data-stu-id="77b57-125">More details about the fundamental functionality of a DbProviderServices implementation can be found on [MSDN](https://msdn.microsoft.com/library/ee789835.aspx).</span></span> <span data-ttu-id="77b57-126">但请注意，截至撰写本文时此信息不会更新 ef6 尽管的大多数概念仍然有效。</span><span class="sxs-lookup"><span data-stu-id="77b57-126">However, note that as of the time of writing this information is not updated for EF6 although most of the concepts are still valid.</span></span> <span data-ttu-id="77b57-127">SQL Server 和 SQL Server Compact 的 DbProviderServices 实现到还签入[开源 o d e b](https://github.com/aspnet/EntityFramework6/)并且可作为有用参考信息的其他实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-127">The SQL Server and SQL Server Compact implementations of DbProviderServices are also checked into to the [open-source codebase](https://github.com/aspnet/EntityFramework6/) and can serve as useful references for other implementations.</span></span>

<span data-ttu-id="77b57-128">在较旧版本的 EF 中直接从 ADO.NET 提供程序中获取了要使用的 DbProviderServices 实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-128">In older versions of EF the DbProviderServices implementation to use was obtained directly from an ADO.NET provider.</span></span> <span data-ttu-id="77b57-129">这是通过强制转换为 IServiceProvider，DbProviderFactory 和调用 GetService 方法。</span><span class="sxs-lookup"><span data-stu-id="77b57-129">This was done by casting DbProviderFactory to IServiceProvider and calling the GetService method.</span></span> <span data-ttu-id="77b57-130">这与 DbProviderFactory 紧密耦合的 EF 提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-130">This tightly coupled the EF provider to the DbProviderFactory.</span></span> <span data-ttu-id="77b57-131">这种耦合阻止 EF 移出.NET Framework，因此为 EF6 这种紧密耦合已被删除，直接在应用程序的配置文件中或在基于代码的现已注册的 DbProviderServices 实现配置更多详细信息中所述_注册 DbProviderServices_下面一节。</span><span class="sxs-lookup"><span data-stu-id="77b57-131">This coupling blocked EF from being moved out of the .NET Framework and therefore for EF6 this tight coupling has been removed and an implementation of DbProviderServices is now registered directly in the application’s configuration file or in code-based configuration as described in more detail the _Registering DbProviderServices_ section below.</span></span>

## <a name="additional-services"></a><span data-ttu-id="77b57-132">其他服务</span><span class="sxs-lookup"><span data-stu-id="77b57-132">Additional services</span></span>

<span data-ttu-id="77b57-133">除了上面所述的基本服务还有其他许多服务使用的 EF 始终或有时提供程序特定。</span><span class="sxs-lookup"><span data-stu-id="77b57-133">In addition to the fundamental services described above there are also many other services used by EF which are either always or sometimes provider-specific.</span></span> <span data-ttu-id="77b57-134">DbProviderServices 实现可以提供这些服务的默认提供程序特定于实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-134">Default provider-specific implementations of these services can be supplied by a DbProviderServices implementation.</span></span> <span data-ttu-id="77b57-135">应用程序还可以重写这些服务的实现或 DbProviderServices 类型不提供默认值时提供的实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-135">Applications can also override the implementations of these services, or provide implementations when a DbProviderServices type does not provide a default.</span></span> <span data-ttu-id="77b57-136">这在更多详细信息中所述_解析其他服务_下面一节。</span><span class="sxs-lookup"><span data-stu-id="77b57-136">This is described in more detail in the _Resolving additional services_ section below.</span></span>

<span data-ttu-id="77b57-137">下面列出了提供程序可能需要向提供程序的其他服务类型。</span><span class="sxs-lookup"><span data-stu-id="77b57-137">The additional service types that a provider may be of interest to a provider are listed below.</span></span> <span data-ttu-id="77b57-138">可以在 API 文档中找到有关每个服务类型的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="77b57-138">More details about each of these service types can be found in the API documentation.</span></span>

### <a name="idbexecutionstrategy"></a><span data-ttu-id="77b57-139">IDbExecutionStrategy</span><span class="sxs-lookup"><span data-stu-id="77b57-139">IDbExecutionStrategy</span></span>

<span data-ttu-id="77b57-140">这是一项可选服务，允许提供程序时对数据库执行查询和命令实现重试或其他行为。</span><span class="sxs-lookup"><span data-stu-id="77b57-140">This is an optional service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="77b57-141">如果未不提供任何实现，然后 EF 将只需执行命令并引发任何异常传播。</span><span class="sxs-lookup"><span data-stu-id="77b57-141">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="77b57-142">SQL Server 的此服务用于提供的重试策略，这是针对基于云的数据库服务器，例如 SQL Azure 运行时特别有用。</span><span class="sxs-lookup"><span data-stu-id="77b57-142">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>

### <a name="idbconnectionfactory"></a><span data-ttu-id="77b57-143">IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="77b57-143">IDbConnectionFactory</span></span>

<span data-ttu-id="77b57-144">这是一项可选服务，允许一个提供程序来创建 DbConnection 对象按照约定，如果给定的数据库名称。</span><span class="sxs-lookup"><span data-stu-id="77b57-144">This is an optional service that allows a provider to create DbConnection objects by convention when given only a database name.</span></span> <span data-ttu-id="77b57-145">请注意，虽然 DbProviderServices 实现即可解决此服务，它 EF 4.1 以来一直存在，也可以显式设置配置文件中或在代码中。</span><span class="sxs-lookup"><span data-stu-id="77b57-145">Note that while this service can be resolved by a DbProviderServices implementation it has been present since EF 4.1 and can also be explicitly set in either the config file or in code.</span></span> <span data-ttu-id="77b57-146">提供程序仅将有机会若要解决此服务，如果它注册为默认提供程序 (请参阅_的默认提供程序_下面)，如果默认连接工厂尚未设置其他位置。</span><span class="sxs-lookup"><span data-stu-id="77b57-146">The provider will only get a chance to resolve this service if it registered as the default provider (see _The default provider_ below) and if a default connection factory has not been set elsewhere.</span></span>

### <a name="dbspatialservices"></a><span data-ttu-id="77b57-147">DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="77b57-147">DbSpatialServices</span></span>

<span data-ttu-id="77b57-148">这是一项可选服务，允许一个提供程序来添加对 geography 和 geometry 空间类型的支持。</span><span class="sxs-lookup"><span data-stu-id="77b57-148">This is an optional services that allows a provider to add support for geography and geometry spatial types.</span></span> <span data-ttu-id="77b57-149">为了使应用程序使用的空间类型使用 EF，必须提供此服务的实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-149">An implementation of this service must be supplied in order for an application to use EF with spatial types.</span></span> <span data-ttu-id="77b57-150">DbSptialServices 要求的两种方式。</span><span class="sxs-lookup"><span data-stu-id="77b57-150">DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="77b57-151">首先，提供程序特定的空间服务请求使用 DbProviderInfo 对象 (其中包含固定条件名称和清单标记) 作为键。</span><span class="sxs-lookup"><span data-stu-id="77b57-151">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as key.</span></span> <span data-ttu-id="77b57-152">其次，DbSpatialServices 可以要求不含键。</span><span class="sxs-lookup"><span data-stu-id="77b57-152">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="77b57-153">这用于解决"全局空间提供程序"创建独立的 DbGeography 或 DbGeometry 类型时使用。</span><span class="sxs-lookup"><span data-stu-id="77b57-153">This is used to resolve the “global spatial provider” that is used when creating stand-alone DbGeography or DbGeometry types.</span></span>

### <a name="migrationsqlgenerator"></a><span data-ttu-id="77b57-154">MigrationSqlGenerator</span><span class="sxs-lookup"><span data-stu-id="77b57-154">MigrationSqlGenerator</span></span>

<span data-ttu-id="77b57-155">这是一项可选服务，用于生成使用 SQL 创建和修改数据库架构中由 Code First 的 EF 迁移。</span><span class="sxs-lookup"><span data-stu-id="77b57-155">This is an optional service that allows EF Migrations to be used for the generation of SQL used in creating and modifying database schemas by Code First.</span></span> <span data-ttu-id="77b57-156">实现所需支持迁移。</span><span class="sxs-lookup"><span data-stu-id="77b57-156">An implementation is required in order to support Migrations.</span></span> <span data-ttu-id="77b57-157">如果提供了一个实现然后它将还使用时使用数据库初始值设定项或 Database.Create 方法创建数据库。</span><span class="sxs-lookup"><span data-stu-id="77b57-157">If an implementation is provided then it will also be used when databases are created using database initializers or the Database.Create method.</span></span>

### <a name="funcdbconnection-string-historycontextfactory"></a><span data-ttu-id="77b57-158">Func < DbConnection，HistoryContextFactory 字符串 ></span><span class="sxs-lookup"><span data-stu-id="77b57-158">Func<DbConnection, string, HistoryContextFactory></span></span>

<span data-ttu-id="77b57-159">这是一项可选服务，允许一个提供程序来配置映射到 HistoryContext`__MigrationHistory`使用 EF 迁移的表。</span><span class="sxs-lookup"><span data-stu-id="77b57-159">This is an optional service that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="77b57-160">HistoryContext 是代码的第一个 DbContext，并且可以使用正常的 fluent API 来更改像表和列映射规范的名称进行配置。</span><span class="sxs-lookup"><span data-stu-id="77b57-160">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span> <span data-ttu-id="77b57-161">此服务为所有提供程序返回的 EF 的默认实现可能适用于给定的数据库服务器，如果该提供程序支持所有的默认值表和列映射。</span><span class="sxs-lookup"><span data-stu-id="77b57-161">The default implementation of this service returned by EF for all providers may work for a given database server if all the default table and column mappings are supported by that provider.</span></span> <span data-ttu-id="77b57-162">这种情况下不需要提供此服务的实现提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-162">In such a case the provider does not need to supply an implementation of this service.</span></span>

### <a name="idbproviderfactoryresolver"></a><span data-ttu-id="77b57-163">IDbProviderFactoryResolver</span><span class="sxs-lookup"><span data-stu-id="77b57-163">IDbProviderFactoryResolver</span></span>

<span data-ttu-id="77b57-164">这是一项可选服务用于从给定的 DbConnection 对象获取正确的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="77b57-164">This is an optional service for obtaining the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="77b57-165">此服务为所有提供程序返回的 EF 的默认实现需要适用于所有提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-165">The default implementation of this service returned by EF for all providers is intended to work for all providers.</span></span> <span data-ttu-id="77b57-166">但是，.NET 4 上运行时，DbProviderFactory 不可公开访问从一个 if 其 DbConnections。</span><span class="sxs-lookup"><span data-stu-id="77b57-166">However, when running on .NET 4, the DbProviderFactory is not publicly accessible from one if its DbConnections.</span></span> <span data-ttu-id="77b57-167">因此，EF 使用某些启发搜索已注册的提供程序查找匹配项。</span><span class="sxs-lookup"><span data-stu-id="77b57-167">Therefore, EF uses some heuristics to search the registered providers to find a match.</span></span> <span data-ttu-id="77b57-168">很可能某些提供商对于这些试探法将失败，而且该提供程序应在这种情况下提供的新实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-168">It is possible that for some providers these heuristics will fail and in such situations the provider should supply a new implementation.</span></span>

## <a name="registering-dbproviderservices"></a><span data-ttu-id="77b57-169">注册 DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="77b57-169">Registering DbProviderServices</span></span>

<span data-ttu-id="77b57-170">在应用程序的配置文件 （app.config 或 web.config） 或使用基于代码的配置中，可以注册要使用的 DbProviderServices 实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-170">The DbProviderServices implementation to use can be registered either in the application’s configuration file (app.config or web.config) or using code-based configuration.</span></span> <span data-ttu-id="77b57-171">在任一情况下注册作为键使用提供程序的"固定名称"。</span><span class="sxs-lookup"><span data-stu-id="77b57-171">In either case the registration uses the provider’s “invariant name” as a key.</span></span> <span data-ttu-id="77b57-172">这允许多个提供程序注册和单个应用程序中使用。</span><span class="sxs-lookup"><span data-stu-id="77b57-172">This allows multiple providers to be registered and used in a single application.</span></span> <span data-ttu-id="77b57-173">用于 ADO.NET 提供程序注册和连接字符串的固定名称相同的 EF 注册使用的固定名称。</span><span class="sxs-lookup"><span data-stu-id="77b57-173">The invariant name used for EF registrations is the same as the invariant name used for ADO.NET provider registration and connection strings.</span></span> <span data-ttu-id="77b57-174">例如，对于 SQL Server 的固定名称"System.Data.SqlClient"使用。</span><span class="sxs-lookup"><span data-stu-id="77b57-174">For example, for SQL Server the invariant name “System.Data.SqlClient” is used.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="77b57-175">配置文件注册</span><span class="sxs-lookup"><span data-stu-id="77b57-175">Config file registration</span></span>

<span data-ttu-id="77b57-176">要使用的 DbProviderServices 类型注册为应用程序配置文件的 entityFramework 部分的提供程序列表中的提供程序元素。</span><span class="sxs-lookup"><span data-stu-id="77b57-176">The DbProviderServices type to use is registered as a provider element in the providers list of the entityFramework section of the application’s config file.</span></span> <span data-ttu-id="77b57-177">例如：</span><span class="sxs-lookup"><span data-stu-id="77b57-177">For example:</span></span>

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

<span data-ttu-id="77b57-178">_类型_字符串必须是要使用的 DbProviderServices 实现的程序集限定类型名称。</span><span class="sxs-lookup"><span data-stu-id="77b57-178">The _type_ string must be the assembly-qualified type name of the DbProviderServices implementation to use.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="77b57-179">基于代码的注册</span><span class="sxs-lookup"><span data-stu-id="77b57-179">Code-based registration</span></span>

<span data-ttu-id="77b57-180">此外可使用代码注册从 EF6 提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-180">Starting with EF6 providers can also be registered using code.</span></span> <span data-ttu-id="77b57-181">这允许要使用而无需对应用程序的配置文件的任何更改的 EF 提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-181">This allows an EF provider to be used without any change to the application’s configuration file.</span></span> <span data-ttu-id="77b57-182">若要使用基于代码的配置应用程序应创建一个 DbConfiguration 类，如中所述[基于代码的配置文档](https://msdn.com/data/jj680699)。</span><span class="sxs-lookup"><span data-stu-id="77b57-182">To use code-based configuration an application should create a DbConfiguration class as described in the [code-based configuration documentation](https://msdn.com/data/jj680699).</span></span> <span data-ttu-id="77b57-183">然后，DbConfiguration 类的构造函数应调用 SetProviderServices 注册 EF 提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-183">The constructor of the DbConfiguration class should then call SetProviderServices to register the EF provider.</span></span> <span data-ttu-id="77b57-184">例如：</span><span class="sxs-lookup"><span data-stu-id="77b57-184">For example:</span></span>

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a><span data-ttu-id="77b57-185">解析其他服务</span><span class="sxs-lookup"><span data-stu-id="77b57-185">Resolving additional services</span></span>

<span data-ttu-id="77b57-186">如中所述_提供程序类型概述_部分 DbProviderServices 类还可用于解决其他服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-186">As mentioned above in the _Provider types overview_ section, a DbProviderServices class can also be used to resolve additional services.</span></span> <span data-ttu-id="77b57-187">这可能是因为 DbProviderServices 实现 IDbDependencyResolver 并且每个已注册的 DbProviderServices 类型添加为"默认解析程序"。</span><span class="sxs-lookup"><span data-stu-id="77b57-187">This is possible because DbProviderServices implements IDbDependencyResolver and each registered DbProviderServices type is added as a “default resolver”.</span></span> <span data-ttu-id="77b57-188">中更详细地介绍 IDbDpendencyResolver 机制[依赖项解析](~/ef6/fundamentals/configuring/dependency-resolution.md)。</span><span class="sxs-lookup"><span data-stu-id="77b57-188">The IDbDpendencyResolver mechanism is described in more detail in [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md).</span></span> <span data-ttu-id="77b57-189">但是，不需要了解此规范，若要解决提供程序中的其他服务中的所有概念。</span><span class="sxs-lookup"><span data-stu-id="77b57-189">However, it is not necessary to understand all the concepts in this specification to resolve additional services in a provider.</span></span>

<span data-ttu-id="77b57-190">若要解决其他服务的提供程序的最常见方法是调用 DbProviderServices.AddDependencyResolver DbProviderServices 类的构造函数中每个服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-190">The most common way for a provider to resolve additional services is to call DbProviderServices.AddDependencyResolver for each service in the constructor of the DbProviderServices class.</span></span> <span data-ttu-id="77b57-191">例如，SqlProviderServices （SQL Server 的 EF 提供程序） 具有类似于此的初始化代码：</span><span class="sxs-lookup"><span data-stu-id="77b57-191">For example, SqlProviderServices (the EF provider for SQL Server) has code similar to this for initialization:</span></span>

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

<span data-ttu-id="77b57-192">此构造函数使用以下帮助器类：</span><span class="sxs-lookup"><span data-stu-id="77b57-192">This constructor uses the following helper classes:</span></span>

*   <span data-ttu-id="77b57-193">SingletonDependencyResolver： 提供用于解析单一实例服务的简单方法，即为其同一个实例返回每次调用 GetService 的服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-193">SingletonDependencyResolver: provides a simple way to resolve Singleton services—that is, services for which the same instance is returned each time that GetService is called.</span></span> <span data-ttu-id="77b57-194">暂时性服务通常注册为一个单一实例工厂，它将用于按需创建暂时性的实例。</span><span class="sxs-lookup"><span data-stu-id="77b57-194">Transient services are often registered as a singleton factory that will be used to create transient instances on demand.</span></span>
*   <span data-ttu-id="77b57-195">ExecutionStrategyResolver： 冲突解决程序特定于返回 IExecutionStrategy 实现。</span><span class="sxs-lookup"><span data-stu-id="77b57-195">ExecutionStrategyResolver: a resolver specific to returning IExecutionStrategy implementations.</span></span>

<span data-ttu-id="77b57-196">而不是使用 DbProviderServices.AddDependencyResolver 还有可能重写 DbProviderServices.GetService 并直接解决其他服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-196">Instead of using DbProviderServices.AddDependencyResolver it is also possible to override DbProviderServices.GetService and resolve additional services directly.</span></span> <span data-ttu-id="77b57-197">当 EF 需要定义通过某一类型，然后在某些情况下，给定密钥的服务时，将调用此方法。</span><span class="sxs-lookup"><span data-stu-id="77b57-197">This method will be called when EF needs a service defined by a certain type and, in some cases, for a given key.</span></span> <span data-ttu-id="77b57-198">如果它可以或返回为 null，以选择退出的返回该服务并允许另一个类来解决此问题，该方法应返回该服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-198">The method should return the service if it can, or return null to opt-out of returning the service and instead allow another class to resolve it.</span></span> <span data-ttu-id="77b57-199">例如，若要解决的默认连接工厂 GetService 中的代码可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="77b57-199">For example, to resolve the default connection factory the code in GetService might look something like this:</span></span>

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

### <a name="registration-order"></a><span data-ttu-id="77b57-200">注册顺序</span><span class="sxs-lookup"><span data-stu-id="77b57-200">Registration order</span></span>

<span data-ttu-id="77b57-201">在应用程序的配置文件中注册的多个 DbProviderServices 实现后，它们将作为辅助冲突解决程序中所列的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="77b57-201">When multiple DbProviderServices implementations are registered in an application’s config file they will be added as secondary resolvers in the order that they are listed.</span></span> <span data-ttu-id="77b57-202">由于冲突解决程序总是会添加到辅助冲突解决程序链顶部这意味着，在列表末尾的提供程序将有机会解决先于其他的依赖项。</span><span class="sxs-lookup"><span data-stu-id="77b57-202">Since resolvers are always added to the top of the secondary resolver chain this means that the provider at the end of the list will get a chance to resolve dependencies before the others.</span></span> <span data-ttu-id="77b57-203">（这可能看起来稍有违反语感一开始，但它是有意义，如果您假设每个提供程序的列表，并堆叠在现有提供程序一起）。</span><span class="sxs-lookup"><span data-stu-id="77b57-203">(This can seem a little counter-intuitive at first, but it makes sense if you imagine taking each provider out of the list and stacking it on top of the existing providers.)</span></span>

<span data-ttu-id="77b57-204">这种顺序通常并不重要因为大多数提供程序服务是特定于提供程序和由提供程序固定名称键控。</span><span class="sxs-lookup"><span data-stu-id="77b57-204">This ordering usually doesn’t matter because most provider services are provider-specific and keyed by provider invariant name.</span></span> <span data-ttu-id="77b57-205">但是，为服务，不由进行键控提供程序固定名称或某些其他特定于提供程序的密钥将解析该服务基于此排序。</span><span class="sxs-lookup"><span data-stu-id="77b57-205">However, for services that are not keyed by provider invariant name or some other provider-specific key the service will be resolved based on this ordering.</span></span> <span data-ttu-id="77b57-206">例如，如果未显式设置以不同方式某处 else，然后使用默认连接工厂将来自链中的最顶层的提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-206">For example, if it is not explicitly set differently somewhere else, then the default connection factory will come from the topmost provider in the chain.</span></span>

## <a name="additional-config-file-registrations"></a><span data-ttu-id="77b57-207">其他配置文件注册</span><span class="sxs-lookup"><span data-stu-id="77b57-207">Additional config file registrations</span></span>

<span data-ttu-id="77b57-208">就可以显式注册的某些其他提供程序服务直接在应用程序的配置文件中上面所述。</span><span class="sxs-lookup"><span data-stu-id="77b57-208">It is possible to explicitly register some of the additional provider services described above directly in an application’s config file.</span></span> <span data-ttu-id="77b57-209">完成此操作后的配置文件中的注册，将使用而不是任何返回的 DbProviderServices 实现的 GetService 方法。</span><span class="sxs-lookup"><span data-stu-id="77b57-209">When this is done the registration in the config file will be used instead of anything returned by the GetService method of the DbProviderServices implementation.</span></span>

### <a name="registering-the-default-connection-factory"></a><span data-ttu-id="77b57-210">注册默认连接工厂</span><span class="sxs-lookup"><span data-stu-id="77b57-210">Registering the default connection factory</span></span>

<span data-ttu-id="77b57-211">从 EF5 EntityFramework NuGet 包自动注册的 SQL Express 连接工厂或 LocalDb 连接工厂的配置文件中。</span><span class="sxs-lookup"><span data-stu-id="77b57-211">Starting with EF5 the EntityFramework NuGet package automatically registered either the SQL Express connection factory or the LocalDb connection factory in the config file.</span></span>

<span data-ttu-id="77b57-212">例如：</span><span class="sxs-lookup"><span data-stu-id="77b57-212">For example:</span></span>

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

<span data-ttu-id="77b57-213">_类型_是默认连接工厂必须实现 IDbConnectionFactory 的程序集限定类型名称。</span><span class="sxs-lookup"><span data-stu-id="77b57-213">The _type_ is the assembly-qualified type name for the default connection factory, which must implement IDbConnectionFactory.</span></span>

<span data-ttu-id="77b57-214">建议提供程序 NuGet 包安装时这种方式设置的默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="77b57-214">It is recommended that a provider NuGet package set the default connection factory in this way when installed.</span></span> <span data-ttu-id="77b57-215">请参阅_提供程序的 NuGet 包_下面。</span><span class="sxs-lookup"><span data-stu-id="77b57-215">See _NuGet Packages for providers_ below.</span></span>

## <a name="additional-ef6-provider-changes"></a><span data-ttu-id="77b57-216">其他 EF6 提供程序更改</span><span class="sxs-lookup"><span data-stu-id="77b57-216">Additional EF6 provider changes</span></span>

### <a name="spatial-provider-changes"></a><span data-ttu-id="77b57-217">空间提供程序更改</span><span class="sxs-lookup"><span data-stu-id="77b57-217">Spatial provider changes</span></span>

<span data-ttu-id="77b57-218">从 DbSpatialDataReader 派生类中支持空间类型的提供程序现在必须实现一些其他方法：</span><span class="sxs-lookup"><span data-stu-id="77b57-218">Providers that support spatial types must now implement some additional methods on classes deriving from DbSpatialDataReader:</span></span>

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

<span data-ttu-id="77b57-219">也有新的建议的默认实现委托给同步方法，并因此不会执行以异步方式重写的现有方法的异步版本：</span><span class="sxs-lookup"><span data-stu-id="77b57-219">There are also new asynchronous versions of existing methods that are recommended to be overridden as the default implementations delegate to the synchronous methods and therefore do not execute asynchronously:</span></span>

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a><span data-ttu-id="77b57-220">对 Enumerable.Contains 的本机支持</span><span class="sxs-lookup"><span data-stu-id="77b57-220">Native support for Enumerable.Contains</span></span>

<span data-ttu-id="77b57-221">EF6 引入了新的表达式类型 DbInExpression，已添加到在 LINQ 查询中使用 Enumerable.Contains 围绕解决性能问题。</span><span class="sxs-lookup"><span data-stu-id="77b57-221">EF6 introduces a new expression type, DbInExpression, which was added to address performance issues around use of Enumerable.Contains in LINQ queries.</span></span> <span data-ttu-id="77b57-222">DbProviderManifest 类有一个新的虚拟方法，SupportsInExpression，EF 来确定处理新的表达式类型的提供的调用。</span><span class="sxs-lookup"><span data-stu-id="77b57-222">The DbProviderManifest class has a new virtual method, SupportsInExpression, which is called by EF to determine if a provider handles the new expression type.</span></span> <span data-ttu-id="77b57-223">与现有提供程序实现的兼容性，方法返回 false。</span><span class="sxs-lookup"><span data-stu-id="77b57-223">For compatibility with existing provider implementations the method returns false.</span></span> <span data-ttu-id="77b57-224">若要从这一改进中获益，EF6 提供程序可以添加代码以处理 DbInExpression 并重写 SupportsInExpression 返回 true。</span><span class="sxs-lookup"><span data-stu-id="77b57-224">To benefit from this improvement, an EF6 provider can add code to handle DbInExpression and override SupportsInExpression to return true.</span></span> <span data-ttu-id="77b57-225">可以通过调用 DbExpressionBuilder.In 方法创建的 DbInExpression 实例。</span><span class="sxs-lookup"><span data-stu-id="77b57-225">An instance of DbInExpression can be created by calling the DbExpressionBuilder.In method.</span></span> <span data-ttu-id="77b57-226">DbInExpression 实例组成 DbExpression，通常表示表列和 DbConstantExpression 要测试其匹配项的列表。</span><span class="sxs-lookup"><span data-stu-id="77b57-226">A DbInExpression instance is composed of a DbExpression, usually representing a table column, and a list of DbConstantExpression to test for a match.</span></span>

## <a name="nuget-packages-for-providers"></a><span data-ttu-id="77b57-227">提供程序的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="77b57-227">NuGet packages for providers</span></span>

<span data-ttu-id="77b57-228">若要使 EF6 提供程序可用的一种方法是作为 NuGet 包发布。</span><span class="sxs-lookup"><span data-stu-id="77b57-228">One way to make an EF6 provider available is to release it as a NuGet package.</span></span> <span data-ttu-id="77b57-229">使用 NuGet 包具有以下优点：</span><span class="sxs-lookup"><span data-stu-id="77b57-229">Using a NuGet package has the following advantages:</span></span>

*   <span data-ttu-id="77b57-230">它是易于使用的 NuGet 提供程序注册添加到应用程序的配置文件</span><span class="sxs-lookup"><span data-stu-id="77b57-230">It is easy to use NuGet to add the provider registration to the application’s config file</span></span>
*   <span data-ttu-id="77b57-231">其他更改可以对配置文件来设置默认连接工厂，以便按照约定进行的连接将使用已注册的提供程序</span><span class="sxs-lookup"><span data-stu-id="77b57-231">Additional changes can be made to the config file to set the default connection factory so that connections made by convention will use the registered provider</span></span>
*   <span data-ttu-id="77b57-232">添加绑定重定向，以便 EF6 提供程序应继续工作，即使新的 EF 程序包发布的 NuGet 句柄</span><span class="sxs-lookup"><span data-stu-id="77b57-232">NuGet handles adding binding redirects so that the EF6 provider should continue to work even after a new EF package is released</span></span>

<span data-ttu-id="77b57-233">此示例是 EntityFramework.SqlServerCompact 包随附于[开源 o d e b](http://github.com/aspnet/entityframework6)。</span><span class="sxs-lookup"><span data-stu-id="77b57-233">An example of this is the EntityFramework.SqlServerCompact package which is included in the [open source codebase](http://github.com/aspnet/entityframework6).</span></span> <span data-ttu-id="77b57-234">此包提供用于创建 EF 提供程序 NuGet 包的合适的模板。</span><span class="sxs-lookup"><span data-stu-id="77b57-234">This package provides a good template for creating EF provider NuGet packages.</span></span>

### <a name="powershell-commands"></a><span data-ttu-id="77b57-235">PowerShell 命令</span><span class="sxs-lookup"><span data-stu-id="77b57-235">PowerShell commands</span></span>

<span data-ttu-id="77b57-236">安装 EntityFramework NuGet 包时它将注册的 PowerShell 模块，包含对于提供程序的包非常有用的两个命令：</span><span class="sxs-lookup"><span data-stu-id="77b57-236">When the EntityFramework NuGet package is installed it registers a PowerShell module that contains two commands that are very useful for provider packages:</span></span>

*   <span data-ttu-id="77b57-237">添加 EFProvider 将新实体添加目标项目的配置文件中的提供程序，并确保它在已注册的提供程序的列表的末尾处。</span><span class="sxs-lookup"><span data-stu-id="77b57-237">Add-EFProvider adds a new entity for the provider in the target project’s configuration file and makes sure it is at the end of the list of registered providers.</span></span>
*   <span data-ttu-id="77b57-238">添加 EFDefaultConnectionFactory 添加，或更新目标项目的配置文件中的 defaultConnectionFactory 注册。</span><span class="sxs-lookup"><span data-stu-id="77b57-238">Add-EFDefaultConnectionFactory either adds or updates the defaultConnectionFactory registration in the target project’s configuration file.</span></span>

<span data-ttu-id="77b57-239">这两个命令负责将 entityFramework 部分添加到配置文件并添加提供程序集合，如有必要。</span><span class="sxs-lookup"><span data-stu-id="77b57-239">Both these commands take care of adding an entityFramework section to the config file and adding a providers collection if necessary.</span></span>

<span data-ttu-id="77b57-240">其目的是，从 install.ps1 NuGet 脚本调用这些命令。</span><span class="sxs-lookup"><span data-stu-id="77b57-240">It is intended that these commands be called from the install.ps1 NuGet script.</span></span> <span data-ttu-id="77b57-241">例如，SQL Compact 提供程序的 install.ps1 看起来类似于此：</span><span class="sxs-lookup"><span data-stu-id="77b57-241">For example, install.ps1 for the SQL Compact provider looks similar to this:</span></span>

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

<span data-ttu-id="77b57-242">可以通过包管理器控制台窗口中使用 get-help 获取有关这些命令的详细信息。</span><span class="sxs-lookup"><span data-stu-id="77b57-242">More information about these commands can be obtained by using get-help in the Package Manager Console window.</span></span>

## <a name="wrapping-providers"></a><span data-ttu-id="77b57-243">包装提供程序</span><span class="sxs-lookup"><span data-stu-id="77b57-243">Wrapping providers</span></span>

<span data-ttu-id="77b57-244">换行提供程序是包装现有的提供程序以使用其他功能，如分析或跟踪功能来扩展它的 EF 和/或 ADO.NET 提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-244">A wrapping provider is an EF and/or ADO.NET provider that wraps an existing provider to extend it with other functionality such as profiling or tracing capabilities.</span></span> <span data-ttu-id="77b57-245">包装提供程序可以注册以正常方式，但通常会更方便地通过截获与提供程序相关的服务的分辨率设置在运行时包装提供程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-245">Wrapping providers can be registered in the normal way, but it is often more convenient to setup the wrapping provider at runtime by intercepting the resolution of provider-related services.</span></span> <span data-ttu-id="77b57-246">DbConfiguration 类上的静态事件 OnLockingConfiguration 可用来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="77b57-246">The static event OnLockingConfiguration on the DbConfiguration class can be used to do this.</span></span>

<span data-ttu-id="77b57-247">OnLockingConfiguration 称为 EF 已确定将来获取应用程序域的所有 EF 配置后，但用于锁定之前。</span><span class="sxs-lookup"><span data-stu-id="77b57-247">OnLockingConfiguration is called after EF has determined where all EF configuration for the app domain will be obtained from but before it is locked for use.</span></span> <span data-ttu-id="77b57-248">在应用启动时 （EF 使用之前） 应用程序应注册此事件的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-248">At app startup (before EF is used) the app should register an event handler for this event.</span></span> <span data-ttu-id="77b57-249">（我们正在考虑添加对配置文件中注册此处理程序的支持，但尚不支持，此）。事件处理程序应请 ReplaceService 以包装所需的每个服务调用。</span><span class="sxs-lookup"><span data-stu-id="77b57-249">(We are considering adding support for registering this handler in the config file but this is not yet supported.) The event handler should then make a call to ReplaceService for every service that needs to be wrapped.</span></span>  

<span data-ttu-id="77b57-250">例如，来包装 IDbConnectionFactory 和 DbProviderService，应注册一个处理程序如下所示：</span><span class="sxs-lookup"><span data-stu-id="77b57-250">For example, to wrap IDbConnectionFactory and DbProviderService, a handler something like this should be registered:</span></span>

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

<span data-ttu-id="77b57-251">已解决，现在应该用于将服务解析的关键字以及包装的服务将传递给处理程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-251">The service that has been resolved and should now be wrapped together with the key that was used to resolve the service are passed to the handler.</span></span> <span data-ttu-id="77b57-252">然后，该处理程序可以包装此服务，并返回的服务替换为已包装的版本。</span><span class="sxs-lookup"><span data-stu-id="77b57-252">The handler can then wrap this service and replace the returned service with the wrapped version.</span></span>

## <a name="resolving-a-dbproviderfactory-with-ef"></a><span data-ttu-id="77b57-253">解决使用 EF DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="77b57-253">Resolving a DbProviderFactory with EF</span></span>

<span data-ttu-id="77b57-254">DbProviderFactory 是所需的 EF 中所述的基本提供程序类型之一_提供程序类型概述_上面一节。</span><span class="sxs-lookup"><span data-stu-id="77b57-254">DbProviderFactory is one of the fundamental provider types needed by EF as described in the _Provider types overview_ section above.</span></span> <span data-ttu-id="77b57-255">如前面所述，它不是 EF 类型和注册通常不 EF 配置的一部分，而是改为在 machine.config 文件和/或应用程序配置文件正常的 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="77b57-255">As already mentioned, It is not an EF type and registration is usually not part of EF configuration, but is instead the normal ADO.NET provider registration in the machine.config file and/or application’s config file.</span></span>

<span data-ttu-id="77b57-256">尽管此 EF 仍使用其普通依赖项解析机制查找 DbProviderFactory 时使用。</span><span class="sxs-lookup"><span data-stu-id="77b57-256">Despite this EF still uses its normal dependency resolution mechanism when looking for a DbProviderFactory to use.</span></span> <span data-ttu-id="77b57-257">默认冲突解决程序在配置文件中使用普通 ADO.NET 注册并因此这通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="77b57-257">The default resolver uses the normal ADO.NET registration in the config files and so this is usually transparent.</span></span> <span data-ttu-id="77b57-258">由于正常的依赖项解析使用机制，但这意味着 IDbDependencyResolver 可用于解决 DbProviderFactory，即使尚未执行普通 ADO.NET 注册。</span><span class="sxs-lookup"><span data-stu-id="77b57-258">But because of the normal dependency resolution mechanism is used it means that an IDbDependencyResolver can be used to resolve a DbProviderFactory even when normal ADO.NET registration has not been done.</span></span>

<span data-ttu-id="77b57-259">在这种方式中解决 DbProviderFactory 具有几个含义：</span><span class="sxs-lookup"><span data-stu-id="77b57-259">Resolving DbProviderFactory in this way has several implications:</span></span>

*   <span data-ttu-id="77b57-260">使用基于代码的配置的应用程序可以在注册相应的 DbProviderFactory 其 DbConfiguration 类中添加调用。</span><span class="sxs-lookup"><span data-stu-id="77b57-260">An application using code-based configuration can add calls in their DbConfiguration class to register the appropriate DbProviderFactory.</span></span> <span data-ttu-id="77b57-261">这是特别有用的应用程序不希望 （或无法） 进行根本使用的任何基于文件的配置。</span><span class="sxs-lookup"><span data-stu-id="77b57-261">This is especially useful for applications that do not want to (or cannot) make use of any file-based configuration at all.</span></span>
*   <span data-ttu-id="77b57-262">该服务可以包装或替换中所述，使用 ReplaceService_包装提供程序_上面部分</span><span class="sxs-lookup"><span data-stu-id="77b57-262">The service can be wrapped or replaced using ReplaceService as described in the _Wrapping providers_ section above</span></span>
*   <span data-ttu-id="77b57-263">理论上讲，DbProviderServices 实现无法解析 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="77b57-263">Theoretically, a DbProviderServices implementation could resolve a DbProviderFactory.</span></span>

<span data-ttu-id="77b57-264">关于执行其中的任何内容需要注意的重要一点是，它们只会影响由 EF 的 DbProviderFactory 的查找。</span><span class="sxs-lookup"><span data-stu-id="77b57-264">The important point to note about doing any of these things is that they will only affect the lookup of DbProviderFactory by EF.</span></span> <span data-ttu-id="77b57-265">其他非 EF 代码仍可能要以正常方式注册的 ADO.NET 提供程序，如果找不到注册可能会失败。</span><span class="sxs-lookup"><span data-stu-id="77b57-265">Other non-EF code may still expect the ADO.NET provider to be registered in the normal way and may fail if the registration is not found.</span></span> <span data-ttu-id="77b57-266">出于此原因是通常更适合普通的 ADO.NET 方法中注册 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="77b57-266">For this reason it is normally better for a DbProviderFactory to be registered in the normal ADO.NET way.</span></span>

### <a name="related-services"></a><span data-ttu-id="77b57-267">相关的服务</span><span class="sxs-lookup"><span data-stu-id="77b57-267">Related services</span></span>

<span data-ttu-id="77b57-268">如果 EF 用于解析 DbProviderFactory，则它还应解析的 IProviderInvariantName 和 IDbProviderFactoryResolver 服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-268">If EF is used to resolve a DbProviderFactory, then it should also resolve the IProviderInvariantName and IDbProviderFactoryResolver services.</span></span>

<span data-ttu-id="77b57-269">IProviderInvariantName 是一种服务，用于确定给定类型的 DbProviderFactory 的提供程序固定名称。</span><span class="sxs-lookup"><span data-stu-id="77b57-269">IProviderInvariantName is a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="77b57-270">此服务的默认实现使用 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="77b57-270">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="77b57-271">这意味着，如果因为 DbProviderFactory 正在解析由 EF，ADO.NET 提供程序未注册以正常方式，然后它将还需要解析此服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-271">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span> <span data-ttu-id="77b57-272">请注意使用 DbConfiguration.SetProviderFactory 方法时，会自动添加此服务的冲突解决程序。</span><span class="sxs-lookup"><span data-stu-id="77b57-272">Note that a resolver for this service is automatically added when using the DbConfiguration.SetProviderFactory method.</span></span>

<span data-ttu-id="77b57-273">如中所述_提供程序类型概述_上面部分中，IDbProviderFactoryResolver 用于从给定的 DbConnection 对象获取正确的 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="77b57-273">As described in the _Provider types overview_ section above, the IDbProviderFactoryResolver is used to obtain the correct DbProviderFactory from a given DbConnection object.</span></span> <span data-ttu-id="77b57-274">此服务在.NET 4 运行时的默认实现使用 ADO.NET 提供程序注册。</span><span class="sxs-lookup"><span data-stu-id="77b57-274">The default implementation of this service when running on .NET 4 uses the ADO.NET provider registration.</span></span> <span data-ttu-id="77b57-275">这意味着，如果因为 DbProviderFactory 正在解析由 EF，ADO.NET 提供程序未注册以正常方式，然后它将还需要解析此服务。</span><span class="sxs-lookup"><span data-stu-id="77b57-275">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>
