---
title: EF Core 工具参考 (程序包管理器控制台) -EF Core
description: Entity Framework Core Visual Studio 包管理器控制台的参考指南
author: bricelam
ms.date: 10/13/2020
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 3a9599288d74013bf4da910c64bc858539c0c32c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062381"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="17815-103">Entity Framework Core 工具参考-Visual Studio 中的包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="17815-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="17815-104">程序包管理器控制台 (用于 Entity Framework Core 执行设计时开发任务的 PMC) 工具。</span><span class="sxs-lookup"><span data-stu-id="17815-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="17815-105">例如，他们基于现有数据库创建 [迁移](/aspnet/core/data/ef-mvc/migrations)、应用迁移和生成模型的代码。</span><span class="sxs-lookup"><span data-stu-id="17815-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="17815-106">命令使用 [包管理器控制台](/nuget/tools/package-manager-console)在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="17815-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="17815-107">这些工具同时适用于 .NET Framework 和 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="17815-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="17815-108">如果未使用 Visual Studio，则建议改为使用 [EF Core 命令行工具](xref:core/miscellaneous/cli/dotnet) 。</span><span class="sxs-lookup"><span data-stu-id="17815-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/miscellaneous/cli/dotnet) instead.</span></span> <span data-ttu-id="17815-109">.NET Core CLI 工具是跨平台的，在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="17815-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="17815-110">安装工具</span><span class="sxs-lookup"><span data-stu-id="17815-110">Installing the tools</span></span>

<span data-ttu-id="17815-111">在 **Package Manager console**中运行以下命令，安装包管理器控制台工具：</span><span class="sxs-lookup"><span data-stu-id="17815-111">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="17815-112">在 **Package Manager Console**中运行以下命令，更新这些工具。</span><span class="sxs-lookup"><span data-stu-id="17815-112">Update the tools by running the following command in **Package Manager Console**.</span></span>

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="17815-113">验证安装</span><span class="sxs-lookup"><span data-stu-id="17815-113">Verify the installation</span></span>

<span data-ttu-id="17815-114">通过运行以下命令验证是否已安装这些工具：</span><span class="sxs-lookup"><span data-stu-id="17815-114">Verify that the tools are installed by running this command:</span></span>

```powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="17815-115">输出如下所示 (它不会告诉你使用的工具版本) ：</span><span class="sxs-lookup"><span data-stu-id="17815-115">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="17815-116">使用工具</span><span class="sxs-lookup"><span data-stu-id="17815-116">Using the tools</span></span>

<span data-ttu-id="17815-117">使用这些工具之前：</span><span class="sxs-lookup"><span data-stu-id="17815-117">Before using the tools:</span></span>

* <span data-ttu-id="17815-118">了解目标项目和启动项目之间的差异。</span><span class="sxs-lookup"><span data-stu-id="17815-118">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="17815-119">了解如何将工具与 .NET Standard 类库一起使用。</span><span class="sxs-lookup"><span data-stu-id="17815-119">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="17815-120">对于 ASP.NET Core 项目，请设置环境。</span><span class="sxs-lookup"><span data-stu-id="17815-120">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="17815-121">目标和启动项目</span><span class="sxs-lookup"><span data-stu-id="17815-121">Target and startup project</span></span>

<span data-ttu-id="17815-122">命令引用 *项目* 和 *启动项目*。</span><span class="sxs-lookup"><span data-stu-id="17815-122">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="17815-123">该 *项目* 也称为 *目标项目* ，因为它是命令在其中添加或删除文件的位置。</span><span class="sxs-lookup"><span data-stu-id="17815-123">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="17815-124">默认情况下，在 "**程序包管理器控制台**" 中选择的**默认项目**是目标项目。</span><span class="sxs-lookup"><span data-stu-id="17815-124">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="17815-125">可以使用选项指定其他项目作为目标项目 <nobr>`--project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="17815-125">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="17815-126">*启动项目*是工具生成和运行的项目。</span><span class="sxs-lookup"><span data-stu-id="17815-126">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="17815-127">这些工具必须在设计时执行应用程序代码，以获取有关项目的信息，例如数据库连接字符串和模型的配置。</span><span class="sxs-lookup"><span data-stu-id="17815-127">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="17815-128">默认情况下，**解决方案资源管理器**中的**启动项目**是启动项目。</span><span class="sxs-lookup"><span data-stu-id="17815-128">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="17815-129">您可以使用选项指定其他项目作为启动项目 <nobr>`--startup-project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="17815-129">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="17815-130">启动项目和目标项目通常是同一个项目。</span><span class="sxs-lookup"><span data-stu-id="17815-130">The startup project and target project are often the same project.</span></span> <span data-ttu-id="17815-131">它们是不同的项目的典型方案是：</span><span class="sxs-lookup"><span data-stu-id="17815-131">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="17815-132">EF Core 的上下文和实体类位于 .NET Core 类库中。</span><span class="sxs-lookup"><span data-stu-id="17815-132">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="17815-133">.NET Core 控制台应用程序或 web 应用程序引用类库。</span><span class="sxs-lookup"><span data-stu-id="17815-133">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="17815-134">还可以 [将迁移代码放在与 EF Core 上下文分离](xref:core/managing-schemas/migrations/projects)的类库中。</span><span class="sxs-lookup"><span data-stu-id="17815-134">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="17815-135">其他目标框架</span><span class="sxs-lookup"><span data-stu-id="17815-135">Other target frameworks</span></span>

<span data-ttu-id="17815-136">包管理器控制台工具适用于 .NET Core 或 .NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="17815-136">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="17815-137">.NET Standard 类库中具有 EF Core 模型的应用可能没有 .NET Core 或 .NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="17815-137">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="17815-138">例如，这适用于 Xamarin 和通用 Windows 平台应用。</span><span class="sxs-lookup"><span data-stu-id="17815-138">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="17815-139">在这种情况下，你可以创建一个 .NET Core 或 .NET Framework 的控制台应用程序项目，该项目的唯一用途是充当工具的启动项目。</span><span class="sxs-lookup"><span data-stu-id="17815-139">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="17815-140">项目可以是不包含实际代码的虚拟项目， &mdash; 只需为工具提供目标。</span><span class="sxs-lookup"><span data-stu-id="17815-140">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="17815-141">为什么需要虚拟项目？</span><span class="sxs-lookup"><span data-stu-id="17815-141">Why is a dummy project required?</span></span> <span data-ttu-id="17815-142">如前文所述，这些工具必须在设计时执行应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="17815-142">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="17815-143">为此，需要使用 .NET Core 或 .NET Framework 运行时。</span><span class="sxs-lookup"><span data-stu-id="17815-143">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="17815-144">当 EF Core 模型位于面向 .NET Core 或 .NET Framework 的项目中时，EF Core 工具会借用项目中的运行时。</span><span class="sxs-lookup"><span data-stu-id="17815-144">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="17815-145">如果 EF Core 模型在 .NET Standard 类库中，则无法执行此操作。</span><span class="sxs-lookup"><span data-stu-id="17815-145">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="17815-146">.NET Standard 不是实际的 .NET 实现;它是 .NET 实现所必须支持的一组 Api 的规范。</span><span class="sxs-lookup"><span data-stu-id="17815-146">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="17815-147">因此 .NET Standard 不足以执行应用程序代码 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="17815-147">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="17815-148">你创建的用于启动项目的虚拟项目提供了一个具体的目标平台，工具可在其中加载 .NET Standard 类库。</span><span class="sxs-lookup"><span data-stu-id="17815-148">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="17815-149">ASP.NET Core 环境</span><span class="sxs-lookup"><span data-stu-id="17815-149">ASP.NET Core environment</span></span>

<span data-ttu-id="17815-150">若要为 ASP.NET Core 项目指定环境，请在运行命令之前设置 **env： ASPNETCORE_ENVIRONMENT** 。</span><span class="sxs-lookup"><span data-stu-id="17815-150">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="17815-151">通用参数</span><span class="sxs-lookup"><span data-stu-id="17815-151">Common parameters</span></span>

<span data-ttu-id="17815-152">下表显示了所有 EF Core 命令共有的参数：</span><span class="sxs-lookup"><span data-stu-id="17815-152">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="17815-153">参数</span><span class="sxs-lookup"><span data-stu-id="17815-153">Parameter</span></span>                 | <span data-ttu-id="17815-154">说明</span><span class="sxs-lookup"><span data-stu-id="17815-154">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="17815-155">-上下文 \<String></span><span class="sxs-lookup"><span data-stu-id="17815-155">-Context \<String></span></span>        | <span data-ttu-id="17815-156">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="17815-156">The `DbContext` class to use.</span></span> <span data-ttu-id="17815-157">仅命名空间或完全限定类名。</span><span class="sxs-lookup"><span data-stu-id="17815-157">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="17815-158">如果省略此参数，EF Core 将查找上下文类。</span><span class="sxs-lookup"><span data-stu-id="17815-158">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="17815-159">如果有多个上下文类，则此参数是必需的。</span><span class="sxs-lookup"><span data-stu-id="17815-159">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="17815-160">-项目 \<String></span><span class="sxs-lookup"><span data-stu-id="17815-160">-Project \<String></span></span>        | <span data-ttu-id="17815-161">目标项目。</span><span class="sxs-lookup"><span data-stu-id="17815-161">The target project.</span></span> <span data-ttu-id="17815-162">如果省略此参数，则**包管理器控制台**的**默认项目**将用作目标项目。</span><span class="sxs-lookup"><span data-stu-id="17815-162">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="17815-163"><nobr>-StartupProject</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="17815-163"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="17815-164">启动项目。</span><span class="sxs-lookup"><span data-stu-id="17815-164">The startup project.</span></span> <span data-ttu-id="17815-165">如果省略此参数，则使用**解决方案属性**中的**启动项目**作为目标项目。</span><span class="sxs-lookup"><span data-stu-id="17815-165">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="17815-166">-参数 \<String></span><span class="sxs-lookup"><span data-stu-id="17815-166">-Args \<String></span></span>           | <span data-ttu-id="17815-167">传递给应用程序的参数。</span><span class="sxs-lookup"><span data-stu-id="17815-167">Arguments passed to the application.</span></span> <span data-ttu-id="17815-168">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-168">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="17815-169">-Verbose</span><span class="sxs-lookup"><span data-stu-id="17815-169">-Verbose</span></span>                  | <span data-ttu-id="17815-170">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="17815-170">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="17815-171">若要显示有关命令的帮助信息，请使用 PowerShell `Get-Help` 命令。</span><span class="sxs-lookup"><span data-stu-id="17815-171">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="17815-172">上下文、项目和 StartupProject 参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="17815-172">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="17815-173">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="17815-173">Add-Migration</span></span>

<span data-ttu-id="17815-174">添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-174">Adds a new migration.</span></span>

<span data-ttu-id="17815-175">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-175">Parameters:</span></span>

| <span data-ttu-id="17815-176">参数</span><span class="sxs-lookup"><span data-stu-id="17815-176">Parameter</span></span>                         | <span data-ttu-id="17815-177">说明</span><span class="sxs-lookup"><span data-stu-id="17815-177">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="17815-178"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="17815-178"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="17815-179">迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="17815-179">The name of the migration.</span></span> <span data-ttu-id="17815-180">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="17815-180">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="17815-181"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-181"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="17815-182">用于输出文件的目录。</span><span class="sxs-lookup"><span data-stu-id="17815-182">The directory use to output the files.</span></span> <span data-ttu-id="17815-183">路径相对于目标项目目录。</span><span class="sxs-lookup"><span data-stu-id="17815-183">Paths are relative to the target project directory.</span></span> <span data-ttu-id="17815-184">默认值为 "迁移"。</span><span class="sxs-lookup"><span data-stu-id="17815-184">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="17815-185"><nobr>-命名空间 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-185"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="17815-186">要用于生成的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="17815-186">The namespace to use for the generated classes.</span></span> <span data-ttu-id="17815-187">默认为从输出目录生成。</span><span class="sxs-lookup"><span data-stu-id="17815-187">Defaults to generated from the output directory.</span></span> <span data-ttu-id="17815-188">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-188">Added in EF Core 5.0.</span></span>  |

<span data-ttu-id="17815-189">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-189">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="drop-database"></a><span data-ttu-id="17815-190">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="17815-190">Drop-Database</span></span>

<span data-ttu-id="17815-191">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="17815-191">Drops the database.</span></span>

<span data-ttu-id="17815-192">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-192">Parameters:</span></span>

| <span data-ttu-id="17815-193">参数</span><span class="sxs-lookup"><span data-stu-id="17815-193">Parameter</span></span> | <span data-ttu-id="17815-194">说明</span><span class="sxs-lookup"><span data-stu-id="17815-194">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="17815-195">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="17815-195">-WhatIf</span></span>   | <span data-ttu-id="17815-196">显示要删除的数据库，但不删除它。</span><span class="sxs-lookup"><span data-stu-id="17815-196">Show which database would be dropped, but don't drop it.</span></span> |

<span data-ttu-id="17815-197">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-197">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-dbcontext"></a><span data-ttu-id="17815-198">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="17815-198">Get-DbContext</span></span>

<span data-ttu-id="17815-199">列出并获取有关可用 `DbContext` 类型的信息。</span><span class="sxs-lookup"><span data-stu-id="17815-199">Lists and gets information about available `DbContext` types.</span></span>

<span data-ttu-id="17815-200">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-200">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-migration"></a><span data-ttu-id="17815-201">Get-Migration</span><span class="sxs-lookup"><span data-stu-id="17815-201">Get-Migration</span></span>

<span data-ttu-id="17815-202">列出可用迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-202">Lists available migrations.</span></span> <span data-ttu-id="17815-203">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-203">Added in EF Core 5.0.</span></span>

<span data-ttu-id="17815-204">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-204">Parameters:</span></span>

| <span data-ttu-id="17815-205">参数</span><span class="sxs-lookup"><span data-stu-id="17815-205">Parameter</span></span>                          | <span data-ttu-id="17815-206">说明</span><span class="sxs-lookup"><span data-stu-id="17815-206">Description</span></span>                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span data-ttu-id="17815-207"><nobr>-连接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-207"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="17815-208">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="17815-208">The connection string to the database.</span></span> <span data-ttu-id="17815-209">默认值为 AddDbContext 或 OnConfiguring 中指定的值。</span><span class="sxs-lookup"><span data-stu-id="17815-209">Defaults to the one specified in AddDbContext or OnConfiguring.</span></span> |
| <span data-ttu-id="17815-210">-NoConnect</span><span class="sxs-lookup"><span data-stu-id="17815-210">-NoConnect</span></span>                         | <span data-ttu-id="17815-211">请勿连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="17815-211">Don't connect to the database.</span></span>                                                                         |

<span data-ttu-id="17815-212">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-212">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="17815-213">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="17815-213">Remove-Migration</span></span>

<span data-ttu-id="17815-214">删除上一次迁移 (回滚对迁移) 所做的代码更改。</span><span class="sxs-lookup"><span data-stu-id="17815-214">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="17815-215">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-215">Parameters:</span></span>

| <span data-ttu-id="17815-216">参数</span><span class="sxs-lookup"><span data-stu-id="17815-216">Parameter</span></span> | <span data-ttu-id="17815-217">说明</span><span class="sxs-lookup"><span data-stu-id="17815-217">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="17815-218">-Force</span><span class="sxs-lookup"><span data-stu-id="17815-218">-Force</span></span>    | <span data-ttu-id="17815-219">还原迁移 (回滚应用于数据库的更改) 。</span><span class="sxs-lookup"><span data-stu-id="17815-219">Revert the migration (roll back the changes that were applied to the database).</span></span> |

<span data-ttu-id="17815-220">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-220">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="scaffold-dbcontext"></a><span data-ttu-id="17815-221">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="17815-221">Scaffold-DbContext</span></span>

<span data-ttu-id="17815-222">为 `DbContext` 数据库的和实体类型生成代码。</span><span class="sxs-lookup"><span data-stu-id="17815-222">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="17815-223">为了使 `Scaffold-DbContext` 生成实体类型，数据库表必须具有主键。</span><span class="sxs-lookup"><span data-stu-id="17815-223">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="17815-224">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-224">Parameters:</span></span>

| <span data-ttu-id="17815-225">参数</span><span class="sxs-lookup"><span data-stu-id="17815-225">Parameter</span></span>                          | <span data-ttu-id="17815-226">说明</span><span class="sxs-lookup"><span data-stu-id="17815-226">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="17815-227"><nobr>-连接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-227"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="17815-228">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="17815-228">The connection string to the database.</span></span> <span data-ttu-id="17815-229">对于 ASP.NET Core 2.x 项目，值可以是\*name = \<name of connection string> \*。</span><span class="sxs-lookup"><span data-stu-id="17815-229">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="17815-230">在这种情况下，该名称来自为项目设置的配置源。</span><span class="sxs-lookup"><span data-stu-id="17815-230">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="17815-231">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="17815-231">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="17815-232"><nobr>-提供程序 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-232"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="17815-233">要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="17815-233">The provider to use.</span></span> <span data-ttu-id="17815-234">通常，这是 NuGet 包的名称，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="17815-234">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="17815-235">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="17815-235">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="17815-236">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="17815-236">-OutputDir \<String></span></span>               | <span data-ttu-id="17815-237">要在其中放置文件的目录。</span><span class="sxs-lookup"><span data-stu-id="17815-237">The directory to put files in.</span></span> <span data-ttu-id="17815-238">路径相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="17815-238">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="17815-239">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="17815-239">-ContextDir \<String></span></span>              | <span data-ttu-id="17815-240">要在其中放置文件的目录 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="17815-240">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="17815-241">路径相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="17815-241">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="17815-242">-命名空间 \<String></span><span class="sxs-lookup"><span data-stu-id="17815-242">-Namespace \<String></span></span>               | <span data-ttu-id="17815-243">要用于所有生成的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="17815-243">The namespace to use for all generated classes.</span></span> <span data-ttu-id="17815-244">默认值为从根命名空间和输出目录生成。</span><span class="sxs-lookup"><span data-stu-id="17815-244">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="17815-245">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-245">Added in EF Core 5.0.</span></span>                                                                                                                           |
| <span data-ttu-id="17815-246">-ContextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="17815-246">-ContextNamespace \<String></span></span>        | <span data-ttu-id="17815-247">要用于生成的类的命名空间 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="17815-247">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="17815-248">注意：重写 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="17815-248">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="17815-249">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-249">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="17815-250">-上下文 \<String></span><span class="sxs-lookup"><span data-stu-id="17815-250">-Context \<String></span></span>                 | <span data-ttu-id="17815-251">`DbContext`要生成的类的名称。</span><span class="sxs-lookup"><span data-stu-id="17815-251">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="17815-252">-架构 \<String[]></span><span class="sxs-lookup"><span data-stu-id="17815-252">-Schemas \<String[]></span></span>               | <span data-ttu-id="17815-253">要为其生成实体类型的表的架构。</span><span class="sxs-lookup"><span data-stu-id="17815-253">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="17815-254">如果省略此参数，则包括所有架构。</span><span class="sxs-lookup"><span data-stu-id="17815-254">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="17815-255">-表 \<String[]></span><span class="sxs-lookup"><span data-stu-id="17815-255">-Tables \<String[]></span></span>                | <span data-ttu-id="17815-256">要为其生成实体类型的表。</span><span class="sxs-lookup"><span data-stu-id="17815-256">The tables to generate entity types for.</span></span> <span data-ttu-id="17815-257">如果省略此参数，则包括所有表。</span><span class="sxs-lookup"><span data-stu-id="17815-257">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="17815-258">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="17815-258">-DataAnnotations</span></span>                   | <span data-ttu-id="17815-259">使用属性可在可能) 的情况下配置模型 (。</span><span class="sxs-lookup"><span data-stu-id="17815-259">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="17815-260">如果省略此参数，则只使用 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="17815-260">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="17815-261">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="17815-261">-UseDatabaseNames</span></span>                  | <span data-ttu-id="17815-262">使用表和列的名称与数据库中显示的名称完全相同。</span><span class="sxs-lookup"><span data-stu-id="17815-262">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="17815-263">如果省略此参数，则更改数据库名称以更严格地符合 c # 名称样式约定。</span><span class="sxs-lookup"><span data-stu-id="17815-263">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="17815-264">-Force</span><span class="sxs-lookup"><span data-stu-id="17815-264">-Force</span></span>                             | <span data-ttu-id="17815-265">覆盖现有文件。</span><span class="sxs-lookup"><span data-stu-id="17815-265">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="17815-266">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="17815-266">-NoOnConfiguring</span></span>                   | <span data-ttu-id="17815-267">不生成 `DbContext.OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="17815-267">Don't generate `DbContext.OnConfiguring`.</span></span> <span data-ttu-id="17815-268">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-268">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                         |
| <span data-ttu-id="17815-269">-NoPluralize</span><span class="sxs-lookup"><span data-stu-id="17815-269">-NoPluralize</span></span>                       | <span data-ttu-id="17815-270">请勿使用复数化程序。</span><span class="sxs-lookup"><span data-stu-id="17815-270">Don't use the pluralizer.</span></span> <span data-ttu-id="17815-271">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-271">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                                         |

