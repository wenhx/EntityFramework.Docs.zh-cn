---
title: 配置文件设置-EF6
description: 实体框架6中的配置文件设置
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: fc991810e93840c27e6631dfb5bc1796c1328d37
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063317"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="6d474-103">配置文件设置</span><span class="sxs-lookup"><span data-stu-id="6d474-103">Configuration File Settings</span></span>
<span data-ttu-id="6d474-104">实体框架允许在配置文件中指定多个设置。</span><span class="sxs-lookup"><span data-stu-id="6d474-104">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="6d474-105">在常规 EF 中，遵循 "约定 over 配置" 原则：在此文章中讨论的所有设置都有默认行为，你只需考虑在默认不再满足你的要求时更改设置。</span><span class="sxs-lookup"><span data-stu-id="6d474-105">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="6d474-106">Code-Based 替代方法</span><span class="sxs-lookup"><span data-stu-id="6d474-106">A Code-Based Alternative</span></span>  

<span data-ttu-id="6d474-107">还可以使用代码应用所有这些设置。</span><span class="sxs-lookup"><span data-stu-id="6d474-107">All of these settings can also be applied using code.</span></span> <span data-ttu-id="6d474-108">从 EF6 开始，我们引入了 [基于代码的配置](xref:ef6/fundamentals/configuring/code-based)，它提供了一种从代码应用配置的集中方式。</span><span class="sxs-lookup"><span data-stu-id="6d474-108">Starting in EF6 we introduced [code-based configuration](xref:ef6/fundamentals/configuring/code-based), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="6d474-109">在 EF6 之前，仍可以从代码应用配置，但需要使用各种 Api 来配置不同的区域。</span><span class="sxs-lookup"><span data-stu-id="6d474-109">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="6d474-110">配置文件选项允许在部署期间轻松地更改这些设置，而无需更新代码。</span><span class="sxs-lookup"><span data-stu-id="6d474-110">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="6d474-111">"实体框架配置" 部分</span><span class="sxs-lookup"><span data-stu-id="6d474-111">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="6d474-112">从 EF 4.1 开始，你可以使用配置文件的 **appSettings** 节为上下文设置数据库初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="6d474-112">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="6d474-113">在 EF 4.3 中，我们引入了自定义 **entityFramework** 部分来处理新设置。</span><span class="sxs-lookup"><span data-stu-id="6d474-113">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="6d474-114">实体框架仍将识别使用旧格式设置的数据库初始值设定项，但建议在可能的情况下移动到新的格式。</span><span class="sxs-lookup"><span data-stu-id="6d474-114">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="6d474-115">安装 EntityFramework NuGet 包时， **entityFramework** 节会自动添加到项目的配置文件中。</span><span class="sxs-lookup"><span data-stu-id="6d474-115">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

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

## <a name="connection-strings"></a><span data-ttu-id="6d474-116">连接字符串</span><span class="sxs-lookup"><span data-stu-id="6d474-116">Connection Strings</span></span>  

<span data-ttu-id="6d474-117">[本页提供了](xref:ef6/fundamentals/configuring/connection-strings) 有关实体框架如何确定要使用的数据库的更多详细信息，包括配置文件中的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6d474-117">[This page](xref:ef6/fundamentals/configuring/connection-strings) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="6d474-118">连接字符串位于标准 **connectionStrings** 元素中，不需要 **entityFramework** 部分。</span><span class="sxs-lookup"><span data-stu-id="6d474-118">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="6d474-119">基于 Code First 的模型使用常规 ADO.NET 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6d474-119">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="6d474-120">例如：</span><span class="sxs-lookup"><span data-stu-id="6d474-120">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="6d474-121">基于 EF 设计器的模型使用特殊 EF 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6d474-121">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="6d474-122">例如：</span><span class="sxs-lookup"><span data-stu-id="6d474-122">For example:</span></span>  

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

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="6d474-123"> (EF6 的 Code-Based 配置类型) </span><span class="sxs-lookup"><span data-stu-id="6d474-123">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="6d474-124">从 EF6 开始，可以指定 EF 的 DbConfiguration，以便在应用程序中使用 [基于代码的配置](xref:ef6/fundamentals/configuring/code-based) 。</span><span class="sxs-lookup"><span data-stu-id="6d474-124">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](xref:ef6/fundamentals/configuring/code-based) in your application.</span></span> <span data-ttu-id="6d474-125">在大多数情况下，无需指定此设置，因为 EF 会自动发现你的 DbConfiguration。</span><span class="sxs-lookup"><span data-stu-id="6d474-125">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="6d474-126">有关可能需要在配置文件中指定 DbConfiguration 的详细信息，请参阅[基于代码的配置](xref:ef6/fundamentals/configuring/code-based)的**移动 DbConfiguration**部分。</span><span class="sxs-lookup"><span data-stu-id="6d474-126">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span>  

