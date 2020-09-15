---
title: 配置文件设置-EF6
description: 实体框架6中的配置文件设置
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: b407ae7d64df18f6a8d80aa13703a8c243c6f3a2
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070713"
---
# <a name="configuration-file-settings"></a>配置文件设置
实体框架允许在配置文件中指定多个设置。 在常规 EF 中，遵循 "约定 over 配置" 原则：在此文章中讨论的所有设置都有默认行为，你只需考虑在默认不再满足你的要求时更改设置。  

## <a name="a-code-based-alternative"></a>基于代码的替代项  

还可以使用代码应用所有这些设置。 从 EF6 开始，我们引入了 [基于代码的配置](xref:ef6/fundamentals/configuring/code-based)，它提供了一种从代码应用配置的集中方式。 在 EF6 之前，仍可以从代码应用配置，但需要使用各种 Api 来配置不同的区域。 配置文件选项允许在部署期间轻松地更改这些设置，而无需更新代码。

## <a name="the-entity-framework-configuration-section"></a>"实体框架配置" 部分  

从 EF 4.1 开始，你可以使用配置文件的 **appSettings** 节为上下文设置数据库初始值设定项。 在 EF 4.3 中，我们引入了自定义 **entityFramework** 部分来处理新设置。 实体框架仍将识别使用旧格式设置的数据库初始值设定项，但建议在可能的情况下移动到新的格式。

安装 EntityFramework NuGet 包时， **entityFramework** 节会自动添加到项目的配置文件中。  

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

## <a name="connection-strings"></a>连接字符串  

[本页提供了](xref:ef6/fundamentals/configuring/connection-strings) 有关实体框架如何确定要使用的数据库的更多详细信息，包括配置文件中的连接字符串。  

连接字符串位于标准 **connectionStrings** 元素中，不需要 **entityFramework** 部分。  

基于 Code First 的模型使用常规 ADO.NET 连接字符串。 例如：  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

基于 EF 设计器的模型使用特殊 EF 连接字符串。 例如：  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>基于代码的配置类型 (EF6 向前)   

从 EF6 开始，可以指定 EF 的 DbConfiguration，以便在应用程序中使用 [基于代码的配置](xref:ef6/fundamentals/configuring/code-based) 。 在大多数情况下，无需指定此设置，因为 EF 会自动发现你的 DbConfiguration。 有关可能需要在配置文件中指定 DbConfiguration 的详细信息，请参阅[基于代码的配置](xref:ef6/fundamentals/configuring/code-based)的**移动 DbConfiguration**部分。  

若要设置 DbConfiguration 类型，请在 **codeConfigurationType** 元素中指定程序集限定的类型名称。  

> [!NOTE]
> 程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。 还可以选择指定程序集版本、区域性和公钥标记。  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>EF 数据库提供程序 (EF6 向前)   

在 EF6 之前，数据库提供程序实体框架特定部分必须作为核心 ADO.NET 提供程序的一部分包括在内。 从 EF6 开始，EF 特定部分现在被管理和注册。  

通常无需自行注册提供程序。 此操作通常会在安装时由提供程序完成。  

提供程序是通过在**entityFramework** **节的 provider 子节**下包含**提供程序**元素来注册的。 提供程序条目有两个必需的属性：  

- **invariantName** 标识此 EF 提供程序针对的核心 ADO.NET 提供程序  
- **类型** 是 EF 提供程序实现的程序集限定类型名称  

> [!NOTE]
> 程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。 还可以选择指定程序集版本、区域性和公钥标记。  

例如，下面是在安装实体框架时创建的用于注册默认 SQL Server 提供程序的条目。  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>拦截 (EF 6.1)   

从 EF 6.1 开始，可以在配置文件中注册侦听器。 当 EF 执行某些操作（例如执行数据库查询、打开连接等）时，拦截允许您运行其他逻辑。  

通过在**entityFramework**部分的**拦截**程序子部分下包含一个**侦听器**元素来注册拦截程序。 例如，下面的配置将注册将所有数据库操作记录到控制台的内置 **DatabaseLogger** 侦听器。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>将数据库操作记录到文件 (EF 6.1 之前)   

如果要将日志记录添加到现有的应用程序以帮助调试问题，则通过配置文件注册侦听器特别有用。 **DatabaseLogger** 支持通过将文件名作为构造函数参数提供来记录文件。  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

默认情况下，每次启动应用程序时，将会使用新文件覆盖日志文件。 如果日志文件已存在，则改为将其追加到日志文件中，如下所示：  

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

有关 **DatabaseLogger** 和注册侦听器的其他信息，请参阅博客文章 [EF 6.1：在不重新编译的情况下启用日志记录](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)。  

## <a name="code-first-default-connection-factory"></a>Code First 默认连接工厂  

"配置" 部分允许你指定 Code First 应用于查找要用于上下文的数据库的默认连接工厂。 仅当未将连接字符串添加到上下文的配置文件时，才使用默认连接工厂。  

当你安装 EF NuGet 包时，将根据你安装的是 SQL Express 或 LocalDB 来注册一个默认连接工厂。  

若要设置连接工厂，请在 **defaultConnectionFactory** 元素中指定程序集限定的类型名称。  

> [!NOTE]
> 程序集限定名称是命名空间限定名称，后跟一个逗号，然后是该类型所在的程序集。 还可以选择指定程序集版本、区域性和公钥标记。  

下面是设置自己的默认连接工厂的示例：  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

上面的示例要求自定义工厂具有无参数的构造函数。 如果需要，可以使用 **parameters** 元素指定构造函数参数。  

例如，SqlCeConnectionFactory 包含在实体框架中，需要你向构造函数提供提供程序固定名称。 提供程序固定名称用于标识要使用的 SQL Compact 的版本。 默认情况下，下面的配置将导致上下文使用 SQL Compact 4.0 版。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

如果未设置默认的连接工厂，Code First 将使用指向的 SqlConnectionFactory `.\SQLEXPRESS` 。 SqlConnectionFactory 还具有一个构造函数，该构造函数允许你重写连接字符串的各个部分。 如果要使用以外的 SQL Server 实例， `.\SQLEXPRESS` 则可以使用此构造函数来设置服务器。  

以下配置将导致 Code First 为未设置显式连接字符串的上下文使用 **MyDatabaseServer** 。  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

默认情况下，假定构造函数参数的类型为字符串。 您可以使用 type 特性来更改此。  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>数据库初始值设定项  

数据库初始值设定项在每个上下文基础上进行配置。 可以使用 **上下文** 元素在配置文件中设置它们。 此元素使用程序集限定名称标识要配置的上下文。  

默认情况下，Code First 上下文配置为使用 CreateDatabaseIfNotExists 初始值设定项。 **上下文**元素上有一个可用于禁用数据库初始化的**disableDatabaseInitialization**属性。  

例如，下面的配置禁用 MyAssembly.dll 中定义的 BlogContext 上下文的数据库初始化。  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

可以使用 **databaseInitializer** 元素设置自定义初始值设定项。  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

构造函数参数与默认连接工厂使用相同的语法。  

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

可以配置实体框架中包含的其中一个泛型数据库初始值设定项。 **Type**特性使用泛型类型的 .NET Framework 格式。  

例如，如果使用 Code First 迁移，则可以将数据库配置为使用 `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` 初始值设定项自动迁移。  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