<span data-ttu-id="17815-272">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-272">The [common parameters](#common-parameters) are listed above.</span></span>

<span data-ttu-id="17815-273">示例：</span><span class="sxs-lookup"><span data-stu-id="17815-273">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="17815-274">示例：仅基架选定的表，并在具有指定名称和命名空间的单独文件夹中创建上下文：</span><span class="sxs-lookup"><span data-stu-id="17815-274">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-dbcontext"></a><span data-ttu-id="17815-275">Script-DbContext</span><span class="sxs-lookup"><span data-stu-id="17815-275">Script-DbContext</span></span>

<span data-ttu-id="17815-276">从 DbContext 生成 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="17815-276">Generates a SQL script from the DbContext.</span></span> <span data-ttu-id="17815-277">绕过任何迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-277">Bypasses any migrations.</span></span> <span data-ttu-id="17815-278">在 EF Core 3.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-278">Added in EF Core 3.0.</span></span>

<span data-ttu-id="17815-279">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-279">Parameters:</span></span>

| <span data-ttu-id="17815-280">参数</span><span class="sxs-lookup"><span data-stu-id="17815-280">Parameter</span></span>                      | <span data-ttu-id="17815-281">说明</span><span class="sxs-lookup"><span data-stu-id="17815-281">Description</span></span>                      |
| ------------------------------ | -------------------------------- |
| <span data-ttu-id="17815-282"><nobr>-输出 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-282"><nobr>-Output \<String></nobr></span></span> | <span data-ttu-id="17815-283">要向其写入结果的文件。</span><span class="sxs-lookup"><span data-stu-id="17815-283">The file to write the result to.</span></span> |

