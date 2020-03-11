---
title: 配置文件设置-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 000044c6-1d32-4cf7-ae1f-ea21d86ebf8f
ms.openlocfilehash: 86389e4a3a3bac46e2a4cf2da648a4b19e29f3c3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414853"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="dc72a-102">配置文件设置</span><span class="sxs-lookup"><span data-stu-id="dc72a-102">Configuration File Settings</span></span>
<span data-ttu-id="dc72a-103">实体框架允许在配置文件中指定多个设置。</span><span class="sxs-lookup"><span data-stu-id="dc72a-103">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="dc72a-104">在常规 EF 中，遵循 "约定 over 配置" 原则：在此文章中讨论的所有设置都有默认行为，你只需考虑在默认不再满足你的要求时更改设置。</span><span class="sxs-lookup"><span data-stu-id="dc72a-104">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="dc72a-105">基于代码的替代项</span><span class="sxs-lookup"><span data-stu-id="dc72a-105">A Code-Based Alternative</span></span>  

<span data-ttu-id="dc72a-106">还可以使用代码应用所有这些设置。</span><span class="sxs-lookup"><span data-stu-id="dc72a-106">All of these settings can also be applied using code.</span></span> <span data-ttu-id="dc72a-107">从 EF6 开始，我们引入了[基于代码的配置](code-based.md)，它提供了一种从代码应用配置的集中方式。</span><span class="sxs-lookup"><span data-stu-id="dc72a-107">Starting in EF6 we introduced [code-based configuration](code-based.md), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="dc72a-108">在 EF6 之前，仍可以从代码应用配置，但需要使用各种 Api 来配置不同的区域。</span><span class="sxs-lookup"><span data-stu-id="dc72a-108">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="dc72a-109">配置文件选项允许在部署期间轻松地更改这些设置，而无需更新代码。</span><span class="sxs-lookup"><span data-stu-id="dc72a-109">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="dc72a-110">"实体框架配置" 部分</span><span class="sxs-lookup"><span data-stu-id="dc72a-110">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="dc72a-111">从 EF 4.1 开始，你可以使用配置文件的**appSettings**节为上下文设置数据库初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="dc72a-111">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="dc72a-112">在 EF 4.3 中，我们引入了自定义**entityFramework**部分来处理新设置。</span><span class="sxs-lookup"><span data-stu-id="dc72a-112">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="dc72a-113">实体框架仍将识别使用旧格式设置的数据库初始值设定项，但建议在可能的情况下移动到新的格式。</span><span class="sxs-lookup"><span data-stu-id="dc72a-113">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="dc72a-114">安装 EntityFramework NuGet 包时， **entityFramework**节会自动添加到项目的配置文件中。</span><span class="sxs-lookup"><span data-stu-id="dc72a-114">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="dc72a-115">连接字符串</span><span class="sxs-lookup"><span data-stu-id="dc72a-115">Connection Strings</span></span>  

<span data-ttu-id="dc72a-116">[本页提供了](~/ef6/fundamentals/configuring/connection-strings.md)有关实体框架如何确定要使用的数据库的更多详细信息，包括配置文件中的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="dc72a-116">[This page](~/ef6/fundamentals/configuring/connection-strings.md) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="dc72a-117">连接字符串位于标准**connectionStrings**元素中，不需要**entityFramework**部分。</span><span class="sxs-lookup"><span data-stu-id="dc72a-117">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="dc72a-118">基于 Code First 的模型使用常规 ADO.NET 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="dc72a-118">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="dc72a-119">例如：</span><span class="sxs-lookup"><span data-stu-id="dc72a-119">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="dc72a-120">基于 EF 设计器的模型使用特殊 EF 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="dc72a-120">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="dc72a-121">例如：</span><span class="sxs-lookup"><span data-stu-id="dc72a-121">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="dc72a-122">基于代码的配置类型（EF6）</span><span class="sxs-lookup"><span data-stu-id="dc72a-122">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="dc72a-123">从 EF6 开始，可以指定 EF 的 DbConfiguration，以便在应用程序中使用[基于代码的配置](code-based.md)。</span><span class="sxs-lookup"><span data-stu-id="dc72a-123">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](code-based.md) in your application.</span></span> <span data-ttu-id="dc72a-124">在大多数情况下，无需指定此设置，因为 EF 会自动发现你的 DbConfiguration。</span><span class="sxs-lookup"><span data-stu-id="dc72a-124">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="dc72a-125">有关可能需要在配置文件中指定 DbConfiguration 的详细信息，请参阅[基于代码的配置](code-based.md)的**移动 DbConfiguration**部分。</span><span class="sxs-lookup"><span data-stu-id="dc72a-125">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](code-based.md).</span></span>  

