---
title: 从 EF6 迁移到 EF Core-移植基于代码的模型-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181217"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>将基于 EF6 代码的模型移植到 EF Core

如果你已经阅读了所有注意事项，并且已准备好进行移植，则可以通过以下指南来帮助你入门。

## <a name="install-ef-core-nuget-packages"></a>安装 EF Core NuGet 包

若要使用 EF Core，请为要使用的数据库提供程序安装 NuGet 包。 例如，如果以 SQL Server 为目标，则将安装 `Microsoft.EntityFrameworkCore.SqlServer`。 有关详细信息，请参阅[数据库提供程序](../../core/providers/index.md)。

如果计划使用迁移，则还应安装 `Microsoft.EntityFrameworkCore.Tools` 包。

最好将从 EF6 NuGet 包 (EntityFramework) 安装，因为 EF Core 和 ef6 更高版本可以是在同一应用程序并行使用。 但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载包将有助于在需要注意的代码段上生成编译错误。

## <a name="swap-namespaces"></a>交换命名空间

在 EF6 中使用的大多数 Api 位于 `System.Data.Entity` 命名空间（以及相关的子命名空间）中。 第一次代码更改是交换到 `Microsoft.EntityFrameworkCore` 命名空间。 通常，您将从派生的上下文代码文件开始，然后从那里开始处理编译错误。

## <a name="context-configuration-connection-etc"></a>上下文配置（连接等）

如[确保 EF Core 适用于你的应用程序](ensure-requirements.md)，EF Core 对检测要连接到的数据库的神奇程度较低。 你将需要覆盖派生上下文上的 `OnConfiguring` 方法，并使用数据库提供程序特定的 API 来设置与数据库的连接。

大多数 EF6 应用程序将连接字符串存储在应用程序 `App/Web.config` 文件中。 在 EF Core 中，你将使用 `ConfigurationManager` API 读取此连接字符串。 可能需要添加对 `System.Configuration` framework 程序集的引用才能使用此 API。

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

## <a name="update-your-code"></a>更新代码

此时，需要解决编译错误和检查代码，以查看行为更改是否会影响你。

## <a name="existing-migrations"></a>现有迁移

目前没有一种可行的方法可以将现有的 EF6 迁移移植到 EF Core。

如果可能，最好假设已将 EF6 中的所有以前的迁移应用到数据库，然后使用 EF Core 开始从该点迁移架构。 若要执行此操作，可以使用 `Add-Migration` 命令在模型移植到 EF Core 后添加迁移。 然后，将从基架迁移的 `Up` 和 `Down` 方法中删除所有代码。 当基架初始迁移时，后续迁移将与模型进行比较。

## <a name="test-the-port"></a>测试端口

只是因为你的应用程序将编译，并不意味着它成功移植到 EF Core。 需要测试应用程序的所有区域，以确保不会对应用程序产生负面影响。
