---
title: 实体框架提供程序 - EF6
description: Entity Framework 6 中的实体框架提供程序
author: divega
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: 7d448f749448e35702c2440717593221324508fa
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210323"
---
# <a name="entity-framework-6-providers"></a><span data-ttu-id="ea955-103">实体框架 6 提供程序</span><span class="sxs-lookup"><span data-stu-id="ea955-103">Entity Framework 6 Providers</span></span>
> [!NOTE]
> <span data-ttu-id="ea955-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="ea955-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ea955-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="ea955-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="ea955-106">实体框架现在正在开源许可证下开发，EF6 及更高版本不会作为 .NET Framework 的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="ea955-106">The Entity Framework is now being developed under an open-source license and EF6 and above will not be shipped as part of the .NET Framework.</span></span> <span data-ttu-id="ea955-107">这样做有许多好处，但也需要针对 EF6 程序集重建 EF 提供程序。</span><span class="sxs-lookup"><span data-stu-id="ea955-107">This has many advantages but also requires that EF providers be rebuilt against the EF6 assemblies.</span></span> <span data-ttu-id="ea955-108">这就意味着 EF5 及以下版本的 EF 提供程序在重建之前不能用于 EF6。</span><span class="sxs-lookup"><span data-stu-id="ea955-108">This means that EF providers for EF5 and below will not work with EF6 until they have been rebuilt.</span></span>

## <a name="which-providers-are-available-for-ef6"></a><span data-ttu-id="ea955-109">哪些提供程序可用于 EF6？</span><span class="sxs-lookup"><span data-stu-id="ea955-109">Which providers are available for EF6?</span></span>

<span data-ttu-id="ea955-110">已知的针对 EF6 重建的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="ea955-110">Providers we are aware of that have been rebuilt for EF6 include:</span></span>

