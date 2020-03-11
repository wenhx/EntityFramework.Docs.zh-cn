---
title: 连接字符串和模型-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
ms.openlocfilehash: 2c9f084107e4de7f5439bf0082b46a3b538496e0
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415951"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="c8068-102">连接字符串和模型</span><span class="sxs-lookup"><span data-stu-id="c8068-102">Connection strings and models</span></span>
<span data-ttu-id="c8068-103">本主题介绍实体框架如何发现要使用的数据库连接，以及如何对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="c8068-103">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="c8068-104">本主题介绍了 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="c8068-104">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="c8068-105">通常实体框架的应用程序使用派生自 DbContext 的类。</span><span class="sxs-lookup"><span data-stu-id="c8068-105">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="c8068-106">此派生类将调用基 DbContext 类上的构造函数之一来控制：</span><span class="sxs-lookup"><span data-stu-id="c8068-106">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="c8068-107">上下文将如何连接到数据库-即，如何查找或使用连接字符串</span><span class="sxs-lookup"><span data-stu-id="c8068-107">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="c8068-108">上下文是使用 Code First 计算模型还是加载使用 EF 设计器创建的模型</span><span class="sxs-lookup"><span data-stu-id="c8068-108">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="c8068-109">其他高级选项</span><span class="sxs-lookup"><span data-stu-id="c8068-109">Additional advanced options</span></span>  

<span data-ttu-id="c8068-110">以下片段显示了可使用 DbContext 构造函数的一些方式。</span><span class="sxs-lookup"><span data-stu-id="c8068-110">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="c8068-111">将 Code First 与按约定连接</span><span class="sxs-lookup"><span data-stu-id="c8068-111">Use Code First with connection by convention</span></span>  

<span data-ttu-id="c8068-112">如果你未在应用程序中进行任何其他配置，则在 DbContext 上调用无参数构造函数将导致 DbContext 在 Code First 模式下运行，并使用约定创建的数据库连接。</span><span class="sxs-lookup"><span data-stu-id="c8068-112">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="c8068-113">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-113">For example:</span></span>  

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

<span data-ttu-id="c8068-114">在此示例中，DbContext 使用派生上下文类的命名空间限定名称（"Bloggingcontext"）作为数据库名称，并使用 SQL Express 或 LocalDB 创建此数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c8068-114">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="c8068-115">如果两者都已安装，将使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="c8068-115">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="c8068-116">Visual Studio 2010 默认情况下包括 SQL Express 和 Visual Studio 2012 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="c8068-116">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="c8068-117">在安装过程中，EntityFramework NuGet 包检查哪些数据库服务器可用。</span><span class="sxs-lookup"><span data-stu-id="c8068-117">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="c8068-118">然后，NuGet 包将通过设置 Code First 在按约定创建连接时使用的默认数据库服务器来更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="c8068-118">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="c8068-119">如果 SQL Express 正在运行，将使用它。</span><span class="sxs-lookup"><span data-stu-id="c8068-119">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="c8068-120">如果 SQL Express 不可用，则将改为将 LocalDB 注册为默认值。</span><span class="sxs-lookup"><span data-stu-id="c8068-120">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="c8068-121">如果配置文件已包含默认连接工厂的设置，则不会对其进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="c8068-121">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="c8068-122">使用具有按约定和指定数据库名称连接的 Code First</span><span class="sxs-lookup"><span data-stu-id="c8068-122">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="c8068-123">如果你未在应用程序中进行任何其他配置，则使用你要使用的数据库名称在 DbContext 上调用字符串构造函数将导致 DbContext 在 Code First 模式下运行，并将数据库连接通过约定创建到数据库该名称。</span><span class="sxs-lookup"><span data-stu-id="c8068-123">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="c8068-124">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-124">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="c8068-125">在此示例中，DbContext 使用 "BloggingDatabase" 作为数据库名称，并使用 SQL Express （与 Visual Studio 2010 一起安装）或 LocalDB （随 Visual Studio 2012 一起安装）为此数据库创建连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c8068-125">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="c8068-126">如果两者都已安装，将使用 SQL Express。</span><span class="sxs-lookup"><span data-stu-id="c8068-126">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="c8068-127">结合 app.config/web.config 文件中的连接字符串使用 Code First</span><span class="sxs-lookup"><span data-stu-id="c8068-127">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="c8068-128">你可以选择将连接字符串放在 app.config 或 web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="c8068-128">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="c8068-129">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-129">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="c8068-130">这是一种简单的方法来告诉 DbContext 使用 SQL Express 或 LocalDB 之外的数据库服务器-上面的示例指定了一个 SQL Server Compact 版本的数据库。</span><span class="sxs-lookup"><span data-stu-id="c8068-130">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="c8068-131">如果连接字符串的名称与你的上下文名称匹配（无论是否具有命名空间限定），都将在使用无参数构造函数时通过 DbContext 找到该名称。</span><span class="sxs-lookup"><span data-stu-id="c8068-131">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="c8068-132">如果连接字符串名称不同于上下文的名称，则可以通过将连接字符串名称传递给 DbContext 构造函数，告诉 DbContext 在 Code First 模式下使用此连接。</span><span class="sxs-lookup"><span data-stu-id="c8068-132">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="c8068-133">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-133">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="c8068-134">或者，您可以为传递到 DbContext 构造函数的字符串使用窗体 "name =\<连接字符串名称\>"。</span><span class="sxs-lookup"><span data-stu-id="c8068-134">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="c8068-135">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-135">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="c8068-136">此窗体使你希望在配置文件中找到连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c8068-136">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="c8068-137">如果找不到具有给定名称的连接字符串，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c8068-137">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="c8068-138">App.config/web.config 文件中包含连接字符串的数据库/Model First</span><span class="sxs-lookup"><span data-stu-id="c8068-138">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="c8068-139">用 EF 设计器创建的模型与 Code First 不同，因为模型已存在，并且不是在应用程序运行时从代码生成的。</span><span class="sxs-lookup"><span data-stu-id="c8068-139">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="c8068-140">模型通常作为 EDMX 文件存在于你的项目中。</span><span class="sxs-lookup"><span data-stu-id="c8068-140">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="c8068-141">设计器会将 EF 连接字符串添加到 app.config 或 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="c8068-141">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="c8068-142">此连接字符串非常特殊，其中包含了有关如何查找 EDMX 文件中的信息的信息。</span><span class="sxs-lookup"><span data-stu-id="c8068-142">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="c8068-143">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-143">For example:</span></span>  

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

