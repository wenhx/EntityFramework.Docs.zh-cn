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
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="286b0-102">从 EF Core 1.0 RC2 升级到 RTM</span><span class="sxs-lookup"><span data-stu-id="286b0-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="286b0-103">本文提供了有关将使用 RC2 包生成的应用程序移动到 1.0.0 RTM 的指南。</span><span class="sxs-lookup"><span data-stu-id="286b0-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="286b0-104">包版本</span><span class="sxs-lookup"><span data-stu-id="286b0-104">Package Versions</span></span>

<span data-ttu-id="286b0-105">通常将安装到应用程序中的顶级包的名称在 RC2 和 RTM 之间不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="286b0-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="286b0-106">**需要将已安装的包升级到 RTM 版本：**</span><span class="sxs-lookup"><span data-stu-id="286b0-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="286b0-107">运行时包（例如 `Microsoft.EntityFrameworkCore.SqlServer`）从 `1.0.0-rc2-final` 更改为 `1.0.0`。</span><span class="sxs-lookup"><span data-stu-id="286b0-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="286b0-108">`Microsoft.EntityFrameworkCore.Tools` 包从 `1.0.0-preview1-final` 改为 `1.0.0-preview2-final`。</span><span class="sxs-lookup"><span data-stu-id="286b0-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="286b0-109">请注意，工具仍处于预发布版本。</span><span class="sxs-lookup"><span data-stu-id="286b0-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="286b0-110">现有迁移可能需要添加 maxLength</span><span class="sxs-lookup"><span data-stu-id="286b0-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="286b0-111">在 RC2 中，迁移中的列定义看起来像 `table.Column<string>(nullable: true)` 并且列的长度是在迁移后的代码中存储的某些元数据中查找的。</span><span class="sxs-lookup"><span data-stu-id="286b0-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="286b0-112">在 RTM 中，此长度现在包含在基架代码 `table.Column<string>(maxLength: 450, nullable: true)`中。</span><span class="sxs-lookup"><span data-stu-id="286b0-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="286b0-113">使用 RTM 之前基架的任何现有迁移都不指定 `maxLength` 参数。</span><span class="sxs-lookup"><span data-stu-id="286b0-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="286b0-114">这意味着将使用数据库支持的最大长度（`nvarchar(max)` SQL Server）。</span><span class="sxs-lookup"><span data-stu-id="286b0-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="286b0-115">这对于某些列可能很好，但作为键、外键或索引一部分的列需要更新以包含最大长度。</span><span class="sxs-lookup"><span data-stu-id="286b0-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="286b0-116">按照约定，450是用于键、外键和索引列的最大长度。</span><span class="sxs-lookup"><span data-stu-id="286b0-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="286b0-117">如果已在模型中显式配置了长度，则应改为使用该长度。</span><span class="sxs-lookup"><span data-stu-id="286b0-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="286b0-118">ASP.NET 标识</span><span class="sxs-lookup"><span data-stu-id="286b0-118">ASP.NET Identity</span></span>

<span data-ttu-id="286b0-119">此更改会影响使用 ASP.NET Identity 的项目和从 RTM 之前的项目模板创建的项目。</span><span class="sxs-lookup"><span data-stu-id="286b0-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="286b0-120">项目模板包含用于创建数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="286b0-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="286b0-121">必须编辑此迁移以指定以下列的最大 `256` 长度。</span><span class="sxs-lookup"><span data-stu-id="286b0-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="286b0-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="286b0-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="286b0-123">“属性”</span><span class="sxs-lookup"><span data-stu-id="286b0-123">Name</span></span>
  * <span data-ttu-id="286b0-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="286b0-124">NormalizedName</span></span>
* <span data-ttu-id="286b0-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="286b0-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="286b0-126">电子邮件</span><span class="sxs-lookup"><span data-stu-id="286b0-126">Email</span></span>
  * <span data-ttu-id="286b0-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="286b0-127">NormalizedEmail</span></span>
  * <span data-ttu-id="286b0-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="286b0-128">NormalizedUserName</span></span>
  * <span data-ttu-id="286b0-129">UserName</span><span class="sxs-lookup"><span data-stu-id="286b0-129">UserName</span></span>

<span data-ttu-id="286b0-130">如果将初始迁移应用于数据库，则无法进行此更改将导致出现以下异常。</span><span class="sxs-lookup"><span data-stu-id="286b0-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="286b0-131">.NET Core：删除项目中的 "导入"</span><span class="sxs-lookup"><span data-stu-id="286b0-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="286b0-132">如果面向带有 RC2 的 .NET Core，则需要将 `imports` 添加到项目 json，作为某些 EF Core 的依赖项（不支持 .NET Standard）的临时解决方法。</span><span class="sxs-lookup"><span data-stu-id="286b0-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="286b0-133">现在可将其删除。</span><span class="sxs-lookup"><span data-stu-id="286b0-133">These can now be removed.</span></span>

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
> <span data-ttu-id="286b0-134">从版本 1.0 RTM 版， [.NET Core SDK](https://www.microsoft.com/net/download/core)不再支持使用 Visual Studio 2015 `project.json` 或开发 .net Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="286b0-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="286b0-135">我们建议你[从 project.json 迁移到 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。</span><span class="sxs-lookup"><span data-stu-id="286b0-135">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="286b0-136">如果使用的是 Visual Studio，建议升级到[Visual studio 2017](https://www.visualstudio.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="286b0-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="286b0-137">UWP：添加绑定重定向</span><span class="sxs-lookup"><span data-stu-id="286b0-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="286b0-138">尝试在通用 Windows 平台（UWP）项目上运行 EF 命令会导致以下错误：</span><span class="sxs-lookup"><span data-stu-id="286b0-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="286b0-139">需要将绑定重定向手动添加到 UWP 项目。</span><span class="sxs-lookup"><span data-stu-id="286b0-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="286b0-140">在项目根文件夹中创建一个名为 `App.config` 的文件，并将重定向添加到正确的程序集版本。</span><span class="sxs-lookup"><span data-stu-id="286b0-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

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