*   <span data-ttu-id="ea955-111">**Microsoft SQL Server 提供程序**</span><span class="sxs-lookup"><span data-stu-id="ea955-111">**Microsoft SQL Server provider**</span></span>
    *   <span data-ttu-id="ea955-112">构建于[实体框架开源代码库](https://github.com/aspnet/EntityFramework6)</span><span class="sxs-lookup"><span data-stu-id="ea955-112">Built from the [Entity Framework open source code base](https://github.com/aspnet/EntityFramework6)</span></span>
    *   <span data-ttu-id="ea955-113">作为 [EntityFramework NuGet 包](https://nuget.org/packages/EntityFramework)的一部分提供</span><span class="sxs-lookup"><span data-stu-id="ea955-113">Shipped as part of the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework)</span></span>
*   <span data-ttu-id="ea955-114">**Microsoft SQL Server Compact Edition 提供程序**</span><span class="sxs-lookup"><span data-stu-id="ea955-114">**Microsoft SQL Server Compact Edition provider**</span></span>
    *   <span data-ttu-id="ea955-115">构建于[实体框架开源代码库](https://github.com/aspnet/EntityFramework6)</span><span class="sxs-lookup"><span data-stu-id="ea955-115">Built from the [Entity Framework open source code base](https://github.com/aspnet/EntityFramework6)</span></span>
    *   <span data-ttu-id="ea955-116">在 [EntityFramework.SqlServerCompact NuGet 包](https://nuget.org/packages/EntityFramework.SqlServerCompact)中提供</span><span class="sxs-lookup"><span data-stu-id="ea955-116">Shipped in the [EntityFramework.SqlServerCompact NuGet package](https://nuget.org/packages/EntityFramework.SqlServerCompact)</span></span>
*   [<span data-ttu-id="ea955-117">**Devart dotConnect 数据提供程序**</span><span class="sxs-lookup"><span data-stu-id="ea955-117">**Devart dotConnect Data Providers**</span></span>](https://www.devart.com/dotconnect/)
    *   <span data-ttu-id="ea955-118">来自 [Devart](https://www.devart.com/) 的提供各种数据库的第三方提供程序，包括 Oracle、MySQL、PostgreSQL、SQLite、Salesforce、DB2 和 SQL Server</span><span class="sxs-lookup"><span data-stu-id="ea955-118">There are third-party providers from [Devart](https://www.devart.com/) for a variety of databases including Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2, and SQL Server</span></span>
*   [<span data-ttu-id="ea955-119">**CData Software 提供程序**</span><span class="sxs-lookup"><span data-stu-id="ea955-119">**CData Software providers**</span></span>](https://www.cdata.com/ado/)
    *   <span data-ttu-id="ea955-120">来自 [CData Software](https://www.cdata.com/ado/) 的提供各种数据存储的第三方提供程序，包括 Salesforce、Azure 表存储、MySql 等等</span><span class="sxs-lookup"><span data-stu-id="ea955-120">There are third-party providers from [CData Software](https://www.cdata.com/ado/) for a variety of data stores including Salesforce, Azure Table Storage, MySql, and many more</span></span>
*   <span data-ttu-id="ea955-121">**Firebird 提供程序**</span><span class="sxs-lookup"><span data-stu-id="ea955-121">**Firebird provider**</span></span>
    *   <span data-ttu-id="ea955-122">作为 [NuGet 包](https://www.nuget.org/packages/EntityFramework.Firebird/)提供</span><span class="sxs-lookup"><span data-stu-id="ea955-122">Available as a [NuGet Package](https://www.nuget.org/packages/EntityFramework.Firebird/)</span></span>
*   <span data-ttu-id="ea955-123">**Visual Fox Pro 提供程序**</span><span class="sxs-lookup"><span data-stu-id="ea955-123">**Visual Fox Pro provider**</span></span>
    *   <span data-ttu-id="ea955-124">作为 [NuGet 包](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/) 提供</span><span class="sxs-lookup"><span data-stu-id="ea955-124">Available as a [NuGet package](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)</span></span>
*   <span data-ttu-id="ea955-125">**MySQL**</span><span class="sxs-lookup"><span data-stu-id="ea955-125">**MySQL**</span></span>
    *   [<span data-ttu-id="ea955-126">MySQL Connector/NET for Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ea955-126">MySQL Connector/NET for Entity Framework</span></span>](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   <span data-ttu-id="ea955-127">**PostgreSQL**</span><span class="sxs-lookup"><span data-stu-id="ea955-127">**PostgreSQL**</span></span>
    *   <span data-ttu-id="ea955-128">Npgsql 作为 [NuGet 包](https://www.nuget.org/packages/EntityFramework6.Npgsql/)提供</span><span class="sxs-lookup"><span data-stu-id="ea955-128">Npgsql is available as a [NuGet package](https://www.nuget.org/packages/EntityFramework6.Npgsql/)</span></span>
*   <span data-ttu-id="ea955-129">**Oracle**</span><span class="sxs-lookup"><span data-stu-id="ea955-129">**Oracle**</span></span>
    *   <span data-ttu-id="ea955-130">ODP.NET 作为 [NuGet 包](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)提供</span><span class="sxs-lookup"><span data-stu-id="ea955-130">ODP.NET is available as a [NuGet package](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)</span></span>
*   <span data-ttu-id="ea955-131">**SQLite**</span><span class="sxs-lookup"><span data-stu-id="ea955-131">**SQLite**</span></span>
    *   <span data-ttu-id="ea955-132">System.Data.SQLite 以 [NuGet 包](https://www.nuget.org/packages/System.Data.SQLite/)的形式提供</span><span class="sxs-lookup"><span data-stu-id="ea955-132">System.Data.SQLite is available as a [NuGet package](https://www.nuget.org/packages/System.Data.SQLite/)</span></span>

<span data-ttu-id="ea955-133">请注意，此列表并不表示给定提供程序的功能级别或支持情况，只表示已构建了 EF6。</span><span class="sxs-lookup"><span data-stu-id="ea955-133">Note that inclusion in this list does not indicate the level of functionality or support for a given provider, only that a build for EF6 has been made available.</span></span>

## <a name="registering-ef-providers"></a><span data-ttu-id="ea955-134">注册 EF 提供程序</span><span class="sxs-lookup"><span data-stu-id="ea955-134">Registering EF providers</span></span>

<span data-ttu-id="ea955-135">从实体框架 6 开始，可以使用基于代码的配置或在应用程序的配置文件中注册 EF 提供程序。</span><span class="sxs-lookup"><span data-stu-id="ea955-135">Starting with Entity Framework 6 EF providers can be registered using either code-based configuration or in the application’s config file.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="ea955-136">配置文件注册</span><span class="sxs-lookup"><span data-stu-id="ea955-136">Config file registration</span></span>

<span data-ttu-id="ea955-137">在 app.config 或 web.config 中注册 EF 提供程序的格式如下：</span><span class="sxs-lookup"><span data-stu-id="ea955-137">Registration of the EF provider in app.config or web.config has the following format:</span></span>


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

<span data-ttu-id="ea955-138">请注意，如果从 NuGet 安装 EF 提供程序，则通常 NuGet 包会自动将此注册添加到配置文件中。</span><span class="sxs-lookup"><span data-stu-id="ea955-138">Note that often if the EF provider is installed from NuGet, then the NuGet package will automatically add this registration to the config file.</span></span> <span data-ttu-id="ea955-139">如果向非应用启动项目安装 NuGet 包，则可能需要将该注册复制到启动项目的配置文件中。</span><span class="sxs-lookup"><span data-stu-id="ea955-139">If you install the NuGet package into a project that is not the startup project for your app, then you may need to copy the registration into the config file for your startup project.</span></span>

<span data-ttu-id="ea955-140">此注册中的“invariantName”是用于标识 ADO.NET 提供程序的相同固定名称。</span><span class="sxs-lookup"><span data-stu-id="ea955-140">The “invariantName” in this registration is the same invariant name used to identify an ADO.NET provider.</span></span> <span data-ttu-id="ea955-141">它是 DbProviderFactories 注册中的“invariant”属性，是连接字符串注册中的“providerName”属性。</span><span class="sxs-lookup"><span data-stu-id="ea955-141">This can be found as the “invariant” attribute in a DbProviderFactories registration and as the “providerName” attribute in a connection string registration.</span></span> <span data-ttu-id="ea955-142">要使用的固定名称也应包含在该提供程序的文档中。</span><span class="sxs-lookup"><span data-stu-id="ea955-142">The invariant name to use should also be included in documentation for the provider.</span></span> <span data-ttu-id="ea955-143">固定名称的示例：SQL Server 为“System.Data.SqlClient”，SQL Server Compact 为“System.Data.SqlServerCe.4.0”。</span><span class="sxs-lookup"><span data-stu-id="ea955-143">Examples of invariant names are “System.Data.SqlClient” for SQL Server and “System.Data.SqlServerCe.4.0” for SQL Server Compact.</span></span>

<span data-ttu-id="ea955-144">此注册中的“类型”是派生自“System.Data.Entity.Core.Common.DbProviderServices”的提供程序类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="ea955-144">The “type” in this registration is the assembly-qualified name of the provider type that derives from “System.Data.Entity.Core.Common.DbProviderServices”.</span></span> <span data-ttu-id="ea955-145">例如，用于 SQL Compact 的字符串是“System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact”。</span><span class="sxs-lookup"><span data-stu-id="ea955-145">For example, the string to use for SQL Compact is “System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact”.</span></span> <span data-ttu-id="ea955-146">此处要使用的类型应包含在该提供程序的文档中。</span><span class="sxs-lookup"><span data-stu-id="ea955-146">The type to use here should be included in documentation for the provider.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="ea955-147">基于代码的注册</span><span class="sxs-lookup"><span data-stu-id="ea955-147">Code-based registration</span></span>

<span data-ttu-id="ea955-148">从实体框架 6 开始，可在代码中指定整个应用程序的 EF 配置。</span><span class="sxs-lookup"><span data-stu-id="ea955-148">Starting with Entity Framework 6 application-wide configuration for EF can be specified in code.</span></span> <span data-ttu-id="ea955-149">有关完整的详细信息，请参阅[实体框架基于代码的配置](https://msdn.microsoft.com/data/jj680699)__。</span><span class="sxs-lookup"><span data-stu-id="ea955-149">For full details see _[Entity Framework Code-Based Configuration](https://msdn.microsoft.com/data/jj680699)_.</span></span> <span data-ttu-id="ea955-150">使用基于代码的配置注册 EF 提供程序的常规方法是，创建一个派生自 System.Data.Entity.DbConfiguration 的新类，并将其放置在与 DbContext 类相同的程序集中。</span><span class="sxs-lookup"><span data-stu-id="ea955-150">The normal way to register an EF provider using code-based configuration is to create a new class that derives from System.Data.Entity.DbConfiguration and place it in the same assembly as your DbContext class.</span></span> <span data-ttu-id="ea955-151">然后，DbConfiguration 类应在其构造函数中注册该提供程序。</span><span class="sxs-lookup"><span data-stu-id="ea955-151">Your DbConfiguration class should then register the provider in its constructor.</span></span> <span data-ttu-id="ea955-152">例如，要注册 SQL Compact 提供程序，DbConfiguration 类如下所示：</span><span class="sxs-lookup"><span data-stu-id="ea955-152">For example, to register the SQL Compact provider the DbConfiguration class looks like this:</span></span>

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

<span data-ttu-id="ea955-153">在此代码中，“SqlCeProviderServices.ProviderInvariantName”为 SQL Server Compact 提供程序的固定名称字符串（“System.Data.SqlServerCe.4.0”）提供了便利，SqlCeProviderServices.Instance 会返回 SQL Compact EF 提供程序的单一实例。</span><span class="sxs-lookup"><span data-stu-id="ea955-153">In this code “SqlCeProviderServices.ProviderInvariantName” is a convenience for the SQL Server Compact provider invariant name string (“System.Data.SqlServerCe.4.0”) and SqlCeProviderServices.Instance returns the singleton instance of the SQL Compact EF provider.</span></span>

## <a name="what-if-the-provider-i-need-isnt-available"></a><span data-ttu-id="ea955-154">如果未提供我需要的提供程序怎么办？</span><span class="sxs-lookup"><span data-stu-id="ea955-154">What if the provider I need isn’t available?</span></span>

<span data-ttu-id="ea955-155">如果该提供程序适用于以前版本的 EF，则建议联系提供程序的所有者并要求他们创建 EF6 版本。</span><span class="sxs-lookup"><span data-stu-id="ea955-155">If the provider is available for previous versions of EF, then we encourage you to contact the owner of the provider and ask them to create an EF6 version.</span></span> <span data-ttu-id="ea955-156">应包含对 [EF6 提供程序模型的文档](xref:ef6/fundamentals/providers/provider-model)的引用。</span><span class="sxs-lookup"><span data-stu-id="ea955-156">You should include a reference to the [documentation for the EF6 provider model](xref:ef6/fundamentals/providers/provider-model).</span></span>

## <a name="can-i-write-a-provider-myself"></a><span data-ttu-id="ea955-157">可以自己编写提供程序吗？</span><span class="sxs-lookup"><span data-stu-id="ea955-157">Can I write a provider myself?</span></span>

<span data-ttu-id="ea955-158">当然可以自己创建 EF 提供程序，但不应把这项任务考虑的太简单。</span><span class="sxs-lookup"><span data-stu-id="ea955-158">It is certainly possible to create an EF provider yourself although it should not be considered a trivial undertaking.</span></span> <span data-ttu-id="ea955-159">可以从上面关于 EF6 提供程序模型的链接开始着手。</span><span class="sxs-lookup"><span data-stu-id="ea955-159">The the link above about the EF6 provider model is a good place to start.</span></span> <span data-ttu-id="ea955-160">你可能还会发现，从 [EF 开源代码库](https://github.com/aspnet/EntityFramework6)中包含的 SQL Server 和 SQL CE 提供程序的代码着手或用作参考会很有用。</span><span class="sxs-lookup"><span data-stu-id="ea955-160">You may also find it useful to use the code for the SQL Server and SQL CE provider included in the [EF open source codebase](https://github.com/aspnet/EntityFramework6) as a starting point or for reference.</span></span>

<span data-ttu-id="ea955-161">请注意，从 EF6 开始，EF 提供程序与基础 ADO.NET 提供程序的耦合紧密程度降低。</span><span class="sxs-lookup"><span data-stu-id="ea955-161">Note that starting with EF6 the EF provider is less tightly coupled to the underlying ADO.NET provider.</span></span> <span data-ttu-id="ea955-162">这使得编写 EF 提供程序更轻松，且无需编写或包装 ADO.NET 类。</span><span class="sxs-lookup"><span data-stu-id="ea955-162">This makes it easier to write an EF provider without needing to write or wrap the ADO.NET classes.</span></span>
