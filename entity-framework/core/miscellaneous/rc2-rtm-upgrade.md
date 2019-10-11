---
title: 从 EF 升级核心到 RTM-1.0 RC2 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: e7f121d18931e26e7b5d11842da6da4a9b789efe
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181367"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>从 EF Core 1.0 RC2 升级到 RTM

本文提供了有关将使用 RC2 包生成的应用程序移动到 1.0.0 RTM 的指南。

## <a name="package-versions"></a>包版本

通常将安装到应用程序中的顶级包的名称在 RC2 和 RTM 之间不会发生更改。

**需要将已安装的包升级到 RTM 版本：**

* 运行时包（例如 `Microsoft.EntityFrameworkCore.SqlServer`）从 @no__t 更改为 `1.0.0`。

* @No__t-0 包从 @no__t 更改为 `1.0.0-preview2-final`。 请注意，工具仍处于预发布版本。

## <a name="existing-migrations-may-need-maxlength-added"></a>现有迁移可能需要添加 maxLength

在 RC2 中，迁移中的列定义与 `table.Column<string>(nullable: true)` 相同，并且在迁移后的代码中查找列的长度。 在 RTM 中，此长度现在包含在基架代码中 `table.Column<string>(maxLength: 450, nullable: true)`。

使用 RTM 之前基架的任何现有迁移都不会指定 @no__t 的参数。 这意味着将使用数据库支持的最大长度（SQL Server @no__t 为0）。 这对于某些列可能很好，但作为键、外键或索引一部分的列需要更新以包含最大长度。 按照约定，450是用于键、外键和索引列的最大长度。 如果已在模型中显式配置了长度，则应改为使用该长度。

**ASP.NET Identity**

此更改会影响使用 ASP.NET Identity 的项目和从 RTM 之前的项目模板创建的项目。 项目模板包含用于创建数据库的迁移。 必须对此迁移进行编辑，以便为以下列的最大长度指定 `256`。

*  **AspNetRoles**

    * name

    * NormalizedName

*  **AspNetUsers**

   * Email

   * NormalizedEmail

   * NormalizedUserName

   * UserName

如果将初始迁移应用于数据库，则无法进行此更改将导致出现以下异常。

```console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core：删除项目中的 "导入"

如果你已面向.NET Core 与 RC2，需要添加`imports`到临时的解决方法不支持.NET 标准的 EF Core 依赖关系的某些 project.json。 现在可将其删除。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> 截至版本 1.0 RTM， [.NET Core SDK](https://www.microsoft.com/net/download/core)不再支持`project.json`或开发.NET Core 应用程序使用 Visual Studio 2015。 我们建议你[从 project.json 迁移到 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。 如果使用的是 Visual Studio，建议升级到[Visual studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="uwp-add-binding-redirects"></a>UWP添加绑定重定向

尝试在通用 Windows 平台（UWP）项目上运行 EF 命令会导致以下错误：

```console
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

需要将绑定重定向手动添加到 UWP 项目。 在项目根文件夹中创建一个名为 `App.config` 的文件，并将重定向添加到正确的程序集版本。

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
