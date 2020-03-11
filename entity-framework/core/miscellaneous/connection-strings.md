---
title: 连接字符串-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: ed89d6d09b15b0dea7fd8bc3ff3e3f631495ecb7
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414091"
---
# <a name="connection-strings"></a>连接字符串

大多数数据库提供程序都需要某种形式的连接字符串才能连接到数据库。 有时，此连接字符串包含需要保护的敏感信息。 在开发、测试和生产环境等环境之间移动应用程序时，可能还需要更改连接字符串。

## <a name="winforms--wpf-applications"></a>WinForms & WPF 应用程序

WinForms、WPF 和 ASP.NET 4 应用程序都有一个已尝试并经过测试的连接字符串模式。 如果你使用的是 ASP.NET，则应将连接字符串添加到应用程序的 app.config 文件（web.config）中。 如果您的连接字符串包含敏感信息（例如用户名和密码），则可以使用[机密管理器工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)来保护配置文件的内容。

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
> 由于数据库提供程序是通过代码配置的，因此在 App.config 中存储的 EF Core 连接字符串上不需要 `providerName` 设置。

然后，可以使用上下文的 `OnConfiguring` 方法中的 `ConfigurationManager` API 读取连接字符串。 可能需要添加对 `System.Configuration` framework 程序集的引用才能使用此 API。

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

## <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 应用程序中的连接字符串通常是仅指定本地文件名的 SQLite 连接。 它们通常不包含敏感信息，并且在部署应用程序时无需更改。 因此，这些连接字符串通常可保留在代码中，如下所示。 如果希望将它们移出代码，则 UWP 支持设置概念，有关详细信息，请参阅[uwp 文档的 "应用设置" 部分](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)。

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

## <a name="aspnet-core"></a>ASP.NET Core

在 ASP.NET Core 配置系统非常灵活，并且可以将连接字符串存储在 `appsettings.json`、环境变量、用户密钥存储或其他配置源中。 有关更多详细信息，请参阅[ASP.NET Core 文档](https://docs.asp.net/en/latest/fundamentals/configuration.html)的 "配置" 部分。 下面的示例演示 `appsettings.json`中存储的连接字符串。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

上下文通常在 `Startup.cs` 中进行配置，其中的连接字符串是从配置中读取的。 请注意，`GetConnectionString()` 方法查找其键 `ConnectionStrings:<connection string name>`的配置值。 需要导入 Microsoft extension [. 配置](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)命名空间才能使用此扩展方法。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