<span data-ttu-id="17815-284">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-284">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="script-migration"></a><span data-ttu-id="17815-285">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="17815-285">Script-Migration</span></span>

<span data-ttu-id="17815-286">生成一个 SQL 脚本，该脚本将所选迁移中的所有更改应用于另一个选定的迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-286">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="17815-287">参数：</span><span class="sxs-lookup"><span data-stu-id="17815-287">Parameters:</span></span>

| <span data-ttu-id="17815-288">参数</span><span class="sxs-lookup"><span data-stu-id="17815-288">Parameter</span></span>                    | <span data-ttu-id="17815-289">说明</span><span class="sxs-lookup"><span data-stu-id="17815-289">Description</span></span>                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="17815-290">*-来自*\<String></span><span class="sxs-lookup"><span data-stu-id="17815-290">*-From* \<String></span></span>            | <span data-ttu-id="17815-291">开始迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-291">The starting migration.</span></span> <span data-ttu-id="17815-292">可以按名称或 ID 识别迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-292">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="17815-293">数字0是一个特殊情况，表示在 *第一次迁移之前*。</span><span class="sxs-lookup"><span data-stu-id="17815-293">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="17815-294">默认值为 0。</span><span class="sxs-lookup"><span data-stu-id="17815-294">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="17815-295">*-到*\<String></span><span class="sxs-lookup"><span data-stu-id="17815-295">*-To* \<String></span></span>              | <span data-ttu-id="17815-296">结束迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-296">The ending migration.</span></span> <span data-ttu-id="17815-297">默认为上次迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-297">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="17815-298">-幂等</span><span class="sxs-lookup"><span data-stu-id="17815-298">-Idempotent</span></span>                  | <span data-ttu-id="17815-299">生成可用于任何迁移的数据库的脚本。</span><span class="sxs-lookup"><span data-stu-id="17815-299">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="17815-300"><nobr>-NoTransactions</nobr></span><span class="sxs-lookup"><span data-stu-id="17815-300"><nobr>-NoTransactions</nobr></span></span> | <span data-ttu-id="17815-301">不生成 SQL transaction 语句。</span><span class="sxs-lookup"><span data-stu-id="17815-301">Don't generate SQL transaction statements.</span></span> <span data-ttu-id="17815-302">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-302">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="17815-303">-输出 \<String></span><span class="sxs-lookup"><span data-stu-id="17815-303">-Output \<String></span></span>            | <span data-ttu-id="17815-304">要向其写入结果的文件。</span><span class="sxs-lookup"><span data-stu-id="17815-304">The file to write the result to.</span></span> <span data-ttu-id="17815-305">如果省略此参数，则会在创建应用的运行时文件所在的同一文件夹中创建具有生成名称的文件，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。</span><span class="sxs-lookup"><span data-stu-id="17815-305">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

