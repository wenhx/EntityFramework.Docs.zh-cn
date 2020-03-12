---
title: 从 EF6 移植到 EF Core - 移植基于代码的模型 - EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413857"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>将基于 EF6 代码的模型移植到 EF Core

如果你已阅读所有说明且已准备好进行移植，请查看下面一些指南，它们可帮你入门。

## <a name="install-ef-core-nuget-packages"></a>安装 EF Core NuGet 包

要使用 EF Core，请针对要使用的数据库提供程序安装 NuGet 包。 例如，如果以 SQL Server 为目标，则安装 `Microsoft.EntityFrameworkCore.SqlServer`。 有关详细信息，请参阅[数据库提供程序](../../core/providers/index.md)。

如果计划使用迁移，则还应安装 `Microsoft.EntityFrameworkCore.Tools` 包。

可将 EF6 NuGet 包 (EntityFramework) 保留在安装状态，因为 EF Core 和 EF6 可在同一应用程序中并排使用。 但是，如果你不打算在应用程序的任何区域使用 EF6，则卸载此包将有助于就需注意的代码片段生成编译错误。

## <a name="swap-namespaces"></a>交换命名空间

在 EF6 中使用的大多数 API 位于 `System.Data.Entity` 命名空间（及相关的子命名空间）中。 第一个代码更改是交换到 `Microsoft.EntityFrameworkCore` 命名空间。 通常，你首先是处理已派生的上下文代码文件，然后从此处开始，在出现编译错误时处理这些错误。

## <a name="context-configuration-connection-etc"></a>上下文配置（连接等）

如[确保 EF Core 适用于你的应用程序](ensure-requirements.md)中所述，EF Core 检测到连接到的数据库时操作更一目了然。 你将需要替代已派生的上下文中的 `OnConfiguring` 方法，并使用数据库提供程序特定的 API 设置到数据库的连接。

大多数 EF6 应用程序将连接字符串存储在应用程序 `App/Web.config` 文件中。 在 EF Core 中，你将使用 `ConfigurationManager` API 读取此连接字符串。 你可能还需要添加对 `System.Configuration` 框架程序集的引用才能使用此 API。

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

此时，需要处理编译错误并检查代码，以查看行为变化是否会对你产生影响。

## <a name="existing-migrations"></a>现有迁移

目前没有一种可行的方法可将现有 EF6 迁移移植到 EF Core。

如果可能，最好假设之前来自 EF6 的所有迁移都已应用到数据库，然后再使用 EF Core 从此处开始迁移架构。 为此，一旦模型移植到 EF Core，即可使用 `Add-Migration` 添加迁移。 然后，需已设置基架的迁移的 `Up` 和 `Down` 方法中删除所有代码。 设置初始迁移的基架后，后续迁移将与此模型进行比较。

## <a name="test-the-port"></a>测试移植

你的应用程序进行了编译，并不意味着它会成功移植到 EF Core。 需要测试应用程序的所有区域，确保行为变化都未对你的应用程序产生负面影响。
