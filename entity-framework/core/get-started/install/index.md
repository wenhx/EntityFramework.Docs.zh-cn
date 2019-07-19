---
title: 安装 Entity Framework Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: db1b83a9222e00a5e226a134085b18247b31f29a
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306484"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="01991-102">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="01991-102">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01991-103">系统必备</span><span class="sxs-lookup"><span data-stu-id="01991-103">Prerequisites</span></span>

* <span data-ttu-id="01991-104">EF Core 是一个 [.NET Standard 2.0](/dotnet/standard/net-standard) 库。</span><span class="sxs-lookup"><span data-stu-id="01991-104">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="01991-105">因此，EF Core 需要支持运行 .NET Standard 2.0 的实现。</span><span class="sxs-lookup"><span data-stu-id="01991-105">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="01991-106">其他 .NET Standard 2.0 库也可引用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="01991-106">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span> 

* <span data-ttu-id="01991-107">例如，可使用 EF Core 开发面向 .NET Core 的应用。</span><span class="sxs-lookup"><span data-stu-id="01991-107">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="01991-108">生成 .NET Core 应用需要 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="01991-108">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="01991-109">还可选择使用 Visual Studio、Visual Studio for Mac 或 Visual Studio Code 等开发环境。</span><span class="sxs-lookup"><span data-stu-id="01991-109">Optionally, you can also use a development environment like Visual Studio, Visual Studio for Mac, or Visual Studio Code.</span></span> <span data-ttu-id="01991-110">有关详细信息，请参阅 [.NET Core 入门](/dotnet/core/get-started)。</span><span class="sxs-lookup"><span data-stu-id="01991-110">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="01991-111">可在 Windows 上的 Visual Studio 中使用 EF Core 来开发面向 .NET Framework 4.6.1 或更高版本的应用程序。</span><span class="sxs-lookup"><span data-stu-id="01991-111">You can use EF Core to develop applications that target .NET Framework 4.6.1 or later on Windows, using Visual Studio.</span></span> <span data-ttu-id="01991-112">建议使用最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="01991-112">The latest version of Visual Studio is recommended.</span></span> <span data-ttu-id="01991-113">如果想要使用较旧版本（例如 Visual Studio 2015），请确保[将 NuGet 客户端升级到版本 3.6.0](https://www.nuget.org/downloads)，以便使用 .NET Standard 2.0 库。</span><span class="sxs-lookup"><span data-stu-id="01991-113">If you want to use an older version, like Visual Studio 2015, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="01991-114">EF Core 可以在 Xamarin 和 .NET Native 等其他 .NET 实现上运行。</span><span class="sxs-lookup"><span data-stu-id="01991-114">EF Core can run on other .NET implementations like Xamarin and .NET Native.</span></span> <span data-ttu-id="01991-115">但在实践中，这些实现具有运行时限制，可能会影响 EF Core 处理应用的效率。</span><span class="sxs-lookup"><span data-stu-id="01991-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="01991-116">有关详细信息，请参阅 [EF Core 支持的 .NET 实现](xref:core/platforms/index)。</span><span class="sxs-lookup"><span data-stu-id="01991-116">For more information, see [.NET implementations supported by EF Core](xref:core/platforms/index).</span></span>

* <span data-ttu-id="01991-117">最后，不同的数据库提供程序可能需要特定的数据库引擎版本、.NET 实现或操作系统。</span><span class="sxs-lookup"><span data-stu-id="01991-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="01991-118">请确保可用的 [EF Core 数据库提供程序](xref:core/providers/index)支持适用于应用程序的环境。</span><span class="sxs-lookup"><span data-stu-id="01991-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="01991-119">获取 Entity Framework Core 运行时</span><span class="sxs-lookup"><span data-stu-id="01991-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="01991-120">要将 EF Core 添加到应用程序，请安装适用于要使用的数据库提供程序的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="01991-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="01991-121">如果要生成 ASP.NET Core 应用程序，不需要安装内存中和 SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="01991-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="01991-122">这些提供程序随 EF Core 运行时一起包含在当前版本的 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="01991-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>  

<span data-ttu-id="01991-123">要安装或更新 NuGet 包，可以使用 .NET Core 命令行界面 (CLI)、Visual Studio 包管理器对话框或 Visual Studio 包管理器控制台。</span><span class="sxs-lookup"><span data-stu-id="01991-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="01991-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="01991-124">.NET Core CLI</span></span>

* <span data-ttu-id="01991-125">在操作系统的命令行中使用以下 .NET Core CLI 命令来安装或更新 EF Core SQL Server 提供程序：</span><span class="sxs-lookup"><span data-stu-id="01991-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ``` Console
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="01991-126">可以使用 `-v` 修饰符在 `dotnet add package` 命令中指明特定的版本。</span><span class="sxs-lookup"><span data-stu-id="01991-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="01991-127">例如，若要安装 EF Core 2.2.0 包，请将 `-v 2.2.0` 追加到命令中。</span><span class="sxs-lookup"><span data-stu-id="01991-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="01991-128">有关详细信息，请参阅 [.NET 命令行接口 (CLI) 工具](/dotnet/core/tools/)。</span><span class="sxs-lookup"><span data-stu-id="01991-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="01991-129">Visual Studio NuGet 包管理器对话框</span><span class="sxs-lookup"><span data-stu-id="01991-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="01991-130">从 Visual Studio 菜单中选择“项目”>“管理 NuGet 包” </span><span class="sxs-lookup"><span data-stu-id="01991-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="01991-131">单击“浏览”或“更新”选项卡  </span><span class="sxs-lookup"><span data-stu-id="01991-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="01991-132">若要安装或更新 SQL Server 提供程序，请选择 `Microsoft.EntityFrameworkCore.SqlServer` 包并确认。</span><span class="sxs-lookup"><span data-stu-id="01991-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="01991-133">有关详细信息，请参阅 [NuGet 包管理器对话框](/nuget/tools/package-manager-ui)。</span><span class="sxs-lookup"><span data-stu-id="01991-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="01991-134">Visual Studio NuGet 包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="01991-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="01991-135">从 Visual Studio 菜单中选择“工具”>“NuGet 包管理器”>“包管理器控制台” </span><span class="sxs-lookup"><span data-stu-id="01991-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="01991-136">若要安装 SQL Server 提供程序，请在包管理器控制台中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="01991-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* <span data-ttu-id="01991-137">若要更新提供程序，使用 `Update-Package` 命令。</span><span class="sxs-lookup"><span data-stu-id="01991-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="01991-138">若要指定特定版本，可以使用 `-Version` 修饰符。</span><span class="sxs-lookup"><span data-stu-id="01991-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="01991-139">例如，若要安装 EF Core 2.2.0 包，请将 `-Version 2.2.0` 追加到命令中</span><span class="sxs-lookup"><span data-stu-id="01991-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="01991-140">有关详细信息，请参阅[包管理器控制台](/nuget/tools/package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="01991-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="01991-141">获取 Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="01991-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="01991-142">可以安装工具来执行项目中与 EF Core 相关的任务，例如创建和应用数据库迁移，或基于现有数据库创建 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="01991-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="01991-143">提供了两个工具集：</span><span class="sxs-lookup"><span data-stu-id="01991-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="01991-144">[.NET Core 命令行接口 (CLI) 工具](xref:core/miscellaneous/cli/dotnet)可用于 Windows、Linux 或 macOS。</span><span class="sxs-lookup"><span data-stu-id="01991-144">The [.NET Core command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="01991-145">这些命令以 `dotnet ef` 开头。</span><span class="sxs-lookup"><span data-stu-id="01991-145">These commands begin with `dotnet ef`.</span></span> 

* <span data-ttu-id="01991-146">[包管理器控制台 (PMC) 工具](xref:core/miscellaneous/cli/powershell)在 Windows 上的 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="01991-146">The [Package Manager Console (PMC) tools](xref:core/miscellaneous/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="01991-147">这些命令以动词开头，例如 `Add-Migration`、`Update-Database`。</span><span class="sxs-lookup"><span data-stu-id="01991-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="01991-148">虽然也可在包管理器控制台中使用 `dotnet ef` 命令，但在使用 Visual Studio 时建议使用包管理器控制台工具：</span><span class="sxs-lookup"><span data-stu-id="01991-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="01991-149">它们会自动使用在 Visual Studio 的 PMC 中选择的当前项目，无需手动切换目录。</span><span class="sxs-lookup"><span data-stu-id="01991-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>  

* <span data-ttu-id="01991-150">命令完成后，它们会自动在 Visual Studio 中打开命令所生成的文件。</span><span class="sxs-lookup"><span data-stu-id="01991-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="01991-151">获取 .NET Core CLI 工具</span><span class="sxs-lookup"><span data-stu-id="01991-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="01991-152">.NET core CLI 工具需要前面的[系统必备](#prerequisites)中提到的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="01991-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

<span data-ttu-id="01991-153">`dotnet ef` 命令包含在当前版本的 .NET Core SDK 中，但若要对特定命令启用该命令，必须安装 `Microsoft.EntityFrameworkCore.Design` 包：</span><span class="sxs-lookup"><span data-stu-id="01991-153">The `dotnet ef` commands are included in current versions of the .NET Core SDK, but to enable the commands on a specific project, you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

``` Console 
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

<span data-ttu-id="01991-154">ASP.NET Core 应用自动随附此包。</span><span class="sxs-lookup"><span data-stu-id="01991-154">For ASP.NET Core apps, this package is included automatically.</span></span>

> [!IMPORTANT]      
> <span data-ttu-id="01991-155">请务必使用与运行时包主版本匹配的工具包版本。</span><span class="sxs-lookup"><span data-stu-id="01991-155">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="01991-156">获取包管理器控制台工具</span><span class="sxs-lookup"><span data-stu-id="01991-156">Get the Package Manager Console tools</span></span>

<span data-ttu-id="01991-157">若要获取适用于 EF Core 的包管理器控制台工具，请安装 `Microsoft.EntityFrameworkCore.Tools` 包。</span><span class="sxs-lookup"><span data-stu-id="01991-157">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="01991-158">例如，在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="01991-158">For example, from Visual Studio:</span></span>

``` PowerShell  
Install-Package Microsoft.EntityFrameworkCore.Tools
``` 

<span data-ttu-id="01991-159">ASP.NET Core 应用自动随附此包。</span><span class="sxs-lookup"><span data-stu-id="01991-159">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="01991-160">升级到最新版本的 EF Core</span><span class="sxs-lookup"><span data-stu-id="01991-160">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="01991-161">每次发布新版本的 EF Core 时，我们都还会在 EF Core 项目中发布新版本的提供程序，如 Microsoft.EntityFrameworkCore.SqlServer、Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.InMemory。</span><span class="sxs-lookup"><span data-stu-id="01991-161">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="01991-162">只需升级到新版本的提供程序即可获取所有改进。</span><span class="sxs-lookup"><span data-stu-id="01991-162">You can just upgrade to the new version of the provider to get all the improvements.</span></span> 

* <span data-ttu-id="01991-163">EF Core 以及 SQL Server 和内存中提供程序包含在当前版本的 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="01991-163">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="01991-164">若要将现有 ASP.NET Core 应用程序升级到较新版本的 EF Core，请务必升级 ASP.NET Core 的版本。</span><span class="sxs-lookup"><span data-stu-id="01991-164">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="01991-165">如需更新使用第三方数据库提供程序的应用程序，请始终检查与要使用的 EF Core 版本兼容的提供程序有无更新。</span><span class="sxs-lookup"><span data-stu-id="01991-165">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="01991-166">例如，早期版本的数据库提供程序不兼容 2.0 版 EF Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="01991-166">For example, database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="01991-167">用于 EF Core 的第三方提供程序通常不随 EF Core 运行发布修补程序版本。</span><span class="sxs-lookup"><span data-stu-id="01991-167">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="01991-168">若要将使用第三方提供程序的应用程序升级到 EF Core 的修补程序版本，可能需要添加对单独的 EF Core 运行时组件（如 Microsoft.EntityFrameworkCore 和 Microsoft.EntityFrameworkCore.Relational）的直接引用。</span><span class="sxs-lookup"><span data-stu-id="01991-168">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>

* <span data-ttu-id="01991-169">若要将现有应用程序升级到最新版本的 EF Core，可能需要手动删除一些对旧版 EF Core 包的引用：</span><span class="sxs-lookup"><span data-stu-id="01991-169">If you're upgrading an existing application to the latest version of EF Core, some references to older EF Core packages may need to be removed manually:</span></span>

  * <span data-ttu-id="01991-170">EF Core 2.0 及更高版本不再需要或支持 `Microsoft.EntityFrameworkCore.SqlServer.Design` 等数据库提供程序设计时包，但在升级其他包后，它们不会被自动删除。</span><span class="sxs-lookup"><span data-stu-id="01991-170">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported from EF Core 2.0 and later, but aren't automatically removed when upgrading the other packages.</span></span>

  * <span data-ttu-id="01991-171">自版本 2.1 起的 .NET SDK 包含 .NET CLI 工具，这样就可以从项目文件中删除对相应包的引用：</span><span class="sxs-lookup"><span data-stu-id="01991-171">The .NET CLI tools are included in the .NET SDK since version 2.1, so the reference to that package can be removed from the project file:</span></span>

    ```
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```

* <span data-ttu-id="01991-172">面向 .NET Framework 的应用程序可能需要更改为使用 .NET Standard 2.0 库：</span><span class="sxs-lookup"><span data-stu-id="01991-172">Applications that target .NET Framework may need changes to work with .NET Standard 2.0 libraries:</span></span>

  * <span data-ttu-id="01991-173">编辑项目文件，并确保以下条目出现在初始属性组中：</span><span class="sxs-lookup"><span data-stu-id="01991-173">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

    ``` xml
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    ```

  * <span data-ttu-id="01991-174">对于测试项目，还要确保存在以下条目：</span><span class="sxs-lookup"><span data-stu-id="01991-174">For test projects, also make sure the following entry is present:</span></span>

    ``` xml
    <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
    ```