<span data-ttu-id="17815-306">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-306">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="17815-307">To、From 和 Output 参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="17815-307">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="17815-308">以下示例使用迁移名称创建用于 InitialCreate 迁移的脚本。</span><span class="sxs-lookup"><span data-stu-id="17815-308">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="17815-309">以下示例使用迁移 ID，为 InitialCreate 迁移后的所有迁移创建一个脚本。</span><span class="sxs-lookup"><span data-stu-id="17815-309">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="17815-310">Update-Database</span><span class="sxs-lookup"><span data-stu-id="17815-310">Update-Database</span></span>

<span data-ttu-id="17815-311">将数据库更新到上次迁移或指定迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-311">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="17815-312">参数</span><span class="sxs-lookup"><span data-stu-id="17815-312">Parameter</span></span>                           | <span data-ttu-id="17815-313">说明</span><span class="sxs-lookup"><span data-stu-id="17815-313">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="17815-314"><nobr>*-迁移*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-314"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="17815-315">目标迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-315">The target migration.</span></span> <span data-ttu-id="17815-316">可以按名称或 ID 识别迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-316">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="17815-317">数字0是一种特殊情况，表示在 *第一次迁移之前* ，并导致还原所有迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-317">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="17815-318">如果未指定迁移，则该命令默认为上一次迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-318">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="17815-319"><nobr>-连接 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="17815-319"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="17815-320">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="17815-320">The connection string to the database.</span></span> <span data-ttu-id="17815-321">默认为或中指定的 `AddDbContext` 一个 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="17815-321">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> <span data-ttu-id="17815-322">在 EF Core 5.0 中添加。</span><span class="sxs-lookup"><span data-stu-id="17815-322">Added in EF Core 5.0.</span></span>                                                                                                                                |

<span data-ttu-id="17815-323">上面列出了 [常见参数](#common-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="17815-323">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="17815-324">迁移参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="17815-324">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="17815-325">下面的示例将还原所有迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-325">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="17815-326">下面的示例将数据库更新为指定的迁移。</span><span class="sxs-lookup"><span data-stu-id="17815-326">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="17815-327">第一个使用迁移名称，第二个使用迁移 ID 和指定的连接：</span><span class="sxs-lookup"><span data-stu-id="17815-327">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="17815-328">其他资源</span><span class="sxs-lookup"><span data-stu-id="17815-328">Additional resources</span></span>

* [<span data-ttu-id="17815-329">迁移</span><span class="sxs-lookup"><span data-stu-id="17815-329">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="17815-330">反向工程</span><span class="sxs-lookup"><span data-stu-id="17815-330">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
