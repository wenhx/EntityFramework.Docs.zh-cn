---
title: 连接字符串的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 52a8527170845d3e73ebcec518713ade3f3844f0
ms.sourcegitcommit: 06073f8efde97dd5f540dbfb69f574d8380566fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67333837"
---
# <a name="connection-strings"></a><span data-ttu-id="c33b4-102">连接字符串</span><span class="sxs-lookup"><span data-stu-id="c33b4-102">Connection Strings</span></span>

<span data-ttu-id="c33b4-103">大多数数据库提供程序需要某种形式的连接字符串以连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="c33b4-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="c33b4-104">有时此连接字符串包含必须受到保护的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c33b4-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="c33b4-105">在开发、测试和生产环境等环境之间移动应用程序时，可能还需要更改连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c33b4-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="net-framework-applications"></a><span data-ttu-id="c33b4-106">.NET framework 应用程序</span><span class="sxs-lookup"><span data-stu-id="c33b4-106">.NET Framework Applications</span></span>

<span data-ttu-id="c33b4-107">.NET framework 应用程序，如 WinForms、 WPF、 控制台和 ASP.NET 4 中，具有经过反复测试的连接字符串模式。</span><span class="sxs-lookup"><span data-stu-id="c33b4-107">.NET Framework applications, such as WinForms, WPF, Console, and ASP.NET 4, have a tried and tested connection string pattern.</span></span> <span data-ttu-id="c33b4-108">应将连接字符串添加到你的应用程序的 App.config 文件 (如果您使用的 ASP.NET Web.config)。</span><span class="sxs-lookup"><span data-stu-id="c33b4-108">The connection string should be added to your applications App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="c33b4-109">如果你的连接字符串包含敏感信息，如用户名和密码，你可以保护配置文件使用的内容[受保护的配置](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c33b4-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

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
> <span data-ttu-id="c33b4-110">`providerName`上 EF Core连接字符串存储在 App.config，因为通过代码配置了数据库提供程序不需要设置。</span><span class="sxs-lookup"><span data-stu-id="c33b4-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="c33b4-111">然后可以读取连接字符串使用`ConfigurationManager`在您的上下文中的 API`OnConfiguring`方法。</span><span class="sxs-lookup"><span data-stu-id="c33b4-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="c33b4-112">可能需要添加对引用`System.Configuration`framework 程序集，以便能够使用此 API。</span><span class="sxs-lookup"><span data-stu-id="c33b4-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="c33b4-113">通用 Windows 平台 (UWP)</span><span class="sxs-lookup"><span data-stu-id="c33b4-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="c33b4-114">UWP 应用程序中的连接字符串通常是仅指定本地文件名的 SQLite 连接。</span><span class="sxs-lookup"><span data-stu-id="c33b4-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="c33b4-115">它们通常不包含敏感信息，并且无需更改，因为应用程序已部署。</span><span class="sxs-lookup"><span data-stu-id="c33b4-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="c33b4-116">因此，这些连接字符串通常可保留在代码中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="c33b4-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="c33b4-117">如果要将它们移出代码并使 UWP 支持设置的概念，请参阅 [UWP 文档的应用设置部分](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="c33b4-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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

## <a name="aspnet-core"></a><span data-ttu-id="c33b4-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c33b4-118">ASP.NET Core</span></span>

<span data-ttu-id="c33b4-119">ASP.NET Core 中的配置系统非常灵活，并且连接字符串可以存储在 `appsettings.json`（一种环境变量）、用户机密存储或其他配置源中。</span><span class="sxs-lookup"><span data-stu-id="c33b4-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="c33b4-120">请参阅 [ASP.NET Core文档的配置部分](https://docs.asp.net/en/latest/fundamentals/configuration.html)了解更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="c33b4-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="c33b4-121">下面的示例演示 `appsettings.json` 中存储的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c33b4-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="c33b4-122">通常在中配置上下文`Startup.cs`正在从配置中读取的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c33b4-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="c33b4-123">请注意`GetConnectionString()`方法查找配置值，其键与`ConnectionStrings:<connection string name>`。</span><span class="sxs-lookup"><span data-stu-id="c33b4-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="c33b4-124">需要导入[Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)命名空间才能使用此扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c33b4-124">You need to import the [Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