<span data-ttu-id="c8068-144">EF 设计器还将生成代码，该代码通过将连接字符串名称传递到 DbContext 构造函数来告诉 DbContext 使用此连接。</span><span class="sxs-lookup"><span data-stu-id="c8068-144">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="c8068-145">例如：</span><span class="sxs-lookup"><span data-stu-id="c8068-145">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="c8068-146">DbContext 知道要加载现有模型（而不是使用 Code First 从代码中计算），因为连接字符串是一个 EF 连接字符串，其中包含要使用的模型的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c8068-146">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="c8068-147">其他 DbContext 构造函数选项</span><span class="sxs-lookup"><span data-stu-id="c8068-147">Other DbContext constructor options</span></span>  

<span data-ttu-id="c8068-148">DbContext 类包含其他构造函数和使用模式，可实现更高级的方案。</span><span class="sxs-lookup"><span data-stu-id="c8068-148">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="c8068-149">其中一些是：</span><span class="sxs-lookup"><span data-stu-id="c8068-149">Some of these are:</span></span>  

- <span data-ttu-id="c8068-150">你可以使用 DbModelBuilder 类来生成 Code First 模型，而无需实例化 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="c8068-150">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="c8068-151">这是一个 DbModel 对象。</span><span class="sxs-lookup"><span data-stu-id="c8068-151">The result of this is a DbModel object.</span></span> <span data-ttu-id="c8068-152">然后，在准备好创建 DbContext 实例时，可以将此 DbModel 对象传递给 DbContext 构造函数之一。</span><span class="sxs-lookup"><span data-stu-id="c8068-152">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="c8068-153">可以将完整的连接字符串传递给 DbContext，而不只是数据库或连接字符串的名称。</span><span class="sxs-lookup"><span data-stu-id="c8068-153">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="c8068-154">默认情况下，此连接字符串与 SqlClient 提供程序一起使用。可以通过将 IConnectionFactory 的不同实现设置到上下文来更改此设置。DefaultConnectionFactory。</span><span class="sxs-lookup"><span data-stu-id="c8068-154">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="c8068-155">可以通过将现有的 DbConnection 对象传递给 DbContext 构造函数来使用该对象。</span><span class="sxs-lookup"><span data-stu-id="c8068-155">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="c8068-156">如果连接对象是 EntityConnection 的实例，则将使用连接中指定的模型，而不是使用 Code First 来计算模型。</span><span class="sxs-lookup"><span data-stu-id="c8068-156">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="c8068-157">如果对象是某个其他类型（例如 SqlConnection）的实例，则上下文会将其用于 Code First 模式。</span><span class="sxs-lookup"><span data-stu-id="c8068-157">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="c8068-158">可以将现有 ObjectContext 传递到 DbContext 构造函数，以创建 DbContext 包装现有上下文的功能。</span><span class="sxs-lookup"><span data-stu-id="c8068-158">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="c8068-159">这可用于使用 ObjectContext 但想要在应用程序的某些部分中利用 DbContext 的现有应用程序。</span><span class="sxs-lookup"><span data-stu-id="c8068-159">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
