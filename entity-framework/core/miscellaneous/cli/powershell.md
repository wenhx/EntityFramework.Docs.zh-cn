---
title: EF Core 工具参考（程序包管理器控制台）-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 0676475d46a8d21dee7bd10e25dd273a11e96ac3
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538405"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="bc394-102">Entity Framework Core 工具参考-Visual Studio 中的包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="bc394-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="bc394-103">适用于 Entity Framework Core 的包管理器控制台（PMC）工具执行设计时开发任务。</span><span class="sxs-lookup"><span data-stu-id="bc394-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="bc394-104">例如，他们基于现有数据库创建[迁移](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)、应用迁移和生成模型的代码。</span><span class="sxs-lookup"><span data-stu-id="bc394-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="bc394-105">命令使用[包管理器控制台](/nuget/tools/package-manager-console)在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="bc394-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="bc394-106">这些工具同时适用于 .NET Framework 和 .NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="bc394-107">如果未使用 Visual Studio，则建议改为使用[EF Core 命令行工具](dotnet.md)。</span><span class="sxs-lookup"><span data-stu-id="bc394-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="bc394-108">CLI 工具是跨平台的，在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="bc394-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="bc394-109">安装工具</span><span class="sxs-lookup"><span data-stu-id="bc394-109">Installing the tools</span></span>

