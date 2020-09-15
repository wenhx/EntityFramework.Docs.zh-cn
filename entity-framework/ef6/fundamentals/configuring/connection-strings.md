---
title: 连接字符串和模型-EF6
description: 实体框架6中的连接字符串和模型
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: 45db461b18cde3bc1f1fccadec3c8ece6dd16832
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070778"
---
# <a name="connection-strings-and-models"></a>连接字符串和模型
本主题介绍实体框架如何发现要使用的数据库连接，以及如何对其进行更改。 本主题介绍了 Code First 和 EF 设计器创建的模型。  

通常实体框架的应用程序使用派生自 DbContext 的类。 此派生类将调用基 DbContext 类上的构造函数之一来控制：  

- 上下文将如何连接到数据库-即，如何查找或使用连接字符串  
- 上下文是使用 Code First 计算模型还是加载使用 EF 设计器创建的模型  
- 其他高级选项  

以下片段显示了可使用 DbContext 构造函数的一些方式。  

## <a name="use-code-first-with-connection-by-convention"></a>将 Code First 与按约定连接  

如果你未在应用程序中进行任何其他配置，则在 DbContext 上调用无参数构造函数将导致 DbContext 在 Code First 模式下运行，并使用约定创建的数据库连接。 例如：  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

在此示例中，DbContext 使用派生上下文类的命名空间限定名称（"Bloggingcontext"）作为数据库名称，并使用 SQL Express 或 LocalDB 创建此数据库的连接字符串。 如果两者都已安装，将使用 SQL Express。  

Visual Studio 2010 默认情况下包括 SQL Express 和 Visual Studio 2012 及更高版本。 在安装过程中，EntityFramework NuGet 包检查哪些数据库服务器可用。 然后，NuGet 包将通过设置 Code First 在按约定创建连接时使用的默认数据库服务器来更新配置文件。 如果 SQL Express 正在运行，将使用它。 如果 SQL Express 不可用，则将改为将 LocalDB 注册为默认值。 如果配置文件已包含默认连接工厂的设置，则不会对其进行任何更改。  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>使用具有按约定和指定数据库名称连接的 Code First  

如果你未在应用程序中执行任何其他配置，则使用你要使用的数据库名称在 DbContext 上调用字符串构造函数将导致 DbContext 在 Code First 模式下运行，并使用约定创建的数据库连接到该名称的数据库。 例如：  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

在此示例中，DbContext 使用 "BloggingDatabase" 作为数据库名称，并使用随 visual studio 2010) 或与 Visual Studio 2012) 一起安装的 LocalDB (一起安装的 SQL Express (为此数据库创建连接字符串。 如果两者都已安装，将使用 SQL Express。  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>将 Code First 与 app.config/web.config 文件中的连接字符串一起使用  

您可以选择将连接字符串放在 app.config 或 web.config 文件中。 例如：  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

这是一种简单的方法来告诉 DbContext 使用 SQL Express 或 LocalDB 之外的数据库服务器-上面的示例指定了一个 SQL Server Compact 版本的数据库。  

如果连接字符串的名称与上下文的名称相匹配 (无论是否) 命名空间限定，都将在使用无参数构造函数时通过 DbContext 找到该名称。 如果连接字符串名称不同于上下文的名称，则可以通过将连接字符串名称传递给 DbContext 构造函数，告诉 DbContext 在 Code First 模式下使用此连接。 例如：  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

或者，您可以 \<connection string name\> 对传递给 DbContext 构造函数的字符串使用窗体 "name ="。 例如：  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

此窗体使你希望在配置文件中找到连接字符串。 如果找不到具有给定名称的连接字符串，则会引发异常。  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>包含 app.config/web.config 文件中的连接字符串的数据库/Model First  

用 EF 设计器创建的模型与 Code First 不同，因为模型已存在，并且不是在应用程序运行时从代码生成的。 模型通常作为 EDMX 文件存在于你的项目中。  

设计器会将 EF 连接字符串添加到 app.config 或 web.config 文件中。 此连接字符串非常特殊，其中包含了有关如何查找 EDMX 文件中的信息的信息。 例如：  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

EF 设计器还将生成代码，该代码通过将连接字符串名称传递到 DbContext 构造函数来告诉 DbContext 使用此连接。 例如：  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbContext 知道要加载现有模型 (而不是使用 Code First 从代码) 计算它，因为连接字符串是一个 EF 连接字符串，其中包含要使用的模型的详细信息。  

## <a name="other-dbcontext-constructor-options"></a>其他 DbContext 构造函数选项  

DbContext 类包含其他构造函数和使用模式，可实现更高级的方案。 其中一些是：  

- 你可以使用 DbModelBuilder 类来生成 Code First 模型，而无需实例化 DbContext 实例。 这是一个 DbModel 对象。 然后，在准备好创建 DbContext 实例时，可以将此 DbModel 对象传递给 DbContext 构造函数之一。  
- 可以将完整的连接字符串传递给 DbContext，而不只是数据库或连接字符串的名称。 默认情况下，此连接字符串与 SqlClient 提供程序一起使用。可以通过将 IConnectionFactory 的不同实现设置到上下文来更改此设置。DefaultConnectionFactory。  
- 可以通过将现有的 DbConnection 对象传递给 DbContext 构造函数来使用该对象。 如果连接对象是 EntityConnection 的实例，则将使用连接中指定的模型，而不是使用 Code First 来计算模型。 如果对象是某个其他类型（例如 SqlConnection）的实例，则上下文会将其用于 Code First 模式。  
- 可以将现有 ObjectContext 传递到 DbContext 构造函数，以创建 DbContext 包装现有上下文的功能。 这可用于使用 ObjectContext 但想要在应用程序的某些部分中利用 DbContext 的现有应用程序。  
