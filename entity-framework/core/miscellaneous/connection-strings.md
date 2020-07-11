---
title: 连接字符串-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e955e93723fc371170641b0b3209cca014ef1c26
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238146"
---
# <a name="connection-strings"></a><span data-ttu-id="84f27-102">连接字符串</span><span class="sxs-lookup"><span data-stu-id="84f27-102">Connection Strings</span></span>

<span data-ttu-id="84f27-103">大多数数据库提供程序都需要某种形式的连接字符串才能连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="84f27-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="84f27-104">有时，此连接字符串包含需要保护的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="84f27-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="84f27-105">在开发、测试和生产等环境之间移动应用程序时，可能还需要更改连接字符串。</span><span class="sxs-lookup"><span data-stu-id="84f27-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="84f27-106">WinForms & WPF 应用程序</span><span class="sxs-lookup"><span data-stu-id="84f27-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="84f27-107">WinForms、WPF 和 ASP.NET 4 应用程序都有一个已尝试并经过测试的连接字符串模式。</span><span class="sxs-lookup"><span data-stu-id="84f27-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="84f27-108">如果使用 ASP.NET) ，则应将连接字符串添加到应用程序的 App.config 文件中 ( # A1。</span><span class="sxs-lookup"><span data-stu-id="84f27-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="84f27-109">如果您的连接字符串包含敏感信息（例如用户名和密码），则可以使用[机密管理器工具](/aspnet/core/security/app-secrets#secret-manager)来保护配置文件的内容。</span><span class="sxs-lookup"><span data-stu-id="84f27-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="84f27-110">`providerName`由于数据库提供程序是通过代码配置的，因此在 App.config 中存储的 EF Core 连接字符串上不需要此设置。</span><span class="sxs-lookup"><span data-stu-id="84f27-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="84f27-111">然后，你可以 `ConfigurationManager` 在上下文的方法中使用 API 来读取连接字符串 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="84f27-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="84f27-112">可能需要添加对 `System.Configuration` 框架程序集的引用才能使用此 API。</span><span class="sxs-lookup"><span data-stu-id="84f27-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="84f27-113">通用 Windows 平台 (UWP)</span><span class="sxs-lookup"><span data-stu-id="84f27-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="84f27-114">UWP 应用程序中的连接字符串通常是一个 SQLite 连接，只需指定本地文件名。</span><span class="sxs-lookup"><span data-stu-id="84f27-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="84f27-115">它们通常不包含敏感信息，并且在部署应用程序时无需更改。</span><span class="sxs-lookup"><span data-stu-id="84f27-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="84f27-116">因此，这些连接字符串通常可以保留在代码中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="84f27-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="84f27-117">如果希望将它们移出代码，则 UWP 支持设置概念，有关详细信息，请参阅[uwp 文档的 "应用设置" 部分](/windows/uwp/app-settings/store-and-retrieve-app-data)。</span><span class="sxs-lookup"><span data-stu-id="84f27-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="84f27-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84f27-118">ASP.NET Core</span></span>

<span data-ttu-id="84f27-119">在 ASP.NET Core 配置系统非常灵活，并且可以将连接字符串存储在 `appsettings.json` 、环境变量、用户密钥存储或其他配置源中。</span><span class="sxs-lookup"><span data-stu-id="84f27-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="84f27-120">有关更多详细信息，请参阅[ASP.NET Core 文档](/aspnet/core/fundamentals/configuration)的 "配置" 部分。</span><span class="sxs-lookup"><span data-stu-id="84f27-120">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="84f27-121">例如，你可以使用[机密管理器工具](/aspnet/core/security/app-secrets#secret-manager)存储数据库密码，然后在基架中，使用只包含的连接字符串 `Name=<database-alias>` 。</span><span class="sxs-lookup"><span data-stu-id="84f27-121">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="84f27-122">下面的示例显示了中存储的连接字符串 `appsettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="84f27-122">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="84f27-123">然后，上下文通常在中配置为在 `Startup.cs` 从配置中读取的连接字符串中。</span><span class="sxs-lookup"><span data-stu-id="84f27-123">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="84f27-124">请注意， `GetConnectionString()` 方法查找其键为的配置值 `ConnectionStrings:<connection string name>` 。</span><span class="sxs-lookup"><span data-stu-id="84f27-124">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="84f27-125">需要导入[Microsoft.Extensions.Configu](/dotnet/api/microsoft.extensions.configuration)命名空间才能使用此扩展方法。</span><span class="sxs-lookup"><span data-stu-id="84f27-125">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
