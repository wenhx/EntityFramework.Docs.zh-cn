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
# <a name="connection-strings-and-models"></a><span data-ttu-id="83498-103">连接字符串和模型</span><span class="sxs-lookup"><span data-stu-id="83498-103">Connection strings and models</span></span>
<span data-ttu-id="83498-104">本主题介绍实体框架如何发现要使用的数据库连接，以及如何对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="83498-104">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="83498-105">本主题介绍了 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="83498-105">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="83498-106">通常实体框架的应用程序使用派生自 DbContext 的类。</span><span class="sxs-lookup"><span data-stu-id="83498-106">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="83498-107">此派生类将调用基 DbContext 类上的构造函数之一来控制：</span><span class="sxs-lookup"><span data-stu-id="83498-107">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="83498-108">上下文将如何连接到数据库-即，如何查找或使用连接字符串</span><span class="sxs-lookup"><span data-stu-id="83498-108">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="83498-109">上下文是使用 Code First 计算模型还是加载使用 EF 设计器创建的模型</span><span class="sxs-lookup"><span data-stu-id="83498-109">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="83498-110">其他高级选项</span><span class="sxs-lookup"><span data-stu-id="83498-110">Additional advanced options</span></span>  

<span data-ttu-id="83498-111">以下片段显示了可使用 DbContext 构造函数的一些方式。</span><span class="sxs-lookup"><span data-stu-id="83498-111">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="83498-112">将 Code First 与按约定连接</span><span class="sxs-lookup"><span data-stu-id="83498-112">Use Code First with connection by convention</span></span>  

<span data-ttu-id="83498-113">如果你未在应用程序中进行任何其他配置，则在 DbContext 上调用无参数构造函数将导致 DbContext 在 Code First 模式下运行，并使用约定创建的数据库连接。</span><span class="sxs-lookup"><span data-stu-id="83498-113">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="83498-114">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-114">For example:</span></span>  

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

<span data-ttu-id="83498-115">在此示例中，DbContext 使用派生上下文类的命名空间限定名称（"Bloggingcontext"）作为数据库名称，并使用 SQL Express 或 LocalDB 创建此数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="83498-115">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="83498-116">如果两者都已安装，将使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="83498-116">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="83498-117">Visual Studio 2010 默认情况下包括 SQL Express 和 Visual Studio 2012 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="83498-117">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="83498-118">在安装过程中，EntityFramework NuGet 包检查哪些数据库服务器可用。</span><span class="sxs-lookup"><span data-stu-id="83498-118">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="83498-119">然后，NuGet 包将通过设置 Code First 在按约定创建连接时使用的默认数据库服务器来更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="83498-119">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="83498-120">如果 SQL Express 正在运行，将使用它。</span><span class="sxs-lookup"><span data-stu-id="83498-120">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="83498-121">如果 SQL Express 不可用，则将改为将 LocalDB 注册为默认值。</span><span class="sxs-lookup"><span data-stu-id="83498-121">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="83498-122">如果配置文件已包含默认连接工厂的设置，则不会对其进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="83498-122">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="83498-123">使用具有按约定和指定数据库名称连接的 Code First</span><span class="sxs-lookup"><span data-stu-id="83498-123">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="83498-124">如果你未在应用程序中执行任何其他配置，则使用你要使用的数据库名称在 DbContext 上调用字符串构造函数将导致 DbContext 在 Code First 模式下运行，并使用约定创建的数据库连接到该名称的数据库。</span><span class="sxs-lookup"><span data-stu-id="83498-124">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="83498-125">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-125">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="83498-126">在此示例中，DbContext 使用 "BloggingDatabase" 作为数据库名称，并使用随 visual studio 2010) 或与 Visual Studio 2012) 一起安装的 LocalDB (一起安装的 SQL Express (为此数据库创建连接字符串。</span><span class="sxs-lookup"><span data-stu-id="83498-126">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="83498-127">如果两者都已安装，将使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="83498-127">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="83498-128">将 Code First 与 app.config/web.config 文件中的连接字符串一起使用</span><span class="sxs-lookup"><span data-stu-id="83498-128">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="83498-129">您可以选择将连接字符串放在 app.config 或 web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="83498-129">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="83498-130">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-130">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="83498-131">这是一种简单的方法来告诉 DbContext 使用 SQL Express 或 LocalDB 之外的数据库服务器-上面的示例指定了一个 SQL Server Compact 版本的数据库。</span><span class="sxs-lookup"><span data-stu-id="83498-131">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="83498-132">如果连接字符串的名称与上下文的名称相匹配 (无论是否) 命名空间限定，都将在使用无参数构造函数时通过 DbContext 找到该名称。</span><span class="sxs-lookup"><span data-stu-id="83498-132">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="83498-133">如果连接字符串名称不同于上下文的名称，则可以通过将连接字符串名称传递给 DbContext 构造函数，告诉 DbContext 在 Code First 模式下使用此连接。</span><span class="sxs-lookup"><span data-stu-id="83498-133">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="83498-134">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-134">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="83498-135">或者，您可以 \<connection string name\> 对传递给 DbContext 构造函数的字符串使用窗体 "name ="。</span><span class="sxs-lookup"><span data-stu-id="83498-135">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="83498-136">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-136">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="83498-137">此窗体使你希望在配置文件中找到连接字符串。</span><span class="sxs-lookup"><span data-stu-id="83498-137">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="83498-138">如果找不到具有给定名称的连接字符串，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="83498-138">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="83498-139">包含 app.config/web.config 文件中的连接字符串的数据库/Model First</span><span class="sxs-lookup"><span data-stu-id="83498-139">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="83498-140">用 EF 设计器创建的模型与 Code First 不同，因为模型已存在，并且不是在应用程序运行时从代码生成的。</span><span class="sxs-lookup"><span data-stu-id="83498-140">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="83498-141">模型通常作为 EDMX 文件存在于你的项目中。</span><span class="sxs-lookup"><span data-stu-id="83498-141">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="83498-142">设计器会将 EF 连接字符串添加到 app.config 或 web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="83498-142">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="83498-143">此连接字符串非常特殊，其中包含了有关如何查找 EDMX 文件中的信息的信息。</span><span class="sxs-lookup"><span data-stu-id="83498-143">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="83498-144">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-144">For example:</span></span>  

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

