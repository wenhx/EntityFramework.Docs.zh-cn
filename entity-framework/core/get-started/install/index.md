---
title: 安装 Entity Framework Core - EF Core
description: Entity Framework Core 的安装说明
author: bricelam
ms.date: 08/06/2017
uid: core/get-started/install/index
ms.openlocfilehash: 3aae80998768d8d1bfbad7a872abc3648b792ad5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062095"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="f070f-103">安装 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f070f-103">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f070f-104">先决条件</span><span class="sxs-lookup"><span data-stu-id="f070f-104">Prerequisites</span></span>

* <span data-ttu-id="f070f-105">EF Core 是一个 [.NET Standard 2.0](/dotnet/standard/net-standard) 库。</span><span class="sxs-lookup"><span data-stu-id="f070f-105">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="f070f-106">因此，EF Core 需要支持运行 .NET Standard 2.0 的实现。</span><span class="sxs-lookup"><span data-stu-id="f070f-106">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="f070f-107">其他 .NET Standard 2.0 库也可引用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="f070f-107">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="f070f-108">例如，可使用 EF Core 开发面向 .NET Core 的应用。</span><span class="sxs-lookup"><span data-stu-id="f070f-108">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="f070f-109">生成 .NET Core 应用需要 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="f070f-109">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="f070f-110">还可选择使用 [Visual Studio](https://visualstudio.microsoft.com/vs)[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac) 或 [Visual Studio Code](https://code.visualstudio.com) 等开发环境。</span><span class="sxs-lookup"><span data-stu-id="f070f-110">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="f070f-111">有关详细信息，请参阅 [.NET Core 入门](/dotnet/core/get-started)。</span><span class="sxs-lookup"><span data-stu-id="f070f-111">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="f070f-112">可在 Windows 上的 Visual Studio 中使用 EF Core 来开发应用程序。</span><span class="sxs-lookup"><span data-stu-id="f070f-112">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="f070f-113">建议使用最新版本的 [Visual Studio](https://visualstudio.microsoft.com/vs)。</span><span class="sxs-lookup"><span data-stu-id="f070f-113">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="f070f-114">EF Core 可以在 [Xamarin](https://dotnet.microsoft.com/apps/xamarin) 和 .NET Native 等其他 .NET 实现上运行。</span><span class="sxs-lookup"><span data-stu-id="f070f-114">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="f070f-115">但在实践中，这些实现具有运行时限制，可能会影响 EF Core 处理应用的效率。</span><span class="sxs-lookup"><span data-stu-id="f070f-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="f070f-116">有关详细信息，请参阅 [EF Core 支持的 .NET 实现](xref:core/platforms/index)。</span><span class="sxs-lookup"><span data-stu-id="f070f-116">For more information, see [.NET implementations supported by EF Core](xref:core/platforms/index).</span></span>

* <span data-ttu-id="f070f-117">最后，不同的数据库提供程序可能需要特定的数据库引擎版本、.NET 实现或操作系统。</span><span class="sxs-lookup"><span data-stu-id="f070f-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="f070f-118">请确保可用的 [EF Core 数据库提供程序](xref:core/providers/index)支持适用于应用程序的环境。</span><span class="sxs-lookup"><span data-stu-id="f070f-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="f070f-119">获取 Entity Framework Core 运行时</span><span class="sxs-lookup"><span data-stu-id="f070f-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="f070f-120">要将 EF Core 添加到应用程序，请安装适用于要使用的数据库提供程序的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f070f-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="f070f-121">如果要生成 ASP.NET Core 应用程序，不需要安装内存中和 SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="f070f-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="f070f-122">这些提供程序随 EF Core 运行时一起包含在当前版本的 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="f070f-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>

<span data-ttu-id="f070f-123">要安装或更新 NuGet 包，可以使用 .NET Core 命令行界面 (CLI)、Visual Studio 包管理器对话框或 Visual Studio 包管理器控制台。</span><span class="sxs-lookup"><span data-stu-id="f070f-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="f070f-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f070f-124">.NET Core CLI</span></span>

* <span data-ttu-id="f070f-125">在操作系统的命令行中使用以下 .NET Core CLI 命令来安装或更新 EF Core SQL Server 提供程序：</span><span class="sxs-lookup"><span data-stu-id="f070f-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="f070f-126">可以使用 `-v` 修饰符在 `dotnet add package` 命令中指明特定的版本。</span><span class="sxs-lookup"><span data-stu-id="f070f-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="f070f-127">例如，若要安装 EF Core 2.2.0 包，请将 `-v 2.2.0` 追加到命令中。</span><span class="sxs-lookup"><span data-stu-id="f070f-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="f070f-128">有关详细信息，请参阅 [.NET 命令行接口 (CLI) 工具](/dotnet/core/tools/)。</span><span class="sxs-lookup"><span data-stu-id="f070f-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="f070f-129">Visual Studio NuGet 包管理器对话框</span><span class="sxs-lookup"><span data-stu-id="f070f-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="f070f-130">从 Visual Studio 菜单中选择“项目”>“管理 NuGet 包”\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="f070f-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="f070f-131">单击“浏览”或“更新”选项卡\*\*\*\*\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="f070f-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="f070f-132">若要安装或更新 SQL Server 提供程序，请选择 `Microsoft.EntityFrameworkCore.SqlServer` 包并确认。</span><span class="sxs-lookup"><span data-stu-id="f070f-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="f070f-133">有关详细信息，请参阅 [NuGet 包管理器对话框](/nuget/tools/package-manager-ui)。</span><span class="sxs-lookup"><span data-stu-id="f070f-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="f070f-134">Visual Studio NuGet 包管理器控制台</span><span class="sxs-lookup"><span data-stu-id="f070f-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="f070f-135">从 Visual Studio 菜单中选择“工具”>“NuGet 包管理器”>“包管理器控制台”\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="f070f-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="f070f-136">若要安装 SQL Server 提供程序，请在包管理器控制台中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f070f-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="f070f-137">若要更新提供程序，使用 `Update-Package` 命令。</span><span class="sxs-lookup"><span data-stu-id="f070f-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="f070f-138">若要指定特定版本，可以使用 `-Version` 修饰符。</span><span class="sxs-lookup"><span data-stu-id="f070f-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="f070f-139">例如，若要安装 EF Core 2.2.0 包，请将 `-Version 2.2.0` 追加到命令中</span><span class="sxs-lookup"><span data-stu-id="f070f-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="f070f-140">有关详细信息，请参阅[包管理器控制台](/nuget/tools/package-manager-console)。</span><span class="sxs-lookup"><span data-stu-id="f070f-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="f070f-141">获取 Entity Framework Core 工具</span><span class="sxs-lookup"><span data-stu-id="f070f-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="f070f-142">可以安装工具来执行项目中与 EF Core 相关的任务，例如创建和应用数据库迁移，或基于现有数据库创建 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="f070f-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="f070f-143">提供了两个工具集：</span><span class="sxs-lookup"><span data-stu-id="f070f-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="f070f-144">[.NET Core 命令行接口 (CLI) 工具](xref:core/miscellaneous/cli/dotnet)可用于 Windows、Linux 或 macOS。</span><span class="sxs-lookup"><span data-stu-id="f070f-144">The [.NET Core command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="f070f-145">这些命令以 `dotnet ef` 开头。</span><span class="sxs-lookup"><span data-stu-id="f070f-145">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="f070f-146">[包管理器控制台 (PMC) 工具](xref:core/miscellaneous/cli/powershell)在 Windows 上的 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="f070f-146">The [Package Manager Console (PMC) tools](xref:core/miscellaneous/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="f070f-147">这些命令以动词开头，例如 `Add-Migration`、`Update-Database`。</span><span class="sxs-lookup"><span data-stu-id="f070f-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="f070f-148">虽然也可在包管理器控制台中使用 `dotnet ef` 命令，但在使用 Visual Studio 时建议使用包管理器控制台工具：</span><span class="sxs-lookup"><span data-stu-id="f070f-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="f070f-149">它们会自动使用在 Visual Studio 的 PMC 中选择的当前项目，无需手动切换目录。</span><span class="sxs-lookup"><span data-stu-id="f070f-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>

* <span data-ttu-id="f070f-150">命令完成后，它们会自动在 Visual Studio 中打开命令所生成的文件。</span><span class="sxs-lookup"><span data-stu-id="f070f-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="f070f-151">获取 .NET Core CLI 工具</span><span class="sxs-lookup"><span data-stu-id="f070f-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="f070f-152">.NET core CLI 工具需要前面的[系统必备](#prerequisites)中提到的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="f070f-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

* <span data-ttu-id="f070f-153">必须将 `dotnet ef` 安装为全局工具或本地工具。</span><span class="sxs-lookup"><span data-stu-id="f070f-153">`dotnet ef` must be installed as a global or local tool.</span></span> <span data-ttu-id="f070f-154">大多数开发人员偏向于使用以下命令将 `dotnet ef` 安装为全局工具：</span><span class="sxs-lookup"><span data-stu-id="f070f-154">Most developers prefer installing `dotnet ef` as a global tool using the following command:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  <span data-ttu-id="f070f-155">`dotnet ef` 还可用作本地工具。</span><span class="sxs-lookup"><span data-stu-id="f070f-155">`dotnet ef` can also be used as a local tool.</span></span> <span data-ttu-id="f070f-156">若要将它用作本地工具，请使用[工具清单文件](/dotnet/core/tools/global-tools#install-a-local-tool)恢复声明为工具依赖项的项目的依赖项。</span><span class="sxs-lookup"><span data-stu-id="f070f-156">To use it as a local tool, restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

* <span data-ttu-id="f070f-157">若要更新工具，请使用 `dotnet tool update` 命令。</span><span class="sxs-lookup"><span data-stu-id="f070f-157">To update the tools, use the `dotnet tool update` command.</span></span>

* <span data-ttu-id="f070f-158">安装最新的 `Microsoft.EntityFrameworkCore.Design` 包。</span><span class="sxs-lookup"><span data-stu-id="f070f-158">Install the latest `Microsoft.EntityFrameworkCore.Design` package.</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

> [!IMPORTANT]
> <span data-ttu-id="f070f-159">请务必使用与运行时包主版本匹配的工具包版本。</span><span class="sxs-lookup"><span data-stu-id="f070f-159">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="f070f-160">获取包管理器控制台工具</span><span class="sxs-lookup"><span data-stu-id="f070f-160">Get the Package Manager Console tools</span></span>

<span data-ttu-id="f070f-161">若要获取适用于 EF Core 的包管理器控制台工具，请安装 `Microsoft.EntityFrameworkCore.Tools` 包。</span><span class="sxs-lookup"><span data-stu-id="f070f-161">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="f070f-162">例如，在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="f070f-162">For example, from Visual Studio:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f070f-163">ASP.NET Core 应用自动随附此包。</span><span class="sxs-lookup"><span data-stu-id="f070f-163">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="f070f-164">升级到最新版本的 EF Core</span><span class="sxs-lookup"><span data-stu-id="f070f-164">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="f070f-165">每次发布新版本的 EF Core 时，我们都还会在 EF Core 项目中发布新版本的提供程序，如 Microsoft.EntityFrameworkCore.SqlServer、Microsoft.EntityFrameworkCore.Sqlite 和 Microsoft.EntityFrameworkCore.InMemory。</span><span class="sxs-lookup"><span data-stu-id="f070f-165">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="f070f-166">只需升级到新版本的提供程序即可获取所有改进。</span><span class="sxs-lookup"><span data-stu-id="f070f-166">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="f070f-167">EF Core 以及 SQL Server 和内存中提供程序包含在当前版本的 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="f070f-167">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="f070f-168">若要将现有 ASP.NET Core 应用程序升级到较新版本的 EF Core，请务必升级 ASP.NET Core 的版本。</span><span class="sxs-lookup"><span data-stu-id="f070f-168">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="f070f-169">如需更新使用第三方数据库提供程序的应用程序，请始终检查与要使用的 EF Core 版本兼容的提供程序有无更新。</span><span class="sxs-lookup"><span data-stu-id="f070f-169">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="f070f-170">例如，版本 1.0 的数据库提供程序与版本 2.0 的 EF Core 运行时不兼容。</span><span class="sxs-lookup"><span data-stu-id="f070f-170">For example, database providers for version 1.0 are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="f070f-171">用于 EF Core 的第三方提供程序通常不随 EF Core 运行发布修补程序版本。</span><span class="sxs-lookup"><span data-stu-id="f070f-171">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="f070f-172">若要将使用第三方提供程序的应用程序升级到 EF Core 的修补程序版本，可能需要添加对单独的 EF Core 运行时组件（如 Microsoft.EntityFrameworkCore 和 Microsoft.EntityFrameworkCore.Relational）的直接引用。</span><span class="sxs-lookup"><span data-stu-id="f070f-172">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>