<span data-ttu-id="6d474-127">若要设置 DbConfiguration 类型，请在 **codeConfigurationType** 元素中指定程序集限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="6d474-127">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="6d474-128">程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="6d474-128">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="6d474-129">还可以选择指定程序集版本、区域性和公钥标记。</span><span class="sxs-lookup"><span data-stu-id="6d474-129">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="6d474-130">EF 数据库提供程序 (EF6 向前) </span><span class="sxs-lookup"><span data-stu-id="6d474-130">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="6d474-131">在 EF6 之前，数据库提供程序实体框架特定部分必须作为核心 ADO.NET 提供程序的一部分包括在内。</span><span class="sxs-lookup"><span data-stu-id="6d474-131">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="6d474-132">从 EF6 开始，EF 特定部分现在被管理和注册。</span><span class="sxs-lookup"><span data-stu-id="6d474-132">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="6d474-133">通常无需自行注册提供程序。</span><span class="sxs-lookup"><span data-stu-id="6d474-133">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="6d474-134">此操作通常会在安装时由提供程序完成。</span><span class="sxs-lookup"><span data-stu-id="6d474-134">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="6d474-135">提供程序是通过在**entityFramework** **节的 provider 子节**下包含**提供程序**元素来注册的。</span><span class="sxs-lookup"><span data-stu-id="6d474-135">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="6d474-136">提供程序条目有两个必需的属性：</span><span class="sxs-lookup"><span data-stu-id="6d474-136">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="6d474-137">**invariantName** 标识此 EF 提供程序针对的核心 ADO.NET 提供程序</span><span class="sxs-lookup"><span data-stu-id="6d474-137">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="6d474-138">**类型** 是 EF 提供程序实现的程序集限定类型名称</span><span class="sxs-lookup"><span data-stu-id="6d474-138">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="6d474-139">程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="6d474-139">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="6d474-140">还可以选择指定程序集版本、区域性和公钥标记。</span><span class="sxs-lookup"><span data-stu-id="6d474-140">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="6d474-141">例如，下面是在安装实体框架时创建的用于注册默认 SQL Server 提供程序的条目。</span><span class="sxs-lookup"><span data-stu-id="6d474-141">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="6d474-142">拦截 (EF 6.1) </span><span class="sxs-lookup"><span data-stu-id="6d474-142">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="6d474-143">从 EF 6.1 开始，可以在配置文件中注册侦听器。</span><span class="sxs-lookup"><span data-stu-id="6d474-143">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="6d474-144">当 EF 执行某些操作（例如执行数据库查询、打开连接等）时，拦截允许您运行其他逻辑。</span><span class="sxs-lookup"><span data-stu-id="6d474-144">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="6d474-145">通过在**entityFramework**部分的**拦截**程序子部分下包含一个**侦听器**元素来注册拦截程序。</span><span class="sxs-lookup"><span data-stu-id="6d474-145">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="6d474-146">例如，下面的配置将注册将所有数据库操作记录到控制台的内置 **DatabaseLogger** 侦听器。</span><span class="sxs-lookup"><span data-stu-id="6d474-146">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="6d474-147">将数据库操作记录到文件 (EF 6.1 之前) </span><span class="sxs-lookup"><span data-stu-id="6d474-147">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="6d474-148">如果要将日志记录添加到现有的应用程序以帮助调试问题，则通过配置文件注册侦听器特别有用。</span><span class="sxs-lookup"><span data-stu-id="6d474-148">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="6d474-149">**DatabaseLogger** 支持通过将文件名作为构造函数参数提供来记录文件。</span><span class="sxs-lookup"><span data-stu-id="6d474-149">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="6d474-150">默认情况下，每次启动应用程序时，将会使用新文件覆盖日志文件。</span><span class="sxs-lookup"><span data-stu-id="6d474-150">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="6d474-151">如果日志文件已存在，则改为将其追加到日志文件中，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6d474-151">To instead append to the log file if it already exists use something like:</span></span>  

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

