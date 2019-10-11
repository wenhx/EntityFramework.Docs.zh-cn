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
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="40e5a-102">从 EF Core 1.0 RC2 升级到 RTM</span><span class="sxs-lookup"><span data-stu-id="40e5a-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="40e5a-103">本文提供了有关将使用 RC2 包生成的应用程序移动到 1.0.0 RTM 的指南。</span><span class="sxs-lookup"><span data-stu-id="40e5a-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="40e5a-104">包版本</span><span class="sxs-lookup"><span data-stu-id="40e5a-104">Package Versions</span></span>

<span data-ttu-id="40e5a-105">通常将安装到应用程序中的顶级包的名称在 RC2 和 RTM 之间不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="40e5a-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="40e5a-106">**需要将已安装的包升级到 RTM 版本：**</span><span class="sxs-lookup"><span data-stu-id="40e5a-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="40e5a-107">运行时包（例如 `Microsoft.EntityFrameworkCore.SqlServer`）从 @no__t 更改为 `1.0.0`。</span><span class="sxs-lookup"><span data-stu-id="40e5a-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="40e5a-108">@No__t-0 包从 @no__t 更改为 `1.0.0-preview2-final`。</span><span class="sxs-lookup"><span data-stu-id="40e5a-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="40e5a-109">请注意，工具仍处于预发布版本。</span><span class="sxs-lookup"><span data-stu-id="40e5a-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="40e5a-110">现有迁移可能需要添加 maxLength</span><span class="sxs-lookup"><span data-stu-id="40e5a-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="40e5a-111">在 RC2 中，迁移中的列定义与 `table.Column<string>(nullable: true)` 相同，并且在迁移后的代码中查找列的长度。</span><span class="sxs-lookup"><span data-stu-id="40e5a-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="40e5a-112">在 RTM 中，此长度现在包含在基架代码中 `table.Column<string>(maxLength: 450, nullable: true)`。</span><span class="sxs-lookup"><span data-stu-id="40e5a-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="40e5a-113">使用 RTM 之前基架的任何现有迁移都不会指定 @no__t 的参数。</span><span class="sxs-lookup"><span data-stu-id="40e5a-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="40e5a-114">这意味着将使用数据库支持的最大长度（SQL Server @no__t 为0）。</span><span class="sxs-lookup"><span data-stu-id="40e5a-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="40e5a-115">这对于某些列可能很好，但作为键、外键或索引一部分的列需要更新以包含最大长度。</span><span class="sxs-lookup"><span data-stu-id="40e5a-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="40e5a-116">按照约定，450是用于键、外键和索引列的最大长度。</span><span class="sxs-lookup"><span data-stu-id="40e5a-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="40e5a-117">如果已在模型中显式配置了长度，则应改为使用该长度。</span><span class="sxs-lookup"><span data-stu-id="40e5a-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

<span data-ttu-id="40e5a-118">**ASP.NET Identity**</span><span class="sxs-lookup"><span data-stu-id="40e5a-118">**ASP.NET Identity**</span></span>

<span data-ttu-id="40e5a-119">此更改会影响使用 ASP.NET Identity 的项目和从 RTM 之前的项目模板创建的项目。</span><span class="sxs-lookup"><span data-stu-id="40e5a-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="40e5a-120">项目模板包含用于创建数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="40e5a-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="40e5a-121">必须对此迁移进行编辑，以便为以下列的最大长度指定 `256`。</span><span class="sxs-lookup"><span data-stu-id="40e5a-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

*  <span data-ttu-id="40e5a-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="40e5a-122">**AspNetRoles**</span></span>

    * <span data-ttu-id="40e5a-123">name</span><span class="sxs-lookup"><span data-stu-id="40e5a-123">Name</span></span>

    * <span data-ttu-id="40e5a-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="40e5a-124">NormalizedName</span></span>

*  <span data-ttu-id="40e5a-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="40e5a-125">**AspNetUsers**</span></span>

   * <span data-ttu-id="40e5a-126">Email</span><span class="sxs-lookup"><span data-stu-id="40e5a-126">Email</span></span>

   * <span data-ttu-id="40e5a-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="40e5a-127">NormalizedEmail</span></span>

   * <span data-ttu-id="40e5a-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="40e5a-128">NormalizedUserName</span></span>

   * <span data-ttu-id="40e5a-129">UserName</span><span class="sxs-lookup"><span data-stu-id="40e5a-129">UserName</span></span>

<span data-ttu-id="40e5a-130">如果将初始迁移应用于数据库，则无法进行此更改将导致出现以下异常。</span><span class="sxs-lookup"><span data-stu-id="40e5a-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

```console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="40e5a-131">.NET Core：删除项目中的 "导入"</span><span class="sxs-lookup"><span data-stu-id="40e5a-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="40e5a-132">如果你已面向.NET Core 与 RC2，需要添加`imports`到临时的解决方法不支持.NET 标准的 EF Core 依赖关系的某些 project.json。</span><span class="sxs-lookup"><span data-stu-id="40e5a-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="40e5a-133">现在可将其删除。</span><span class="sxs-lookup"><span data-stu-id="40e5a-133">These can now be removed.</span></span>

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
> <span data-ttu-id="40e5a-134">截至版本 1.0 RTM， [.NET Core SDK](https://www.microsoft.com/net/download/core)不再支持`project.json`或开发.NET Core 应用程序使用 Visual Studio 2015。</span><span class="sxs-lookup"><span data-stu-id="40e5a-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="40e5a-135">我们建议你[从 project.json 迁移到 csproj](https://docs.microsoft.com/dotnet/articles/core/migration/)。</span><span class="sxs-lookup"><span data-stu-id="40e5a-135">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="40e5a-136">如果使用的是 Visual Studio，建议升级到[Visual studio 2017](https://www.visualstudio.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="40e5a-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="40e5a-137">UWP添加绑定重定向</span><span class="sxs-lookup"><span data-stu-id="40e5a-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="40e5a-138">尝试在通用 Windows 平台（UWP）项目上运行 EF 命令会导致以下错误：</span><span class="sxs-lookup"><span data-stu-id="40e5a-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```console
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="40e5a-139">需要将绑定重定向手动添加到 UWP 项目。</span><span class="sxs-lookup"><span data-stu-id="40e5a-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="40e5a-140">在项目根文件夹中创建一个名为 `App.config` 的文件，并将重定向添加到正确的程序集版本。</span><span class="sxs-lookup"><span data-stu-id="40e5a-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

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
