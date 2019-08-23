---
title: EF Core 工具参考 （程序包管理器控制台）-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: cb05e3fb66adf96f8a6778711a76520d0be24c71
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419765"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="4f391-102">Entity Framework Core 工具引用-Visual Studio 中的包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="4f391-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="4f391-103">Entity Framework Core 的包管理器控制台 (PMC) 工具执行设计时开发任务。</span><span class="sxs-lookup"><span data-stu-id="4f391-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="4f391-104">例如，创建方法[迁移](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)、 应用迁移，并为基于现有数据库的模型生成代码。</span><span class="sxs-lookup"><span data-stu-id="4f391-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="4f391-105">在 Visual Studio 中使用的内部运行命令[程序包管理器控制台](/nuget/tools/package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="4f391-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="4f391-106">这些工具同时适用于 .NET Framework 和 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="4f391-107">如果不使用 Visual Studio，我们建议[EF Core 命令行工具](dotnet.md)相反。</span><span class="sxs-lookup"><span data-stu-id="4f391-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="4f391-108">CLI 工具是跨平台，且在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="4f391-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="4f391-109">安装工具</span><span class="sxs-lookup"><span data-stu-id="4f391-109">Installing the tools</span></span>

<span data-ttu-id="4f391-110">ASP.NET Core 2.1 + 和早期版本或其他项目类型之间存在差异的安装和更新工具的过程。</span><span class="sxs-lookup"><span data-stu-id="4f391-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="4f391-111">ASP.NET Core 2.1 和更高版本</span><span class="sxs-lookup"><span data-stu-id="4f391-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="4f391-112">这些工具会自动包含在 ASP.NET Core 2.1 + 项目，因为`Microsoft.EntityFrameworkCore.Tools`包包含在[Microsoft.AspNetCore.App 元包](/aspnet/core/fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="4f391-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4f391-113">因此，无需执行任何操作来安装工具，但可以为：</span><span class="sxs-lookup"><span data-stu-id="4f391-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>
* <span data-ttu-id="4f391-114">在新项目中使用的工具之前还原包。</span><span class="sxs-lookup"><span data-stu-id="4f391-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="4f391-115">安装包更新到较新版本的工具。</span><span class="sxs-lookup"><span data-stu-id="4f391-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="4f391-116">若要确保获得最新版本的工具，我们建议您以下步骤：</span><span class="sxs-lookup"><span data-stu-id="4f391-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="4f391-117">编辑你 *.csproj*文件，并添加一行以指定的最新版本[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)包。</span><span class="sxs-lookup"><span data-stu-id="4f391-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="4f391-118">例如， *.csproj*文件可能包含`ItemGroup`如下所示：</span><span class="sxs-lookup"><span data-stu-id="4f391-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="4f391-119">更新的工具时你收到一条消息，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="4f391-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="4f391-120">EF Core 工具版本"2.1.1-rtm-30846"为早于的运行时"2.1.3-rtm-32065"。</span><span class="sxs-lookup"><span data-stu-id="4f391-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="4f391-121">更新适用于最新功能和 bug 修补程序的工具。</span><span class="sxs-lookup"><span data-stu-id="4f391-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="4f391-122">若要更新的工具：</span><span class="sxs-lookup"><span data-stu-id="4f391-122">To update the tools:</span></span>
* <span data-ttu-id="4f391-123">安装最新的.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="4f391-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="4f391-124">Visual Studio 更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="4f391-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="4f391-125">编辑 *.csproj*文件以使其包括到最新工具包，包引用，如前面所示。</span><span class="sxs-lookup"><span data-stu-id="4f391-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="4f391-126">其他版本和项目类型</span><span class="sxs-lookup"><span data-stu-id="4f391-126">Other versions and project types</span></span>

<span data-ttu-id="4f391-127">通过在运行以下命令安装程序包管理器控制台工具**程序包管理器控制台**:</span><span class="sxs-lookup"><span data-stu-id="4f391-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="4f391-128">通过在运行以下命令更新的工具**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="4f391-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="4f391-129">验证安装</span><span class="sxs-lookup"><span data-stu-id="4f391-129">Verify the installation</span></span>

<span data-ttu-id="4f391-130">验证通过运行以下命令安装这些工具：</span><span class="sxs-lookup"><span data-stu-id="4f391-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="4f391-131">输出如下所示 （它不会让您正在使用的工具版本）：</span><span class="sxs-lookup"><span data-stu-id="4f391-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

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

## <a name="using-the-tools"></a><span data-ttu-id="4f391-132">使用的工具</span><span class="sxs-lookup"><span data-stu-id="4f391-132">Using the tools</span></span>

<span data-ttu-id="4f391-133">之前使用的工具：</span><span class="sxs-lookup"><span data-stu-id="4f391-133">Before using the tools:</span></span>
* <span data-ttu-id="4f391-134">了解目标和启动项目之间的差异。</span><span class="sxs-lookup"><span data-stu-id="4f391-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="4f391-135">了解如何使用.NET Standard 类库的工具。</span><span class="sxs-lookup"><span data-stu-id="4f391-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="4f391-136">对于 ASP.NET Core 项目设置的环境。</span><span class="sxs-lookup"><span data-stu-id="4f391-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="4f391-137">目标和启动项目</span><span class="sxs-lookup"><span data-stu-id="4f391-137">Target and startup project</span></span>

<span data-ttu-id="4f391-138">命令是指*项目*和一个*启动项目*。</span><span class="sxs-lookup"><span data-stu-id="4f391-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="4f391-139">*项目*也称为*目标项目*因为它是命令添加或删除文件的位置。</span><span class="sxs-lookup"><span data-stu-id="4f391-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="4f391-140">默认情况下**默认项目**中所选**程序包管理器控制台**是目标项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="4f391-141">可以通过使用作为目标项目中指定一个不同的项目<nobr> `--project` </nobr>选项。</span><span class="sxs-lookup"><span data-stu-id="4f391-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="4f391-142">*启动项目*是指的工具生成和运行。</span><span class="sxs-lookup"><span data-stu-id="4f391-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="4f391-143">这些工具必须在设计时，获取有关该项目，如数据库连接字符串和模型的配置信息执行应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="4f391-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="4f391-144">默认情况下**启动项目**中**解决方案资源管理器**是启动项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="4f391-145">您可以通过使用不同的项目指定为启动项目<nobr> `--startup-project` </nobr>选项。</span><span class="sxs-lookup"><span data-stu-id="4f391-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="4f391-146">启动项目和目标项目通常是在同一个项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="4f391-147">它们的单独的项目的典型情况是当：</span><span class="sxs-lookup"><span data-stu-id="4f391-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="4f391-148">EF Core 上下文和实体类是在.NET Core 类库中。</span><span class="sxs-lookup"><span data-stu-id="4f391-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="4f391-149">.NET Core 控制台应用程序或 web 应用程序引用的类库。</span><span class="sxs-lookup"><span data-stu-id="4f391-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="4f391-150">此外，还可以向[迁移代码置于独立于 EF Core 上下文类库](xref:core/managing-schemas/migrations/projects)。</span><span class="sxs-lookup"><span data-stu-id="4f391-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="4f391-151">其他目标框架</span><span class="sxs-lookup"><span data-stu-id="4f391-151">Other target frameworks</span></span>

<span data-ttu-id="4f391-152">包管理器控制台工具适用于.NET Core 或.NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="4f391-153">EF Core 模型具有.NET Standard 类库中的应用可能不具有.NET Core 或.NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="4f391-154">例如，这是 Xamarin 和通用 Windows 平台应用，则返回 true。</span><span class="sxs-lookup"><span data-stu-id="4f391-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="4f391-155">在这种情况下，可以创建其唯一用途是作为启动项目的工具的.NET Core 或.NET Framework 控制台应用项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="4f391-156">项目可以是虚拟项目不包含实际代码&mdash;只需为工具提供一个目标。</span><span class="sxs-lookup"><span data-stu-id="4f391-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="4f391-157">为什么是虚拟的项目所需？</span><span class="sxs-lookup"><span data-stu-id="4f391-157">Why is a dummy project required?</span></span> <span data-ttu-id="4f391-158">如前文所述，这些工具必须在设计时执行应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="4f391-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="4f391-159">若要做到这一点，他们需要使用.NET Core 或.NET Framework 运行时。</span><span class="sxs-lookup"><span data-stu-id="4f391-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="4f391-160">EF Core 模型在面向.NET Core 或.NET Framework 的项目时，EF Core 工具借用运行时从该项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="4f391-161">如果在 EF Core 模型处于.NET Standard 类库，他们无法做到这一点。</span><span class="sxs-lookup"><span data-stu-id="4f391-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="4f391-162">.NET Standard 并不实际的.NET 实现;它是一种规范的一组.NET 实现必须支持的 Api。</span><span class="sxs-lookup"><span data-stu-id="4f391-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="4f391-163">因此.NET Standard 是不够的 EF Core 工具来执行应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="4f391-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="4f391-164">创建要用作启动项目的虚拟项目提供了工具可以在其中加载.NET Standard 类库的具体目标平台。</span><span class="sxs-lookup"><span data-stu-id="4f391-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="4f391-165">ASP.NET Core 环境</span><span class="sxs-lookup"><span data-stu-id="4f391-165">ASP.NET Core environment</span></span>

<span data-ttu-id="4f391-166">若要指定用于 ASP.NET Core 项目的环境，请设置**env:ASPNETCORE_ENVIRONMENT**之前运行命令。</span><span class="sxs-lookup"><span data-stu-id="4f391-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="4f391-167">通用参数</span><span class="sxs-lookup"><span data-stu-id="4f391-167">Common parameters</span></span>

<span data-ttu-id="4f391-168">下表显示了所共有的所有 EF Core 命令参数：</span><span class="sxs-lookup"><span data-stu-id="4f391-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="4f391-169">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-169">Parameter</span></span>                 | <span data-ttu-id="4f391-170">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4f391-171">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-171">-Context \<String></span></span>        | <span data-ttu-id="4f391-172">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="4f391-172">The `DbContext` class to use.</span></span> <span data-ttu-id="4f391-173">唯一或完全限定的命名空间的类名称。</span><span class="sxs-lookup"><span data-stu-id="4f391-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="4f391-174">如果省略此参数，EF Core 将查找上下文类。</span><span class="sxs-lookup"><span data-stu-id="4f391-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="4f391-175">如果有多个上下文类，此参数是必需的。</span><span class="sxs-lookup"><span data-stu-id="4f391-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="4f391-176">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-176">-Project \<String></span></span>        | <span data-ttu-id="4f391-177">目标项目中。</span><span class="sxs-lookup"><span data-stu-id="4f391-177">The target project.</span></span> <span data-ttu-id="4f391-178">如果省略此参数，则**默认项目**有关**程序包管理器控制台**用作目标项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="4f391-179">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-179">-StartupProject \<String></span></span> | <span data-ttu-id="4f391-180">启动项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-180">The startup project.</span></span> <span data-ttu-id="4f391-181">如果省略此参数，则**启动项目**中**解决方案属性**用作目标项目。</span><span class="sxs-lookup"><span data-stu-id="4f391-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="4f391-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="4f391-182">-Verbose</span></span>                  | <span data-ttu-id="4f391-183">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="4f391-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="4f391-184">若要显示有关命令的帮助信息，请使用 PowerShell 的`Get-Help`命令。</span><span class="sxs-lookup"><span data-stu-id="4f391-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="4f391-185">Context、Project 和 StartupProject 参数支持tab补全。</span><span class="sxs-lookup"><span data-stu-id="4f391-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="4f391-186">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="4f391-186">Add-Migration</span></span>

<span data-ttu-id="4f391-187">添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-187">Adds a new migration.</span></span>

<span data-ttu-id="4f391-188">参数：</span><span class="sxs-lookup"><span data-stu-id="4f391-188">Parameters:</span></span>

| <span data-ttu-id="4f391-189">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-189">Parameter</span></span>                         | <span data-ttu-id="4f391-190">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4f391-191"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="4f391-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="4f391-192">迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4f391-192">The name of the migration.</span></span> <span data-ttu-id="4f391-193">这是位置参数，是必需的。</span><span class="sxs-lookup"><span data-stu-id="4f391-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="4f391-194"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="4f391-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="4f391-195">目录 （及其子命名空间） 来使用。</span><span class="sxs-lookup"><span data-stu-id="4f391-195">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="4f391-196">路径是相对于目标项目目录。</span><span class="sxs-lookup"><span data-stu-id="4f391-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="4f391-197">默认值为"迁移"。</span><span class="sxs-lookup"><span data-stu-id="4f391-197">Defaults to "Migrations".</span></span> |

## <a name="drop-database"></a><span data-ttu-id="4f391-198">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="4f391-198">Drop-Database</span></span>

<span data-ttu-id="4f391-199">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="4f391-199">Drops the database.</span></span>

<span data-ttu-id="4f391-200">参数：</span><span class="sxs-lookup"><span data-stu-id="4f391-200">Parameters:</span></span>

| <span data-ttu-id="4f391-201">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-201">Parameter</span></span> | <span data-ttu-id="4f391-202">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-202">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="4f391-203">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="4f391-203">-WhatIf</span></span>   | <span data-ttu-id="4f391-204">显示哪个数据库会被丢弃，但没有删除它。</span><span class="sxs-lookup"><span data-stu-id="4f391-204">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="4f391-205">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="4f391-205">Get-DbContext</span></span>

<span data-ttu-id="4f391-206">获取有关的信息`DbContext`类型。</span><span class="sxs-lookup"><span data-stu-id="4f391-206">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="4f391-207">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="4f391-207">Remove-Migration</span></span>

<span data-ttu-id="4f391-208">删除 （请回滚迁移已完成的代码更改） 的最后一个迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-208">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="4f391-209">参数：</span><span class="sxs-lookup"><span data-stu-id="4f391-209">Parameters:</span></span>

| <span data-ttu-id="4f391-210">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-210">Parameter</span></span> | <span data-ttu-id="4f391-211">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-211">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="4f391-212">-Force</span><span class="sxs-lookup"><span data-stu-id="4f391-212">-Force</span></span>    | <span data-ttu-id="4f391-213">还原迁移 （请回滚已应用到数据库的更改）。</span><span class="sxs-lookup"><span data-stu-id="4f391-213">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="4f391-214">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="4f391-214">Scaffold-DbContext</span></span>

<span data-ttu-id="4f391-215">为生成代码`DbContext`和数据库的实体类型。</span><span class="sxs-lookup"><span data-stu-id="4f391-215">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="4f391-216">为了使`Scaffold-DbContext`若要生成的实体类型，数据库表必须具有主键。</span><span class="sxs-lookup"><span data-stu-id="4f391-216">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="4f391-217">参数：</span><span class="sxs-lookup"><span data-stu-id="4f391-217">Parameters:</span></span>

| <span data-ttu-id="4f391-218">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-218">Parameter</span></span>                          | <span data-ttu-id="4f391-219">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-219">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4f391-220"><nobr>-连接\<字符串 ></nobr></span><span class="sxs-lookup"><span data-stu-id="4f391-220"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="4f391-221">数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4f391-221">The connection string to the database.</span></span> <span data-ttu-id="4f391-222">对于 ASP.NET Core 2.x 项目，值可以是*名称 =\<的连接字符串名称 >* 。</span><span class="sxs-lookup"><span data-stu-id="4f391-222">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="4f391-223">在这种情况下名称来自于为项目设置的配置源。</span><span class="sxs-lookup"><span data-stu-id="4f391-223">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="4f391-224">这是位置参数，是必需的。</span><span class="sxs-lookup"><span data-stu-id="4f391-224">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="4f391-225"><nobr>-Provider \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="4f391-225"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="4f391-226">要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="4f391-226">The provider to use.</span></span> <span data-ttu-id="4f391-227">通常这是 NuGet 包的名称为例： `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="4f391-227">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="4f391-228">这是位置参数，是必需的。</span><span class="sxs-lookup"><span data-stu-id="4f391-228">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="4f391-229">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-229">-OutputDir \<String></span></span>               | <span data-ttu-id="4f391-230">要将文件放入的目录。</span><span class="sxs-lookup"><span data-stu-id="4f391-230">The directory to put files in.</span></span> <span data-ttu-id="4f391-231">路径是相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="4f391-231">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="4f391-232">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-232">-ContextDir \<String></span></span>              | <span data-ttu-id="4f391-233">要放置的目录`DbContext`文件中。</span><span class="sxs-lookup"><span data-stu-id="4f391-233">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="4f391-234">路径是相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="4f391-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="4f391-235">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-235">-Context \<String></span></span>                 | <span data-ttu-id="4f391-236">名称`DbContext`类生成。</span><span class="sxs-lookup"><span data-stu-id="4f391-236">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="4f391-237">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="4f391-237">-Schemas \<String[]></span></span>               | <span data-ttu-id="4f391-238">要生成的实体类型的表架构。</span><span class="sxs-lookup"><span data-stu-id="4f391-238">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="4f391-239">如果省略此参数，则包括所有架构。</span><span class="sxs-lookup"><span data-stu-id="4f391-239">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="4f391-240">-表\<String [] ></span><span class="sxs-lookup"><span data-stu-id="4f391-240">-Tables \<String[]></span></span>                | <span data-ttu-id="4f391-241">要生成的实体类型的表。</span><span class="sxs-lookup"><span data-stu-id="4f391-241">The tables to generate entity types for.</span></span> <span data-ttu-id="4f391-242">如果省略此参数，则包括所有表。</span><span class="sxs-lookup"><span data-stu-id="4f391-242">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="4f391-243">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="4f391-243">-DataAnnotations</span></span>                   | <span data-ttu-id="4f391-244">特性用于将模型配置 （如果可能）。</span><span class="sxs-lookup"><span data-stu-id="4f391-244">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="4f391-245">如果省略此参数，则使用仅 fluent API。</span><span class="sxs-lookup"><span data-stu-id="4f391-245">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="4f391-246">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="4f391-246">-UseDatabaseNames</span></span>                  | <span data-ttu-id="4f391-247">在数据库中显示的完全相同，请使用表和列的名称。</span><span class="sxs-lookup"><span data-stu-id="4f391-247">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="4f391-248">如果省略此参数，则会更改数据库名称，使其更紧密地符合 C# 名称样式约定。</span><span class="sxs-lookup"><span data-stu-id="4f391-248">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="4f391-249">-Force</span><span class="sxs-lookup"><span data-stu-id="4f391-249">-Force</span></span>                             | <span data-ttu-id="4f391-250">覆盖现有文件。</span><span class="sxs-lookup"><span data-stu-id="4f391-250">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="4f391-251">示例:</span><span class="sxs-lookup"><span data-stu-id="4f391-251">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="4f391-252">搭建基架以选定的表，并具有指定名称的单独文件夹中创建上下文的示例：</span><span class="sxs-lookup"><span data-stu-id="4f391-252">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a><span data-ttu-id="4f391-253">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="4f391-253">Script-Migration</span></span>

<span data-ttu-id="4f391-254">生成应用的所有更改从一个所选迁移到另一个所选的迁移的 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="4f391-254">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="4f391-255">参数：</span><span class="sxs-lookup"><span data-stu-id="4f391-255">Parameters:</span></span>

| <span data-ttu-id="4f391-256">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-256">Parameter</span></span>                | <span data-ttu-id="4f391-257">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-257">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4f391-258">*-From* \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-258">*-From* \<String></span></span>        | <span data-ttu-id="4f391-259">开始迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-259">The starting migration.</span></span> <span data-ttu-id="4f391-260">可能会被标识迁移，按名称或 id。</span><span class="sxs-lookup"><span data-stu-id="4f391-260">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="4f391-261">数字 0 是一种特殊情况，意味着*第一次迁移之前*。</span><span class="sxs-lookup"><span data-stu-id="4f391-261">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="4f391-262">默认值为 0。</span><span class="sxs-lookup"><span data-stu-id="4f391-262">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="4f391-263">*-To* \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-263">*-To* \<String></span></span>          | <span data-ttu-id="4f391-264">结束的迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-264">The ending migration.</span></span> <span data-ttu-id="4f391-265">默认的最后一个迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-265">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="4f391-266"><nobr>-Idempotent</nobr></span><span class="sxs-lookup"><span data-stu-id="4f391-266"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="4f391-267">生成脚本，可以在任何迁移的数据库上使用。</span><span class="sxs-lookup"><span data-stu-id="4f391-267">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="4f391-268">-Output \<String></span><span class="sxs-lookup"><span data-stu-id="4f391-268">-Output \<String></span></span>        | <span data-ttu-id="4f391-269">要将结果写入的文件。</span><span class="sxs-lookup"><span data-stu-id="4f391-269">The file to write the result to.</span></span> <span data-ttu-id="4f391-270">如果省略此参数，创建应用程序的运行时文件时，例如使用相同的文件夹中生成的名称创建该文件： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* 。</span><span class="sxs-lookup"><span data-stu-id="4f391-270">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="4f391-271">To、From 和 Output 参数支持tab补全。</span><span class="sxs-lookup"><span data-stu-id="4f391-271">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="4f391-272">以下示例创建一个脚本，以便使用迁移名称 InitialCreate 迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-272">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="4f391-273">下面的示例创建一个脚本，以便所有迁移 InitialCreate 迁移之后，使用迁移 id。</span><span class="sxs-lookup"><span data-stu-id="4f391-273">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="4f391-274">Update-Database</span><span class="sxs-lookup"><span data-stu-id="4f391-274">Update-Database</span></span>

<span data-ttu-id="4f391-275">更新数据库，到最后一个迁移或指定的迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-275">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="4f391-276">参数</span><span class="sxs-lookup"><span data-stu-id="4f391-276">Parameter</span></span>                           | <span data-ttu-id="4f391-277">描述</span><span class="sxs-lookup"><span data-stu-id="4f391-277">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="4f391-278"><nobr> *-Migration* \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="4f391-278"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="4f391-279">目标迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-279">The target migration.</span></span> <span data-ttu-id="4f391-280">可能会被标识迁移，按名称或 id。</span><span class="sxs-lookup"><span data-stu-id="4f391-280">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="4f391-281">数字 0 是一种特殊情况，意味着*第一次迁移之前*并导致所有迁移操作，从而还原。</span><span class="sxs-lookup"><span data-stu-id="4f391-281">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="4f391-282">如果指定无需迁移，则此命令的最后一个默认迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-282">If no migration is specified, the command defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="4f391-283">Migration 参数支持tab补全。</span><span class="sxs-lookup"><span data-stu-id="4f391-283">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="4f391-284">下面的示例将恢复所有迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-284">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="4f391-285">下面的示例将数据库更新为指定的迁移。</span><span class="sxs-lookup"><span data-stu-id="4f391-285">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="4f391-286">第一个示例使用迁移名称和另一个使用迁移 ID:</span><span class="sxs-lookup"><span data-stu-id="4f391-286">The first uses the migration name and the second uses the migration ID:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```

## <a name="additional-resources"></a><span data-ttu-id="4f391-287">其他资源</span><span class="sxs-lookup"><span data-stu-id="4f391-287">Additional resources</span></span>

* [<span data-ttu-id="4f391-288">迁移</span><span class="sxs-lookup"><span data-stu-id="4f391-288">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="4f391-289">反向工程</span><span class="sxs-lookup"><span data-stu-id="4f391-289">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