<span data-ttu-id="6d474-152">有关 **DatabaseLogger** 和注册侦听器的其他信息，请参阅博客文章 [EF 6.1：在不重新编译的情况下启用日志记录](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。</span><span class="sxs-lookup"><span data-stu-id="6d474-152">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="6d474-153">Code First 默认连接工厂</span><span class="sxs-lookup"><span data-stu-id="6d474-153">Code First Default Connection Factory</span></span>  

<span data-ttu-id="6d474-154">"配置" 部分允许你指定 Code First 应用于查找要用于上下文的数据库的默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="6d474-154">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="6d474-155">仅当未将连接字符串添加到上下文的配置文件时，才使用默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="6d474-155">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="6d474-156">当你安装 EF NuGet 包时，将根据你安装的是 SQL Express 或 LocalDB 来注册一个默认连接工厂。</span><span class="sxs-lookup"><span data-stu-id="6d474-156">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="6d474-157">若要设置连接工厂，请在 **defaultConnectionFactory** 元素中指定程序集限定的类型名称。</span><span class="sxs-lookup"><span data-stu-id="6d474-157">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="6d474-158">程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="6d474-158">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="6d474-159">还可以选择指定程序集版本、区域性和公钥标记。</span><span class="sxs-lookup"><span data-stu-id="6d474-159">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="6d474-160">下面是设置自己的默认连接工厂的示例：</span><span class="sxs-lookup"><span data-stu-id="6d474-160">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="6d474-161">上面的示例要求自定义工厂具有无参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="6d474-161">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="6d474-162">如果需要，可以使用 **parameters** 元素指定构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="6d474-162">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="6d474-163">例如，SqlCeConnectionFactory 包含在实体框架中，需要你向构造函数提供提供程序固定名称。</span><span class="sxs-lookup"><span data-stu-id="6d474-163">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="6d474-164">提供程序固定名称用于标识要使用的 SQL Compact 的版本。</span><span class="sxs-lookup"><span data-stu-id="6d474-164">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="6d474-165">默认情况下，下面的配置将导致上下文使用 SQL Compact 4.0 版。</span><span class="sxs-lookup"><span data-stu-id="6d474-165">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="6d474-166">如果未设置默认的连接工厂，Code First 将使用指向的 SqlConnectionFactory `.\SQLEXPRESS` 。</span><span class="sxs-lookup"><span data-stu-id="6d474-166">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="6d474-167">SqlConnectionFactory 还具有一个构造函数，该构造函数允许你重写连接字符串的各个部分。</span><span class="sxs-lookup"><span data-stu-id="6d474-167">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="6d474-168">如果要使用以外的 SQL Server 实例， `.\SQLEXPRESS` 则可以使用此构造函数来设置服务器。</span><span class="sxs-lookup"><span data-stu-id="6d474-168">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="6d474-169">以下配置将导致 Code First 为未设置显式连接字符串的上下文使用 **MyDatabaseServer** 。</span><span class="sxs-lookup"><span data-stu-id="6d474-169">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="6d474-170">默认情况下，假定构造函数参数的类型为字符串。</span><span class="sxs-lookup"><span data-stu-id="6d474-170">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="6d474-171">您可以使用 type 特性来更改此。</span><span class="sxs-lookup"><span data-stu-id="6d474-171">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="6d474-172">数据库初始值设定项</span><span class="sxs-lookup"><span data-stu-id="6d474-172">Database Initializers</span></span>  

<span data-ttu-id="6d474-173">数据库初始值设定项在每个上下文基础上进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d474-173">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="6d474-174">可以使用 **上下文** 元素在配置文件中设置它们。</span><span class="sxs-lookup"><span data-stu-id="6d474-174">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="6d474-175">此元素使用程序集限定名称标识要配置的上下文。</span><span class="sxs-lookup"><span data-stu-id="6d474-175">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="6d474-176">默认情况下，Code First 上下文配置为使用 CreateDatabaseIfNotExists 初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="6d474-176">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="6d474-177">**上下文**元素上有一个可用于禁用数据库初始化的**disableDatabaseInitialization**属性。</span><span class="sxs-lookup"><span data-stu-id="6d474-177">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="6d474-178">例如，下面的配置禁用 MyAssembly.dll 中定义的 BlogContext 上下文的数据库初始化。</span><span class="sxs-lookup"><span data-stu-id="6d474-178">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="6d474-179">可以使用 **databaseInitializer** 元素设置自定义初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="6d474-179">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="6d474-180">构造函数参数与默认连接工厂使用相同的语法。</span><span class="sxs-lookup"><span data-stu-id="6d474-180">Constructor parameters use the same syntax as default connection factories.</span></span>  

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

<span data-ttu-id="6d474-181">可以配置实体框架中包含的其中一个泛型数据库初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="6d474-181">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="6d474-182">**Type**特性使用泛型类型的 .NET Framework 格式。</span><span class="sxs-lookup"><span data-stu-id="6d474-182">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="6d474-183">例如，如果使用 Code First 迁移，则可以将数据库配置为使用 `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` 初始值设定项自动迁移。</span><span class="sxs-lookup"><span data-stu-id="6d474-183">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
