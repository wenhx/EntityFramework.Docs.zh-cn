---
title: EF Core 工具参考（程序包管理器控制台）-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 3893d561ccb7d97f3d9c25d9ea66509ad0f3da75
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370588"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="1894f-102">Entity Framework Core 工具参考-Visual Studio 中的包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="1894f-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="1894f-103">适用于 Entity Framework Core 的包管理器控制台（PMC）工具执行设计时开发任务。</span><span class="sxs-lookup"><span data-stu-id="1894f-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="1894f-104">例如，他们基于现有数据库创建[迁移](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)、应用迁移和生成模型的代码。</span><span class="sxs-lookup"><span data-stu-id="1894f-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="1894f-105">命令使用[包管理器控制台](/nuget/tools/package-manager-console)在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="1894f-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="1894f-106">这些工具同时适用于 .NET Framework 和 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="1894f-107">如果未使用 Visual Studio，则建议改为使用[EF Core 命令行工具](dotnet.md)。</span><span class="sxs-lookup"><span data-stu-id="1894f-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="1894f-108">.NET Core CLI 工具是跨平台的，在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="1894f-108">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="1894f-109">安装工具</span><span class="sxs-lookup"><span data-stu-id="1894f-109">Installing the tools</span></span>

<span data-ttu-id="1894f-110">用于安装和更新这些工具的过程在 ASP.NET Core 2.1 及更早版本或其他项目类型之间有所不同。</span><span class="sxs-lookup"><span data-stu-id="1894f-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="1894f-111">ASP.NET Core 版本2.1 及更高版本</span><span class="sxs-lookup"><span data-stu-id="1894f-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="1894f-112">工具将自动包含在 ASP.NET Core 2.1 + 项目中，因为 `Microsoft.EntityFrameworkCore.Tools` 包包含在[AspNetCore 元包](/aspnet/core/fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="1894f-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="1894f-113">因此，您无需执行任何操作来安装这些工具，但必须执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1894f-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="1894f-114">在新项目中使用这些工具之前还原包。</span><span class="sxs-lookup"><span data-stu-id="1894f-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="1894f-115">安装包以将工具更新到较新版本。</span><span class="sxs-lookup"><span data-stu-id="1894f-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="1894f-116">为了确保获得最新版本的工具，我们建议您也执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="1894f-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="1894f-117">编辑 *.csproj*文件，并添加一行以指定最新版本的[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)包。</span><span class="sxs-lookup"><span data-stu-id="1894f-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="1894f-118">例如， *.csproj*文件可能包含如下所示 `ItemGroup` 的：</span><span class="sxs-lookup"><span data-stu-id="1894f-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="1894f-119">在收到类似于以下示例的消息时更新工具：</span><span class="sxs-lookup"><span data-stu-id="1894f-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="1894f-120">EF Core 工具版本 "2.1.1-30846" 早于运行时 "2.1.3-32065"。</span><span class="sxs-lookup"><span data-stu-id="1894f-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="1894f-121">更新工具以获取最新功能和 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="1894f-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="1894f-122">更新工具：</span><span class="sxs-lookup"><span data-stu-id="1894f-122">To update the tools:</span></span>

* <span data-ttu-id="1894f-123">安装最新的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="1894f-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="1894f-124">将 Visual Studio 更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="1894f-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="1894f-125">编辑 *.csproj*文件，使其包含对最新工具包的包引用，如前文所述。</span><span class="sxs-lookup"><span data-stu-id="1894f-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="1894f-126">其他版本和项目类型</span><span class="sxs-lookup"><span data-stu-id="1894f-126">Other versions and project types</span></span>

<span data-ttu-id="1894f-127">在**Package Manager console**中运行以下命令，安装包管理器控制台工具：</span><span class="sxs-lookup"><span data-stu-id="1894f-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="1894f-128">在**Package Manager Console**中运行以下命令，更新这些工具。</span><span class="sxs-lookup"><span data-stu-id="1894f-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="1894f-129">验证安装</span><span class="sxs-lookup"><span data-stu-id="1894f-129">Verify the installation</span></span>

<span data-ttu-id="1894f-130">通过运行以下命令验证是否已安装这些工具：</span><span class="sxs-lookup"><span data-stu-id="1894f-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="1894f-131">输出如下所示（不告诉您正在使用的工具的版本）：</span><span class="sxs-lookup"><span data-stu-id="1894f-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="1894f-132">使用工具</span><span class="sxs-lookup"><span data-stu-id="1894f-132">Using the tools</span></span>

<span data-ttu-id="1894f-133">使用这些工具之前：</span><span class="sxs-lookup"><span data-stu-id="1894f-133">Before using the tools:</span></span>

* <span data-ttu-id="1894f-134">了解目标项目和启动项目之间的差异。</span><span class="sxs-lookup"><span data-stu-id="1894f-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="1894f-135">了解如何将工具与 .NET Standard 类库一起使用。</span><span class="sxs-lookup"><span data-stu-id="1894f-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="1894f-136">对于 ASP.NET Core 项目，请设置环境。</span><span class="sxs-lookup"><span data-stu-id="1894f-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="1894f-137">目标和启动项目</span><span class="sxs-lookup"><span data-stu-id="1894f-137">Target and startup project</span></span>

<span data-ttu-id="1894f-138">命令引用*项目*和*启动项目*。</span><span class="sxs-lookup"><span data-stu-id="1894f-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="1894f-139">该*项目*也称为*目标项目*，因为它是命令在其中添加或删除文件的位置。</span><span class="sxs-lookup"><span data-stu-id="1894f-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="1894f-140">默认情况下，在 "**程序包管理器控制台**" 中选择的**默认项目**是目标项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="1894f-141">可以使用选项指定其他项目作为目标项目 <nobr>`--project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="1894f-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="1894f-142">*启动项目*是工具生成和运行的项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="1894f-143">这些工具必须在设计时执行应用程序代码，以获取有关项目的信息，例如数据库连接字符串和模型的配置。</span><span class="sxs-lookup"><span data-stu-id="1894f-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="1894f-144">默认情况下，**解决方案资源管理器**中的**启动项目**是启动项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="1894f-145">您可以使用选项指定其他项目作为启动项目 <nobr>`--startup-project`</nobr> 。</span><span class="sxs-lookup"><span data-stu-id="1894f-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="1894f-146">启动项目和目标项目通常是同一个项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="1894f-147">它们是不同的项目的典型方案是：</span><span class="sxs-lookup"><span data-stu-id="1894f-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="1894f-148">EF Core 的上下文和实体类位于 .NET Core 类库中。</span><span class="sxs-lookup"><span data-stu-id="1894f-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="1894f-149">.NET Core 控制台应用程序或 web 应用程序引用类库。</span><span class="sxs-lookup"><span data-stu-id="1894f-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="1894f-150">还可以[将迁移代码放在与 EF Core 上下文分离](xref:core/managing-schemas/migrations/projects)的类库中。</span><span class="sxs-lookup"><span data-stu-id="1894f-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="1894f-151">其他目标框架</span><span class="sxs-lookup"><span data-stu-id="1894f-151">Other target frameworks</span></span>

<span data-ttu-id="1894f-152">包管理器控制台工具适用于 .NET Core 或 .NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="1894f-153">.NET Standard 类库中具有 EF Core 模型的应用可能没有 .NET Core 或 .NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="1894f-154">例如，这适用于 Xamarin 和通用 Windows 平台应用。</span><span class="sxs-lookup"><span data-stu-id="1894f-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="1894f-155">在这种情况下，你可以创建一个 .NET Core 或 .NET Framework 的控制台应用程序项目，该项目的唯一用途是充当工具的启动项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="1894f-156">项目可以是不包含实际代码的虚拟项目， &mdash; 只需为工具提供目标。</span><span class="sxs-lookup"><span data-stu-id="1894f-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="1894f-157">为什么需要虚拟项目？</span><span class="sxs-lookup"><span data-stu-id="1894f-157">Why is a dummy project required?</span></span> <span data-ttu-id="1894f-158">如前文所述，这些工具必须在设计时执行应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="1894f-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="1894f-159">为此，需要使用 .NET Core 或 .NET Framework 运行时。</span><span class="sxs-lookup"><span data-stu-id="1894f-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="1894f-160">当 EF Core 模型位于面向 .NET Core 或 .NET Framework 的项目中时，EF Core 工具会借用项目中的运行时。</span><span class="sxs-lookup"><span data-stu-id="1894f-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="1894f-161">如果 EF Core 模型在 .NET Standard 类库中，则无法执行此操作。</span><span class="sxs-lookup"><span data-stu-id="1894f-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="1894f-162">.NET Standard 不是实际的 .NET 实现;它是 .NET 实现所必须支持的一组 Api 的规范。</span><span class="sxs-lookup"><span data-stu-id="1894f-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="1894f-163">因此 .NET Standard 不足以执行应用程序代码 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="1894f-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="1894f-164">你创建的用于启动项目的虚拟项目提供了一个具体的目标平台，工具可在其中加载 .NET Standard 类库。</span><span class="sxs-lookup"><span data-stu-id="1894f-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="1894f-165">ASP.NET Core 环境</span><span class="sxs-lookup"><span data-stu-id="1894f-165">ASP.NET Core environment</span></span>

<span data-ttu-id="1894f-166">若要为 ASP.NET Core 项目指定环境，请在运行命令之前设置**env： ASPNETCORE_ENVIRONMENT** 。</span><span class="sxs-lookup"><span data-stu-id="1894f-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="1894f-167">通用参数</span><span class="sxs-lookup"><span data-stu-id="1894f-167">Common parameters</span></span>

<span data-ttu-id="1894f-168">下表显示了所有 EF Core 命令共有的参数：</span><span class="sxs-lookup"><span data-stu-id="1894f-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="1894f-169">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-169">Parameter</span></span>                 | <span data-ttu-id="1894f-170">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1894f-171">-上下文\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-171">-Context \<String></span></span>        | <span data-ttu-id="1894f-172">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="1894f-172">The `DbContext` class to use.</span></span> <span data-ttu-id="1894f-173">仅命名空间或完全限定类名。</span><span class="sxs-lookup"><span data-stu-id="1894f-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="1894f-174">如果省略此参数，EF Core 将查找上下文类。</span><span class="sxs-lookup"><span data-stu-id="1894f-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="1894f-175">如果有多个上下文类，则此参数是必需的。</span><span class="sxs-lookup"><span data-stu-id="1894f-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="1894f-176">-项目\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-176">-Project \<String></span></span>        | <span data-ttu-id="1894f-177">目标项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-177">The target project.</span></span> <span data-ttu-id="1894f-178">如果省略此参数，则**包管理器控制台**的**默认项目**将用作目标项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="1894f-179"><nobr>-StartupProject</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="1894f-180">启动项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-180">The startup project.</span></span> <span data-ttu-id="1894f-181">如果省略此参数，则使用**解决方案属性**中的**启动项目**作为目标项目。</span><span class="sxs-lookup"><span data-stu-id="1894f-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="1894f-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="1894f-182">-Verbose</span></span>                  | <span data-ttu-id="1894f-183">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="1894f-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="1894f-184">若要显示有关命令的帮助信息，请使用 PowerShell `Get-Help` 命令。</span><span class="sxs-lookup"><span data-stu-id="1894f-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="1894f-185">上下文、项目和 StartupProject 参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="1894f-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="1894f-186">添加-迁移</span><span class="sxs-lookup"><span data-stu-id="1894f-186">Add-Migration</span></span>

<span data-ttu-id="1894f-187">添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-187">Adds a new migration.</span></span>

<span data-ttu-id="1894f-188">参数：</span><span class="sxs-lookup"><span data-stu-id="1894f-188">Parameters:</span></span>

| <span data-ttu-id="1894f-189">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-189">Parameter</span></span>                         | <span data-ttu-id="1894f-190">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1894f-191"><nobr>-Name\<String><nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="1894f-192">迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="1894f-192">The name of the migration.</span></span> <span data-ttu-id="1894f-193">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="1894f-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="1894f-194"><nobr>-OutputDir\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="1894f-195">用于输出文件的目录。</span><span class="sxs-lookup"><span data-stu-id="1894f-195">The directory use to output the files.</span></span> <span data-ttu-id="1894f-196">路径相对于目标项目目录。</span><span class="sxs-lookup"><span data-stu-id="1894f-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="1894f-197">默认值为 "迁移"。</span><span class="sxs-lookup"><span data-stu-id="1894f-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="1894f-198"><nobr>-命名空间\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="1894f-199">要用于生成的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="1894f-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="1894f-200">默认为从输出目录生成。</span><span class="sxs-lookup"><span data-stu-id="1894f-200">Defaults to generated from the output directory.</span></span> <span data-ttu-id="1894f-201">（从 EFCore 5.0.0 开始提供。）</span><span class="sxs-lookup"><span data-stu-id="1894f-201">(Available from EFCore 5.0.0 onwards.)</span></span> |

## <a name="drop-database"></a><span data-ttu-id="1894f-202">Drop 数据库</span><span class="sxs-lookup"><span data-stu-id="1894f-202">Drop-Database</span></span>

<span data-ttu-id="1894f-203">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="1894f-203">Drops the database.</span></span>

<span data-ttu-id="1894f-204">参数：</span><span class="sxs-lookup"><span data-stu-id="1894f-204">Parameters:</span></span>

| <span data-ttu-id="1894f-205">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-205">Parameter</span></span> | <span data-ttu-id="1894f-206">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-206">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="1894f-207">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="1894f-207">-WhatIf</span></span>   | <span data-ttu-id="1894f-208">显示要删除的数据库，但不删除它。</span><span class="sxs-lookup"><span data-stu-id="1894f-208">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="1894f-209">DbContext</span><span class="sxs-lookup"><span data-stu-id="1894f-209">Get-DbContext</span></span>

<span data-ttu-id="1894f-210">获取有关类型的信息 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-210">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="1894f-211">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="1894f-211">Remove-Migration</span></span>

<span data-ttu-id="1894f-212">删除上一次迁移（回滚针对迁移进行的代码更改）。</span><span class="sxs-lookup"><span data-stu-id="1894f-212">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="1894f-213">参数：</span><span class="sxs-lookup"><span data-stu-id="1894f-213">Parameters:</span></span>

| <span data-ttu-id="1894f-214">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-214">Parameter</span></span> | <span data-ttu-id="1894f-215">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-215">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="1894f-216">-Force</span><span class="sxs-lookup"><span data-stu-id="1894f-216">-Force</span></span>    | <span data-ttu-id="1894f-217">恢复迁移（回滚应用于数据库的更改）。</span><span class="sxs-lookup"><span data-stu-id="1894f-217">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="1894f-218">基架-DbContext</span><span class="sxs-lookup"><span data-stu-id="1894f-218">Scaffold-DbContext</span></span>

<span data-ttu-id="1894f-219">为 `DbContext` 数据库的和实体类型生成代码。</span><span class="sxs-lookup"><span data-stu-id="1894f-219">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="1894f-220">为了使 `Scaffold-DbContext` 生成实体类型，数据库表必须具有主键。</span><span class="sxs-lookup"><span data-stu-id="1894f-220">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="1894f-221">参数：</span><span class="sxs-lookup"><span data-stu-id="1894f-221">Parameters:</span></span>

| <span data-ttu-id="1894f-222">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-222">Parameter</span></span>                          | <span data-ttu-id="1894f-223">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-223">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1894f-224"><nobr>-连接\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-224"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="1894f-225">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1894f-225">The connection string to the database.</span></span> <span data-ttu-id="1894f-226">对于 ASP.NET Core 2.x 项目，值可以是\*name = \<name of connection string> \*。</span><span class="sxs-lookup"><span data-stu-id="1894f-226">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="1894f-227">在这种情况下，该名称来自为项目设置的配置源。</span><span class="sxs-lookup"><span data-stu-id="1894f-227">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="1894f-228">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="1894f-228">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="1894f-229"><nobr>-提供程序\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-229"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="1894f-230">要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="1894f-230">The provider to use.</span></span> <span data-ttu-id="1894f-231">通常，这是 NuGet 包的名称，例如： `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-231">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="1894f-232">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="1894f-232">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="1894f-233">-OutputDir\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-233">-OutputDir \<String></span></span>               | <span data-ttu-id="1894f-234">要在其中放置文件的目录。</span><span class="sxs-lookup"><span data-stu-id="1894f-234">The directory to put files in.</span></span> <span data-ttu-id="1894f-235">路径相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="1894f-235">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="1894f-236">-ContextDir\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-236">-ContextDir \<String></span></span>              | <span data-ttu-id="1894f-237">要在其中放置文件的目录 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-237">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="1894f-238">路径相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="1894f-238">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="1894f-239">-命名空间\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-239">-Namespace \<String></span></span>               | <span data-ttu-id="1894f-240">要用于所有生成的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="1894f-240">The namespace to use for all generated classes.</span></span> <span data-ttu-id="1894f-241">默认值为从根命名空间和输出目录生成。</span><span class="sxs-lookup"><span data-stu-id="1894f-241">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="1894f-242">（从 EFCore 5.0.0 开始提供。）</span><span class="sxs-lookup"><span data-stu-id="1894f-242">(Available from EFCore 5.0.0 onwards.)</span></span> |
| <span data-ttu-id="1894f-243">-ContextNamespace\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-243">-ContextNamespace \<String></span></span>        | <span data-ttu-id="1894f-244">要用于生成的类的命名空间 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-244">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="1894f-245">注意：重写 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-245">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="1894f-246">（从 EFCore 5.0.0 开始提供。）</span><span class="sxs-lookup"><span data-stu-id="1894f-246">(Available from EFCore 5.0.0 onwards.)</span></span> |
| <span data-ttu-id="1894f-247">-上下文\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-247">-Context \<String></span></span>                 | <span data-ttu-id="1894f-248">`DbContext`要生成的类的名称。</span><span class="sxs-lookup"><span data-stu-id="1894f-248">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="1894f-249">-架构\<String[]></span><span class="sxs-lookup"><span data-stu-id="1894f-249">-Schemas \<String[]></span></span>               | <span data-ttu-id="1894f-250">要为其生成实体类型的表的架构。</span><span class="sxs-lookup"><span data-stu-id="1894f-250">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="1894f-251">如果省略此参数，则包括所有架构。</span><span class="sxs-lookup"><span data-stu-id="1894f-251">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="1894f-252">-表\<String[]></span><span class="sxs-lookup"><span data-stu-id="1894f-252">-Tables \<String[]></span></span>                | <span data-ttu-id="1894f-253">要为其生成实体类型的表。</span><span class="sxs-lookup"><span data-stu-id="1894f-253">The tables to generate entity types for.</span></span> <span data-ttu-id="1894f-254">如果省略此参数，则包括所有表。</span><span class="sxs-lookup"><span data-stu-id="1894f-254">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="1894f-255">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="1894f-255">-DataAnnotations</span></span>                   | <span data-ttu-id="1894f-256">使用属性配置模型（如果可能）。</span><span class="sxs-lookup"><span data-stu-id="1894f-256">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="1894f-257">如果省略此参数，则只使用 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="1894f-257">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="1894f-258">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="1894f-258">-UseDatabaseNames</span></span>                  | <span data-ttu-id="1894f-259">使用表和列的名称与数据库中显示的名称完全相同。</span><span class="sxs-lookup"><span data-stu-id="1894f-259">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="1894f-260">如果省略此参数，则更改数据库名称以更严格地符合 c # 名称样式约定。</span><span class="sxs-lookup"><span data-stu-id="1894f-260">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="1894f-261">-Force</span><span class="sxs-lookup"><span data-stu-id="1894f-261">-Force</span></span>                             | <span data-ttu-id="1894f-262">覆盖现有文件。</span><span class="sxs-lookup"><span data-stu-id="1894f-262">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="1894f-263">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="1894f-263">-NoOnConfiguring</span></span>                   | <span data-ttu-id="1894f-264">禁止 `OnConfiguring` 在生成的类中生成方法 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-264">Suppresses generation of the `OnConfiguring` method in the generated `DbContext` class.</span></span> <span data-ttu-id="1894f-265">（从 EFCore 5.0.0 开始提供。）</span><span class="sxs-lookup"><span data-stu-id="1894f-265">(Available from EFCore 5.0.0 onwards.)</span></span> |

<span data-ttu-id="1894f-266">示例：</span><span class="sxs-lookup"><span data-stu-id="1894f-266">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="1894f-267">示例：仅基架选定的表，并在具有指定名称和命名空间的单独文件夹中创建上下文：</span><span class="sxs-lookup"><span data-stu-id="1894f-267">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="1894f-268">脚本迁移</span><span class="sxs-lookup"><span data-stu-id="1894f-268">Script-Migration</span></span>

<span data-ttu-id="1894f-269">生成一个 SQL 脚本，该脚本将所选迁移中的所有更改应用于另一个选定的迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-269">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="1894f-270">参数：</span><span class="sxs-lookup"><span data-stu-id="1894f-270">Parameters:</span></span>

| <span data-ttu-id="1894f-271">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-271">Parameter</span></span>                | <span data-ttu-id="1894f-272">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-272">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1894f-273">*-来自*\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-273">*-From* \<String></span></span>        | <span data-ttu-id="1894f-274">开始迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-274">The starting migration.</span></span> <span data-ttu-id="1894f-275">可以按名称或 ID 识别迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-275">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="1894f-276">数字0是一个特殊情况，表示在*第一次迁移之前*。</span><span class="sxs-lookup"><span data-stu-id="1894f-276">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="1894f-277">默认值为 0。</span><span class="sxs-lookup"><span data-stu-id="1894f-277">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="1894f-278">*-到*\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-278">*-To* \<String></span></span>          | <span data-ttu-id="1894f-279">结束迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-279">The ending migration.</span></span> <span data-ttu-id="1894f-280">默认为上次迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-280">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="1894f-281"><nobr>-幂等</nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-281"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="1894f-282">生成可用于任何迁移的数据库的脚本。</span><span class="sxs-lookup"><span data-stu-id="1894f-282">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="1894f-283">-输出\<String></span><span class="sxs-lookup"><span data-stu-id="1894f-283">-Output \<String></span></span>        | <span data-ttu-id="1894f-284">要向其写入结果的文件。</span><span class="sxs-lookup"><span data-stu-id="1894f-284">The file to write the result to.</span></span> <span data-ttu-id="1894f-285">如果省略此参数，则会在创建应用的运行时文件所在的同一文件夹中创建具有生成名称的文件，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。</span><span class="sxs-lookup"><span data-stu-id="1894f-285">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="1894f-286">To、From 和 Output 参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="1894f-286">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="1894f-287">以下示例使用迁移名称创建用于 InitialCreate 迁移的脚本。</span><span class="sxs-lookup"><span data-stu-id="1894f-287">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="1894f-288">以下示例使用迁移 ID，为 InitialCreate 迁移后的所有迁移创建一个脚本。</span><span class="sxs-lookup"><span data-stu-id="1894f-288">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="1894f-289">Update-Database</span><span class="sxs-lookup"><span data-stu-id="1894f-289">Update-Database</span></span>

<span data-ttu-id="1894f-290">将数据库更新到上次迁移或指定迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-290">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="1894f-291">参数</span><span class="sxs-lookup"><span data-stu-id="1894f-291">Parameter</span></span>                           | <span data-ttu-id="1894f-292">说明</span><span class="sxs-lookup"><span data-stu-id="1894f-292">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1894f-293"><nobr>*-迁移*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-293"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="1894f-294">目标迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-294">The target migration.</span></span> <span data-ttu-id="1894f-295">可以按名称或 ID 识别迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-295">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="1894f-296">数字0是一种特殊情况，表示在*第一次迁移之前*，并导致还原所有迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-296">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="1894f-297">如果未指定迁移，则该命令默认为上一次迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-297">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="1894f-298"><nobr>-连接\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="1894f-298"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="1894f-299">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1894f-299">The connection string to the database.</span></span> <span data-ttu-id="1894f-300">默认为或中指定的 `AddDbContext` 一个 `OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="1894f-300">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="1894f-301">迁移参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="1894f-301">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="1894f-302">下面的示例将还原所有迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-302">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="1894f-303">下面的示例将数据库更新为指定的迁移。</span><span class="sxs-lookup"><span data-stu-id="1894f-303">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="1894f-304">第一个使用迁移名称，第二个使用迁移 ID 和指定的连接：</span><span class="sxs-lookup"><span data-stu-id="1894f-304">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="1894f-305">其他资源</span><span class="sxs-lookup"><span data-stu-id="1894f-305">Additional resources</span></span>

* [<span data-ttu-id="1894f-306">迁移</span><span class="sxs-lookup"><span data-stu-id="1894f-306">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="1894f-307">反向工程</span><span class="sxs-lookup"><span data-stu-id="1894f-307">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