<span data-ttu-id="83498-145">EF 设计器还将生成代码，该代码通过将连接字符串名称传递到 DbContext 构造函数来告诉 DbContext 使用此连接。</span><span class="sxs-lookup"><span data-stu-id="83498-145">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="83498-146">例如：</span><span class="sxs-lookup"><span data-stu-id="83498-146">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="83498-147">DbContext 知道要加载现有模型 (而不是使用 Code First 从代码) 计算它，因为连接字符串是一个 EF 连接字符串，其中包含要使用的模型的详细信息。</span><span class="sxs-lookup"><span data-stu-id="83498-147">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="83498-148">其他 DbContext 构造函数选项</span><span class="sxs-lookup"><span data-stu-id="83498-148">Other DbContext constructor options</span></span>  

<span data-ttu-id="83498-149">DbContext 类包含其他构造函数和使用模式，可实现更高级的方案。</span><span class="sxs-lookup"><span data-stu-id="83498-149">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="83498-150">其中一些是：</span><span class="sxs-lookup"><span data-stu-id="83498-150">Some of these are:</span></span>  

- <span data-ttu-id="83498-151">你可以使用 DbModelBuilder 类来生成 Code First 模型，而无需实例化 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="83498-151">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="83498-152">这是一个 DbModel 对象。</span><span class="sxs-lookup"><span data-stu-id="83498-152">The result of this is a DbModel object.</span></span> <span data-ttu-id="83498-153">然后，在准备好创建 DbContext 实例时，可以将此 DbModel 对象传递给 DbContext 构造函数之一。</span><span class="sxs-lookup"><span data-stu-id="83498-153">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="83498-154">可以将完整的连接字符串传递给 DbContext，而不只是数据库或连接字符串的名称。</span><span class="sxs-lookup"><span data-stu-id="83498-154">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="83498-155">默认情况下，此连接字符串与 SqlClient 提供程序一起使用。可以通过将 IConnectionFactory 的不同实现设置到上下文来更改此设置。DefaultConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="83498-155">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="83498-156">可以通过将现有的 DbConnection 对象传递给 DbContext 构造函数来使用该对象。</span><span class="sxs-lookup"><span data-stu-id="83498-156">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="83498-157">如果连接对象是 EntityConnection 的实例，则将使用连接中指定的模型，而不是使用 Code First 来计算模型。</span><span class="sxs-lookup"><span data-stu-id="83498-157">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="83498-158">如果对象是某个其他类型（例如 SqlConnection）的实例，则上下文会将其用于 Code First 模式。</span><span class="sxs-lookup"><span data-stu-id="83498-158">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="83498-159">可以将现有 ObjectContext 传递到 DbContext 构造函数，以创建 DbContext 包装现有上下文的功能。</span><span class="sxs-lookup"><span data-stu-id="83498-159">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="83498-160">这可用于使用 ObjectContext 但想要在应用程序的某些部分中利用 DbContext 的现有应用程序。</span><span class="sxs-lookup"><span data-stu-id="83498-160">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
