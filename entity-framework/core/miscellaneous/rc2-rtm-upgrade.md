---
title: 从 EF Core 1.0 RC2 升级到 RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 779caad7883d13684b389dab7515be44bc42e1ef
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655817"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>从 EF Core 1.0 RC2 升级到 RTM

本文提供了有关将使用 RC2 包生成的应用程序移动到 1.0.0 RTM 的指南。

## <a name="package-versions"></a>包版本

通常将安装到应用程序中的顶级包的名称在 RC2 和 RTM 之间不会发生更改。

**需要将已安装的包升级到 RTM 版本：**

* 运行时包（例如 `Microsoft.EntityFrameworkCore.SqlServer`）从 `1.0.0-rc2-final` 更改为 `1.0.0`。

* `Microsoft.EntityFrameworkCore.Tools` 包从 `1.0.0-preview1-final` 改为 `1.0.0-preview2-final`。 请注意，工具仍处于预发布版本。

## <a name="existing-migrations-may-need-maxlength-added"></a>现有迁移可能需要添加 maxLength

在 RC2 中，迁移中的列定义看起来像 `table.Column<string>(nullable: true)` 并且列的长度是在迁移后的代码中存储的某些元数据中查找的。 在 RTM 中，此长度现在包含在基架代码 `table.Column<string>(maxLength: 450, nullable: true)`中。

使用 RTM 之前基架的任何现有迁移都不指定 `maxLength` 参数。 这意味着将使用数据库支持的最大长度（`nvarchar(max)` SQL Server）。 这对于某些列可能很好，但作为键、外键或索引一部分的列需要更新以包含最大长度。 按照约定，450是用于键、外键和索引列的最大长度。 如果已在模型中显式配置了长度，则应改为使用该长度。

### <a name="aspnet-identity"></a>ASP.NET 标识

此更改会影响使用 ASP.NET Identity 的项目和从 RTM 之前的项目模板创建的项目。 项目模板包含用于创建数据库的迁移。 必须编辑此迁移以指定以下列的最大 `256` 长度。

* **AspNetRoles**
  * “属性”
  * NormalizedName
* **AspNetUsers**
  * 电子邮件
  * NormalizedEmail
  * NormalizedUserName
  * UserName

如果将初始迁移应用于数据库，则无法进行此更改将导致出现以下异常。

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core：删除项目中的 "导入"

如果面向带有 RC2 的 .NET Core，则需要将 `imports` 添加到项目 json，作为某些 EF Core 的依赖项（不支持 .NET Standard）的临时解决方法。 现在可将其删除。

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
> 从版本 1.0 RTM 版， [.NET Core SDK](https://www.microsoft.com/net/download/core)不再支持使用 Visual Studio 2015 `project.json` 或开发 .net Core 应用程序。 我们建议你[从 project.json 迁移到 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。 如果使用的是 Visual Studio，建议升级到[Visual studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="uwp-add-binding-redirects"></a>UWP：添加绑定重定向

尝试在通用 Windows 平台（UWP）项目上运行 EF 命令会导致以下错误：

```output
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
