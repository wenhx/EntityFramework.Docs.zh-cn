---
title: 使用 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
ms.openlocfilehash: 34866ddbbf81f090a064af148a612dd354ae9401
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824811"
---
# <a name="using-migrateexe"></a>使用 debug.exe
Code First 迁移可用于从 visual studio 内部更新数据库，但也可以通过命令行工具 debug.exe 来执行。 此页将简要介绍如何使用 debug.exe 来对数据库执行迁移。

> [!NOTE]
> 本文假设你知道如何在基本方案中使用 Code First 迁移。 如果没有，则需要先阅读[Code First 迁移](~/ef6/modeling/code-first/migrations/index.md)，然后再继续。

## <a name="copy-migrateexe"></a>Copy debug.exe

当你安装实体框架时，将在下载包的 tools 文件夹内使用 NuGet 迁移。 在 &lt;项目文件夹中&gt;\\包\\EntityFramework。&lt;版本&gt;\\工具

迁移 .exe 后，需要将其复制到包含迁移的程序集所在的位置。

如果你的应用程序面向 .NET 4，而不是4.5，则你需要将**重定向配置**复制到该位置，并将其重命名为**debug.exe**。这是因为，debug.exe 会获得正确的绑定重定向，以便能够找到实体框架程序集。

| .NET 4.5                                      | .NET 4.0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![.NET 4.5 文件](~/ef6/media/net45files.png) | ![.NET 4.0 文件](~/ef6/media/net40files.png) |

> [!NOTE]
> debug.exe 不支持 x64 程序集。

将 node.js 移动到正确的文件夹后，应该能够使用它来对数据库执行迁移。 此实用程序的目的是执行迁移。 它无法生成迁移或创建 SQL 脚本。

## <a name="see-options"></a>查看选项

``` console
Migrate.exe /?
```

以上将显示与此实用程序关联的帮助页，请注意，你需要在运行的同一位置中具有 EntityFramework，才能使其正常运行。

## <a name="migrate-to-the-latest-migration"></a>迁移到最新迁移

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

运行 "迁移" 时，唯一的必需参数是程序集，该程序集是包含您尝试运行的迁移的程序集，但如果您未指定配置文件，它将使用所有基于约定的设置。

## <a name="migrate-to-a-specific-migration"></a>迁移到特定迁移

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

如果要运行到特定迁移的迁移，则可以指定迁移的名称。 这会根据需要运行所有以前的迁移，直到获取所指定的迁移。

## <a name="specify-working-directory"></a>指定工作目录

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

如果程序集具有依赖项或读取相对于工作目录的文件，则需要设置 startupDirectory。

## <a name="specify-migration-configuration-to-use"></a>指定要使用的迁移配置

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

如果有多个迁移配置类，继承自 DbMigrationConfiguration 的类，则需要指定将用于此执行的类。 这是通过提供可选的第二个参数（不带上述开关）来指定的。

## <a name="provide-connection-string"></a>提供连接字符串

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

如果要在命令行中指定连接字符串，则还必须提供提供程序名称。 如果未指定提供程序名称，将导致异常。

## <a name="common-problems"></a>常见问题

| 错误消息                                                                                                                                                                                                                                                                                                                      | 解决方案                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未经处理的异常： FileLoadException：无法加载文件或程序集 "EntityFramework，Version = 5.0.0.0，Culture = 中立，PublicKeyToken = b77a5c561934e089" 或其依赖项之一。 找到的程序集清单定义与程序集引用不匹配。 （异常来自 HRESULT：0x80131040）         | 这通常意味着您在运行 .NET 4 应用程序时没有重定向的 .config 文件。 需要将重定向配置复制到与 debug.exe 相同的位置，并将其重命名为 "迁移"。                                                                                       |
| 未经处理的异常： FileLoadException：无法加载文件或程序集 "EntityFramework，Version = 4.4.0.0，Culture = 中立，PublicKeyToken = b77a5c561934e089" 或其依赖项之一。 找到的程序集清单定义与程序集引用不匹配。 （异常来自 HRESULT：0x80131040）          | 此异常表示你正在运行 .NET 4.5 应用程序，并将重定向配置复制到迁移 .exe 位置。 如果应用是 .NET 4.5，则不需要在内部重定向配置文件。 删除迁移文件。                                    |
| 错误：无法更新数据库以匹配当前模型，因为存在挂起的更改，并且禁用了自动迁移。 将挂起的模型更改写入到基于代码的迁移或启用自动迁移。 将 DbMigrationsConfiguration 设置为 true 以启用自动迁移。 | 如果尚未创建迁移来处理对模型所做的更改，并且数据库与模型不匹配，则会发生此错误。 将属性添加到模型类后，如果不创建迁移来升级数据库，就会出现这种情况。 |
| 错误：未解析成员 "ToolingFacade + UpdateRunner，EntityFramework，Version = 5.0.0.0，Culture = 中立，PublicKeyToken = b77a5c561934e089" 的类型。                                                                                                                                       | 此错误的原因可能是指定了错误的启动目录。 这必须是 debug.exe 的位置                                                                                                                                                                                      |
| 未经处理的异常： NullReferenceException：对象引用未设置为对象的实例。 <br/>   在 System.web. Main （String [] args）上。                                                                                                                                             | 这可能是由于未为你使用的方案指定必需参数引起的。 例如，在不指定提供程序名称的情况下指定连接字符串。                                                                                                                        |
| 错误：在程序集 "Classlibrary1.chainone" 中找到了多个迁移配置类型。 指定要使用的名称。                                                                                                                                                                                                  | 作为错误状态，在给定的程序集中有多个配置类。 必须使用/configurationType 开关来指定要使用的。                                                                                                                                           |
| 错误：无法加载文件或程序集 "&lt;assemblyName&gt;" 或其依赖项之一。 给定的程序集名称或基本代码无效。 （异常来自 HRESULT：0x80131047）                                                                                                                                                    | 这可能是由于不正确地指定程序集名称或                                                                                                                                                                                                                          |
| 错误：无法加载文件或程序集 "&lt;assemblyName&gt;" 或其依赖项之一。 试图加载格式不正确的程序。                                                                                                                                                                          | 如果尝试对 x64 应用程序运行迁移，则会发生这种情况。 EF 5.0 和更低的将仅适用于 x86。                                                                                                                                                                                |