<span data-ttu-id="dc72a-126">若要设置 DbConfiguration 类型，请在**codeConfigurationType**元素中指定程序集限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="dc72a-126">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="dc72a-127">程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="dc72a-127">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="dc72a-128">还可以选择指定程序集版本、区域性和公钥标记。</span><span class="sxs-lookup"><span data-stu-id="dc72a-128">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="dc72a-129">EF 数据库提供程序（EF6）</span><span class="sxs-lookup"><span data-stu-id="dc72a-129">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="dc72a-130">在 EF6 之前，数据库提供程序实体框架特定部分必须作为核心 ADO.NET 提供程序的一部分包括在内。</span><span class="sxs-lookup"><span data-stu-id="dc72a-130">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="dc72a-131">从 EF6 开始，EF 特定部分现在被管理和注册。</span><span class="sxs-lookup"><span data-stu-id="dc72a-131">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="dc72a-132">通常无需自行注册提供程序。</span><span class="sxs-lookup"><span data-stu-id="dc72a-132">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="dc72a-133">此操作通常会在安装时由提供程序完成。</span><span class="sxs-lookup"><span data-stu-id="dc72a-133">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="dc72a-134">提供程序是通过在**entityFramework** **节的 provider 子节**下包含**提供程序**元素来注册的。</span><span class="sxs-lookup"><span data-stu-id="dc72a-134">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="dc72a-135">提供程序条目有两个必需的属性：</span><span class="sxs-lookup"><span data-stu-id="dc72a-135">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="dc72a-136">**invariantName**标识此 EF 提供程序针对的核心 ADO.NET 提供程序</span><span class="sxs-lookup"><span data-stu-id="dc72a-136">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="dc72a-137">**类型**是 EF 提供程序实现的程序集限定类型名称</span><span class="sxs-lookup"><span data-stu-id="dc72a-137">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="dc72a-138">程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="dc72a-138">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="dc72a-139">还可以选择指定程序集版本、区域性和公钥标记。</span><span class="sxs-lookup"><span data-stu-id="dc72a-139">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="dc72a-140">例如，下面是在安装实体框架时创建的用于注册默认 SQL Server 提供程序的条目。</span><span class="sxs-lookup"><span data-stu-id="dc72a-140">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="dc72a-141">拦截（EF 6.1 以上版本）</span><span class="sxs-lookup"><span data-stu-id="dc72a-141">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="dc72a-142">从 EF 6.1 开始，可以在配置文件中注册侦听器。</span><span class="sxs-lookup"><span data-stu-id="dc72a-142">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="dc72a-143">当 EF 执行某些操作（例如执行数据库查询、打开连接等）时，拦截允许您运行其他逻辑。</span><span class="sxs-lookup"><span data-stu-id="dc72a-143">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="dc72a-144">通过在**entityFramework**部分的**拦截**程序子部分下包含一个**侦听器**元素来注册拦截程序。</span><span class="sxs-lookup"><span data-stu-id="dc72a-144">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="dc72a-145">例如，下面的配置将注册将所有数据库操作记录到控制台的内置**DatabaseLogger**侦听器。</span><span class="sxs-lookup"><span data-stu-id="dc72a-145">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="dc72a-146">将数据库操作记录到文件中（EF 6.1 以上版本）</span><span class="sxs-lookup"><span data-stu-id="dc72a-146">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="dc72a-147">如果要将日志记录添加到现有的应用程序以帮助调试问题，则通过配置文件注册侦听器特别有用。</span><span class="sxs-lookup"><span data-stu-id="dc72a-147">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="dc72a-148">**DatabaseLogger**支持通过将文件名作为构造函数参数提供来记录文件。</span><span class="sxs-lookup"><span data-stu-id="dc72a-148">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="dc72a-149">默认情况下，每次启动应用程序时，将会使用新文件覆盖日志文件。</span><span class="sxs-lookup"><span data-stu-id="dc72a-149">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="dc72a-150">如果日志文件已存在，则改为将其追加到日志文件中，如下所示：</span><span class="sxs-lookup"><span data-stu-id="dc72a-150">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="dc72a-151">有关**DatabaseLogger**和注册侦听器的其他信息，请参阅博客文章[EF 6.1：在不重新编译的情况下启用日志记录](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。</span><span class="sxs-lookup"><span data-stu-id="dc72a-151">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="dc72a-152">Code First 默认连接工厂</span><span class="sxs-lookup"><span data-stu-id="dc72a-152">Code First Default Connection Factory</span></span>  

<span data-ttu-id="dc72a-153">"配置" 部分允许你指定 Code First 应用于查找要用于上下文的数据库的默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="dc72a-153">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="dc72a-154">仅当未将连接字符串添加到上下文的配置文件时，才使用默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="dc72a-154">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="dc72a-155">当你安装 EF NuGet 包时，将根据你安装的是 SQL Express 或 LocalDB 来注册一个默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="dc72a-155">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="dc72a-156">若要设置连接工厂，请在**defaultConnectionFactory**元素中指定程序集限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="dc72a-156">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="dc72a-157">程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="dc72a-157">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="dc72a-158">还可以选择指定程序集版本、区域性和公钥标记。</span><span class="sxs-lookup"><span data-stu-id="dc72a-158">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="dc72a-159">下面是设置自己的默认连接工厂的示例：</span><span class="sxs-lookup"><span data-stu-id="dc72a-159">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="dc72a-160">上面的示例要求自定义工厂具有无参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="dc72a-160">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="dc72a-161">如果需要，可以使用**parameters**元素指定构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="dc72a-161">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="dc72a-162">例如，SqlCeConnectionFactory 包含在实体框架中，需要你向构造函数提供提供程序固定名称。</span><span class="sxs-lookup"><span data-stu-id="dc72a-162">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="dc72a-163">提供程序固定名称用于标识要使用的 SQL Compact 的版本。</span><span class="sxs-lookup"><span data-stu-id="dc72a-163">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="dc72a-164">默认情况下，下面的配置将导致上下文使用 SQL Compact 4.0 版。</span><span class="sxs-lookup"><span data-stu-id="dc72a-164">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="dc72a-165">如果未设置默认的连接工厂，则 Code First 使用 SqlConnectionFactory，指向 `.\SQLEXPRESS`。</span><span class="sxs-lookup"><span data-stu-id="dc72a-165">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="dc72a-166">SqlConnectionFactory 还具有一个构造函数，该构造函数允许你重写连接字符串的各个部分。</span><span class="sxs-lookup"><span data-stu-id="dc72a-166">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="dc72a-167">如果要使用 `.\SQLEXPRESS` 之外的 SQL Server 实例，则可以使用此构造函数来设置服务器。</span><span class="sxs-lookup"><span data-stu-id="dc72a-167">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="dc72a-168">以下配置将导致 Code First 为未设置显式连接字符串的上下文使用**MyDatabaseServer** 。</span><span class="sxs-lookup"><span data-stu-id="dc72a-168">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="dc72a-169">默认情况下，假定构造函数参数的类型为字符串。</span><span class="sxs-lookup"><span data-stu-id="dc72a-169">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="dc72a-170">您可以使用 type 特性来更改此。</span><span class="sxs-lookup"><span data-stu-id="dc72a-170">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="dc72a-171">数据库初始值设定项</span><span class="sxs-lookup"><span data-stu-id="dc72a-171">Database Initializers</span></span>  

<span data-ttu-id="dc72a-172">数据库初始值设定项在每个上下文基础上进行配置。</span><span class="sxs-lookup"><span data-stu-id="dc72a-172">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="dc72a-173">可以使用**上下文**元素在配置文件中设置它们。</span><span class="sxs-lookup"><span data-stu-id="dc72a-173">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="dc72a-174">此元素使用程序集限定名称标识要配置的上下文。</span><span class="sxs-lookup"><span data-stu-id="dc72a-174">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="dc72a-175">默认情况下，Code First 上下文配置为使用 CreateDatabaseIfNotExists 初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="dc72a-175">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="dc72a-176">**上下文**元素上有一个可用于禁用数据库初始化的**disableDatabaseInitialization**属性。</span><span class="sxs-lookup"><span data-stu-id="dc72a-176">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="dc72a-177">例如，下面的配置将禁用在 MyAssembly 中定义的 BlogContext 上下文的数据库初始化。</span><span class="sxs-lookup"><span data-stu-id="dc72a-177">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="dc72a-178">可以使用**databaseInitializer**元素设置自定义初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="dc72a-178">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="dc72a-179">构造函数参数与默认连接工厂使用相同的语法。</span><span class="sxs-lookup"><span data-stu-id="dc72a-179">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="dc72a-180">可以配置实体框架中包含的其中一个泛型数据库初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="dc72a-180">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="dc72a-181">**Type**特性使用泛型类型的 .NET Framework 格式。</span><span class="sxs-lookup"><span data-stu-id="dc72a-181">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="dc72a-182">例如，如果使用 Code First 迁移，则可以将数据库配置为使用 `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` 初始值设定项自动迁移。</span><span class="sxs-lookup"><span data-stu-id="dc72a-182">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