<span data-ttu-id="bc394-110">用于安装和更新这些工具的过程在 ASP.NET Core 2.1 及更早版本或其他项目类型之间有所不同。</span><span class="sxs-lookup"><span data-stu-id="bc394-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="bc394-111">ASP.NET Core 版本2.1 及更高版本</span><span class="sxs-lookup"><span data-stu-id="bc394-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="bc394-112">工具将自动包含在 ASP.NET Core 2.1 + 项目中，因为`Microsoft.EntityFrameworkCore.Tools`包包含在[AspNetCore 元包](/aspnet/core/fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="bc394-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="bc394-113">因此，您无需执行任何操作来安装这些工具，但必须执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bc394-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="bc394-114">在新项目中使用这些工具之前还原包。</span><span class="sxs-lookup"><span data-stu-id="bc394-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="bc394-115">安装包以将工具更新到较新版本。</span><span class="sxs-lookup"><span data-stu-id="bc394-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="bc394-116">为了确保获得最新版本的工具，我们建议您也执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="bc394-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="bc394-117">编辑 *.csproj*文件，并添加一行以指定最新版本的[microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)包。</span><span class="sxs-lookup"><span data-stu-id="bc394-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="bc394-118">例如， *.csproj*文件可能包含如下所示`ItemGroup`的：</span><span class="sxs-lookup"><span data-stu-id="bc394-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="bc394-119">在收到类似于以下示例的消息时更新工具：</span><span class="sxs-lookup"><span data-stu-id="bc394-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="bc394-120">EF Core 工具版本 "2.1.1-30846" 早于运行时 "2.1.3-32065"。</span><span class="sxs-lookup"><span data-stu-id="bc394-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="bc394-121">更新工具以获取最新功能和 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="bc394-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="bc394-122">更新工具：</span><span class="sxs-lookup"><span data-stu-id="bc394-122">To update the tools:</span></span>

* <span data-ttu-id="bc394-123">安装最新的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="bc394-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="bc394-124">将 Visual Studio 更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="bc394-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="bc394-125">编辑 *.csproj*文件，使其包含对最新工具包的包引用，如前文所述。</span><span class="sxs-lookup"><span data-stu-id="bc394-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="bc394-126">其他版本和项目类型</span><span class="sxs-lookup"><span data-stu-id="bc394-126">Other versions and project types</span></span>

<span data-ttu-id="bc394-127">在**Package Manager console**中运行以下命令，安装包管理器控制台工具：</span><span class="sxs-lookup"><span data-stu-id="bc394-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="bc394-128">在**Package Manager Console**中运行以下命令，更新这些工具。</span><span class="sxs-lookup"><span data-stu-id="bc394-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="bc394-129">验证安装</span><span class="sxs-lookup"><span data-stu-id="bc394-129">Verify the installation</span></span>

<span data-ttu-id="bc394-130">通过运行以下命令验证是否已安装这些工具：</span><span class="sxs-lookup"><span data-stu-id="bc394-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="bc394-131">输出如下所示（不告诉您正在使用的工具的版本）：</span><span class="sxs-lookup"><span data-stu-id="bc394-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="bc394-132">使用工具</span><span class="sxs-lookup"><span data-stu-id="bc394-132">Using the tools</span></span>

<span data-ttu-id="bc394-133">使用这些工具之前：</span><span class="sxs-lookup"><span data-stu-id="bc394-133">Before using the tools:</span></span>

* <span data-ttu-id="bc394-134">了解目标项目和启动项目之间的差异。</span><span class="sxs-lookup"><span data-stu-id="bc394-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="bc394-135">了解如何将工具与 .NET Standard 类库一起使用。</span><span class="sxs-lookup"><span data-stu-id="bc394-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="bc394-136">对于 ASP.NET Core 项目，请设置环境。</span><span class="sxs-lookup"><span data-stu-id="bc394-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="bc394-137">目标和启动项目</span><span class="sxs-lookup"><span data-stu-id="bc394-137">Target and startup project</span></span>

<span data-ttu-id="bc394-138">命令引用*项目*和*启动项目*。</span><span class="sxs-lookup"><span data-stu-id="bc394-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="bc394-139">该*项目*也称为*目标项目*，因为它是命令在其中添加或删除文件的位置。</span><span class="sxs-lookup"><span data-stu-id="bc394-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="bc394-140">默认情况下，在 "**程序包管理器控制台**" 中选择的**默认项目**是目标项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="bc394-141">可以使用<nobr>`--project`</nobr>选项指定其他项目作为目标项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="bc394-142">*启动项目*是工具生成和运行的项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="bc394-143">这些工具必须在设计时执行应用程序代码，以获取有关项目的信息，例如数据库连接字符串和模型的配置。</span><span class="sxs-lookup"><span data-stu-id="bc394-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="bc394-144">默认情况下，**解决方案资源管理器**中的**启动项目**是启动项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="bc394-145">您可以使用<nobr>`--startup-project`</nobr>选项指定其他项目作为启动项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="bc394-146">启动项目和目标项目通常是同一个项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="bc394-147">它们是不同的项目的典型方案是：</span><span class="sxs-lookup"><span data-stu-id="bc394-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="bc394-148">EF Core 的上下文和实体类位于 .NET Core 类库中。</span><span class="sxs-lookup"><span data-stu-id="bc394-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="bc394-149">.NET Core 控制台应用程序或 web 应用程序引用类库。</span><span class="sxs-lookup"><span data-stu-id="bc394-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="bc394-150">还可以[将迁移代码放在与 EF Core 上下文分离](xref:core/managing-schemas/migrations/projects)的类库中。</span><span class="sxs-lookup"><span data-stu-id="bc394-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="bc394-151">其他目标框架</span><span class="sxs-lookup"><span data-stu-id="bc394-151">Other target frameworks</span></span>

<span data-ttu-id="bc394-152">包管理器控制台工具适用于 .NET Core 或 .NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="bc394-153">.NET Standard 类库中具有 EF Core 模型的应用可能没有 .NET Core 或 .NET Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="bc394-154">例如，这适用于 Xamarin 和通用 Windows 平台应用。</span><span class="sxs-lookup"><span data-stu-id="bc394-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="bc394-155">在这种情况下，你可以创建一个 .NET Core 或 .NET Framework 的控制台应用程序项目，该项目的唯一用途是充当工具的启动项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="bc394-156">项目可以是不包含实际代码&mdash;的虚拟项目，只需为工具提供目标。</span><span class="sxs-lookup"><span data-stu-id="bc394-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="bc394-157">为什么需要虚拟项目？</span><span class="sxs-lookup"><span data-stu-id="bc394-157">Why is a dummy project required?</span></span> <span data-ttu-id="bc394-158">如前文所述，这些工具必须在设计时执行应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="bc394-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="bc394-159">为此，需要使用 .NET Core 或 .NET Framework 运行时。</span><span class="sxs-lookup"><span data-stu-id="bc394-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="bc394-160">当 EF Core 模型位于面向 .NET Core 或 .NET Framework 的项目中时，EF Core 工具会借用项目中的运行时。</span><span class="sxs-lookup"><span data-stu-id="bc394-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="bc394-161">如果 EF Core 模型在 .NET Standard 类库中，则无法执行此操作。</span><span class="sxs-lookup"><span data-stu-id="bc394-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="bc394-162">.NET Standard 不是实际的 .NET 实现;它是 .NET 实现所必须支持的一组 Api 的规范。</span><span class="sxs-lookup"><span data-stu-id="bc394-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="bc394-163">因此 .NET Standard 不足以执行应用程序代码 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="bc394-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="bc394-164">你创建的用于启动项目的虚拟项目提供了一个具体的目标平台，工具可在其中加载 .NET Standard 类库。</span><span class="sxs-lookup"><span data-stu-id="bc394-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="bc394-165">ASP.NET Core 环境</span><span class="sxs-lookup"><span data-stu-id="bc394-165">ASP.NET Core environment</span></span>

<span data-ttu-id="bc394-166">若要为 ASP.NET Core 项目指定环境，请在运行命令之前设置**env： ASPNETCORE_ENVIRONMENT** 。</span><span class="sxs-lookup"><span data-stu-id="bc394-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="bc394-167">通用参数</span><span class="sxs-lookup"><span data-stu-id="bc394-167">Common parameters</span></span>

<span data-ttu-id="bc394-168">下表显示了所有 EF Core 命令共有的参数：</span><span class="sxs-lookup"><span data-stu-id="bc394-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="bc394-169">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-169">Parameter</span></span>                 | <span data-ttu-id="bc394-170">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="bc394-171">-上下文\<字符串></span><span class="sxs-lookup"><span data-stu-id="bc394-171">-Context \<String></span></span>        | <span data-ttu-id="bc394-172">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="bc394-172">The `DbContext` class to use.</span></span> <span data-ttu-id="bc394-173">仅命名空间或完全限定类名。</span><span class="sxs-lookup"><span data-stu-id="bc394-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="bc394-174">如果省略此参数，EF Core 将查找上下文类。</span><span class="sxs-lookup"><span data-stu-id="bc394-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="bc394-175">如果有多个上下文类，则此参数是必需的。</span><span class="sxs-lookup"><span data-stu-id="bc394-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="bc394-176">-项目\<字符串></span><span class="sxs-lookup"><span data-stu-id="bc394-176">-Project \<String></span></span>        | <span data-ttu-id="bc394-177">目标项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-177">The target project.</span></span> <span data-ttu-id="bc394-178">如果省略此参数，则**包管理器控制台**的**默认项目**将用作目标项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="bc394-179"><nobr>-StartupProject</nobr> \<String></span><span class="sxs-lookup"><span data-stu-id="bc394-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="bc394-180">启动项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-180">The startup project.</span></span> <span data-ttu-id="bc394-181">如果省略此参数，则使用**解决方案属性**中的**启动项目**作为目标项目。</span><span class="sxs-lookup"><span data-stu-id="bc394-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="bc394-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="bc394-182">-Verbose</span></span>                  | <span data-ttu-id="bc394-183">显示详细输出。</span><span class="sxs-lookup"><span data-stu-id="bc394-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="bc394-184">若要显示有关命令的帮助信息，请使用`Get-Help` PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="bc394-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="bc394-185">上下文、项目和 StartupProject 参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="bc394-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="bc394-186">添加-迁移</span><span class="sxs-lookup"><span data-stu-id="bc394-186">Add-Migration</span></span>

<span data-ttu-id="bc394-187">添加新的迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-187">Adds a new migration.</span></span>

<span data-ttu-id="bc394-188">参数：</span><span class="sxs-lookup"><span data-stu-id="bc394-188">Parameters:</span></span>

| <span data-ttu-id="bc394-189">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-189">Parameter</span></span>                         | <span data-ttu-id="bc394-190">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="bc394-191"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="bc394-192">迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="bc394-192">The name of the migration.</span></span> <span data-ttu-id="bc394-193">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="bc394-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="bc394-194"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="bc394-195">用于输出文件的目录。</span><span class="sxs-lookup"><span data-stu-id="bc394-195">The directory use to output the files.</span></span> <span data-ttu-id="bc394-196">路径相对于目标项目目录。</span><span class="sxs-lookup"><span data-stu-id="bc394-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="bc394-197">默认值为 "迁移"。</span><span class="sxs-lookup"><span data-stu-id="bc394-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="bc394-198"><nobr>-命名\<空间字符串></nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="bc394-199">要用于生成的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="bc394-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="bc394-200">默认为从输出目录生成。</span><span class="sxs-lookup"><span data-stu-id="bc394-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="bc394-201">Drop 数据库</span><span class="sxs-lookup"><span data-stu-id="bc394-201">Drop-Database</span></span>

<span data-ttu-id="bc394-202">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="bc394-202">Drops the database.</span></span>

<span data-ttu-id="bc394-203">参数：</span><span class="sxs-lookup"><span data-stu-id="bc394-203">Parameters:</span></span>

| <span data-ttu-id="bc394-204">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-204">Parameter</span></span> | <span data-ttu-id="bc394-205">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="bc394-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="bc394-206">-WhatIf</span></span>   | <span data-ttu-id="bc394-207">显示要删除的数据库，但不删除它。</span><span class="sxs-lookup"><span data-stu-id="bc394-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="bc394-208">DbContext</span><span class="sxs-lookup"><span data-stu-id="bc394-208">Get-DbContext</span></span>

<span data-ttu-id="bc394-209">获取有关类型的`DbContext`信息。</span><span class="sxs-lookup"><span data-stu-id="bc394-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="bc394-210">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="bc394-210">Remove-Migration</span></span>

<span data-ttu-id="bc394-211">删除上一次迁移（回滚针对迁移进行的代码更改）。</span><span class="sxs-lookup"><span data-stu-id="bc394-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="bc394-212">参数：</span><span class="sxs-lookup"><span data-stu-id="bc394-212">Parameters:</span></span>

| <span data-ttu-id="bc394-213">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-213">Parameter</span></span> | <span data-ttu-id="bc394-214">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="bc394-215">-Force</span><span class="sxs-lookup"><span data-stu-id="bc394-215">-Force</span></span>    | <span data-ttu-id="bc394-216">恢复迁移（回滚应用于数据库的更改）。</span><span class="sxs-lookup"><span data-stu-id="bc394-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="bc394-217">基架-DbContext</span><span class="sxs-lookup"><span data-stu-id="bc394-217">Scaffold-DbContext</span></span>

<span data-ttu-id="bc394-218">为数据库的`DbContext`和实体类型生成代码。</span><span class="sxs-lookup"><span data-stu-id="bc394-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="bc394-219">`Scaffold-DbContext`为了使生成实体类型，数据库表必须具有主键。</span><span class="sxs-lookup"><span data-stu-id="bc394-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="bc394-220">参数：</span><span class="sxs-lookup"><span data-stu-id="bc394-220">Parameters:</span></span>

| <span data-ttu-id="bc394-221">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-221">Parameter</span></span>                          | <span data-ttu-id="bc394-222">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="bc394-223"><nobr>-连接\<字符串></nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="bc394-224">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bc394-224">The connection string to the database.</span></span> <span data-ttu-id="bc394-225">对于 ASP.NET Core 2.x 项目，值可以是*name =\<连接字符串>的名称*。</span><span class="sxs-lookup"><span data-stu-id="bc394-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="bc394-226">在这种情况下，该名称来自为项目设置的配置源。</span><span class="sxs-lookup"><span data-stu-id="bc394-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="bc394-227">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="bc394-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="bc394-228"><nobr>-提供\<程序字符串></nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="bc394-229">要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="bc394-229">The provider to use.</span></span> <span data-ttu-id="bc394-230">通常，这是 NuGet 包的名称，例如： `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="bc394-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="bc394-231">这是一个位置参数，并且是必需的。</span><span class="sxs-lookup"><span data-stu-id="bc394-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="bc394-232">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="bc394-232">-OutputDir \<String></span></span>               | <span data-ttu-id="bc394-233">要在其中放置文件的目录。</span><span class="sxs-lookup"><span data-stu-id="bc394-233">The directory to put files in.</span></span> <span data-ttu-id="bc394-234">路径相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="bc394-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="bc394-235">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="bc394-235">-ContextDir \<String></span></span>              | <span data-ttu-id="bc394-236">要在其中放置`DbContext`文件的目录。</span><span class="sxs-lookup"><span data-stu-id="bc394-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="bc394-237">路径相对于项目目录。</span><span class="sxs-lookup"><span data-stu-id="bc394-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="bc394-238">-命名\<空间字符串></span><span class="sxs-lookup"><span data-stu-id="bc394-238">-Namespace \<String></span></span>               | <span data-ttu-id="bc394-239">要用于所有生成的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="bc394-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="bc394-240">默认值为从根命名空间和输出目录生成。</span><span class="sxs-lookup"><span data-stu-id="bc394-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="bc394-241">-ContextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="bc394-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="bc394-242">要用于生成`DbContext`的类的命名空间。</span><span class="sxs-lookup"><span data-stu-id="bc394-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="bc394-243">注意：重`-Namespace`写。</span><span class="sxs-lookup"><span data-stu-id="bc394-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="bc394-244">-上下文\<字符串></span><span class="sxs-lookup"><span data-stu-id="bc394-244">-Context \<String></span></span>                 | <span data-ttu-id="bc394-245">要生成的`DbContext`类的名称。</span><span class="sxs-lookup"><span data-stu-id="bc394-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="bc394-246">-架构\<字符串 [] ></span><span class="sxs-lookup"><span data-stu-id="bc394-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="bc394-247">要为其生成实体类型的表的架构。</span><span class="sxs-lookup"><span data-stu-id="bc394-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="bc394-248">如果省略此参数，则包括所有架构。</span><span class="sxs-lookup"><span data-stu-id="bc394-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="bc394-249">-Tables \<String [] ></span><span class="sxs-lookup"><span data-stu-id="bc394-249">-Tables \<String[]></span></span>                | <span data-ttu-id="bc394-250">要为其生成实体类型的表。</span><span class="sxs-lookup"><span data-stu-id="bc394-250">The tables to generate entity types for.</span></span> <span data-ttu-id="bc394-251">如果省略此参数，则包括所有表。</span><span class="sxs-lookup"><span data-stu-id="bc394-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="bc394-252">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="bc394-252">-DataAnnotations</span></span>                   | <span data-ttu-id="bc394-253">使用属性配置模型（如果可能）。</span><span class="sxs-lookup"><span data-stu-id="bc394-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="bc394-254">如果省略此参数，则只使用 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="bc394-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="bc394-255">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="bc394-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="bc394-256">使用表和列的名称与数据库中显示的名称完全相同。</span><span class="sxs-lookup"><span data-stu-id="bc394-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="bc394-257">如果省略此参数，则更改数据库名称以更严格地符合 c # 名称样式约定。</span><span class="sxs-lookup"><span data-stu-id="bc394-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="bc394-258">-Force</span><span class="sxs-lookup"><span data-stu-id="bc394-258">-Force</span></span>                             | <span data-ttu-id="bc394-259">覆盖现有文件。</span><span class="sxs-lookup"><span data-stu-id="bc394-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="bc394-260">示例：</span><span class="sxs-lookup"><span data-stu-id="bc394-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="bc394-261">示例：仅基架选定的表，并在具有指定名称和命名空间的单独文件夹中创建上下文：</span><span class="sxs-lookup"><span data-stu-id="bc394-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="bc394-262">脚本迁移</span><span class="sxs-lookup"><span data-stu-id="bc394-262">Script-Migration</span></span>

<span data-ttu-id="bc394-263">生成一个 SQL 脚本，该脚本将所选迁移中的所有更改应用于另一个选定的迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="bc394-264">参数：</span><span class="sxs-lookup"><span data-stu-id="bc394-264">Parameters:</span></span>

| <span data-ttu-id="bc394-265">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-265">Parameter</span></span>                | <span data-ttu-id="bc394-266">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="bc394-267">*-From* \<String></span><span class="sxs-lookup"><span data-stu-id="bc394-267">*-From* \<String></span></span>        | <span data-ttu-id="bc394-268">开始迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-268">The starting migration.</span></span> <span data-ttu-id="bc394-269">可以按名称或 ID 识别迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="bc394-270">数字0是一个特殊情况，表示在*第一次迁移之前*。</span><span class="sxs-lookup"><span data-stu-id="bc394-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="bc394-271">默认值为 0。</span><span class="sxs-lookup"><span data-stu-id="bc394-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="bc394-272">*-到* \<字符串></span><span class="sxs-lookup"><span data-stu-id="bc394-272">*-To* \<String></span></span>          | <span data-ttu-id="bc394-273">结束迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-273">The ending migration.</span></span> <span data-ttu-id="bc394-274">默认为上次迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="bc394-275"><nobr>-幂等</nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="bc394-276">生成可用于任何迁移的数据库的脚本。</span><span class="sxs-lookup"><span data-stu-id="bc394-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="bc394-277">-输出\<字符串></span><span class="sxs-lookup"><span data-stu-id="bc394-277">-Output \<String></span></span>        | <span data-ttu-id="bc394-278">要向其写入结果的文件。</span><span class="sxs-lookup"><span data-stu-id="bc394-278">The file to write the result to.</span></span> <span data-ttu-id="bc394-279">如果省略此参数，则会在创建应用的运行时文件所在的同一文件夹中创建具有生成名称的文件，例如： */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*。</span><span class="sxs-lookup"><span data-stu-id="bc394-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="bc394-280">To、From 和 Output 参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="bc394-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="bc394-281">以下示例使用迁移名称创建用于 InitialCreate 迁移的脚本。</span><span class="sxs-lookup"><span data-stu-id="bc394-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="bc394-282">以下示例使用迁移 ID，为 InitialCreate 迁移后的所有迁移创建一个脚本。</span><span class="sxs-lookup"><span data-stu-id="bc394-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="bc394-283">Update-Database</span><span class="sxs-lookup"><span data-stu-id="bc394-283">Update-Database</span></span>

<span data-ttu-id="bc394-284">将数据库更新到上次迁移或指定迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="bc394-285">参数</span><span class="sxs-lookup"><span data-stu-id="bc394-285">Parameter</span></span>                           | <span data-ttu-id="bc394-286">说明</span><span class="sxs-lookup"><span data-stu-id="bc394-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="bc394-287"><nobr>*-迁移* \<字符串></nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="bc394-288">目标迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-288">The target migration.</span></span> <span data-ttu-id="bc394-289">可以按名称或 ID 识别迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="bc394-290">数字0是一种特殊情况，表示在*第一次迁移之前*，并导致还原所有迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="bc394-291">如果未指定迁移，则该命令默认为上一次迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="bc394-292"><nobr>-连接\<字符串></nobr></span><span class="sxs-lookup"><span data-stu-id="bc394-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="bc394-293">用于连接到数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bc394-293">The connection string to the database.</span></span> <span data-ttu-id="bc394-294">默认为或`AddDbContext` `OnConfiguring`中指定的一个。</span><span class="sxs-lookup"><span data-stu-id="bc394-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="bc394-295">迁移参数支持选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="bc394-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="bc394-296">下面的示例将还原所有迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="bc394-297">下面的示例将数据库更新为指定的迁移。</span><span class="sxs-lookup"><span data-stu-id="bc394-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="bc394-298">第一个使用迁移名称，第二个使用迁移 ID 和指定的连接：</span><span class="sxs-lookup"><span data-stu-id="bc394-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="bc394-299">其他资源</span><span class="sxs-lookup"><span data-stu-id="bc394-299">Additional resources</span></span>

* [<span data-ttu-id="bc394-300">迁移</span><span class="sxs-lookup"><span data-stu-id="bc394-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="bc394-301">反向工程</span><span class="sxs-lookup"><span data-stu-id="bc394-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
