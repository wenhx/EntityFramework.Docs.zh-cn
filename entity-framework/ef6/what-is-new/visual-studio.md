---
title: Visual Studio 版本-EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
ms.openlocfilehash: 16bcdc6d0e7c5632d4f4c06ba285a7a666f24204
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414355"
---
# <a name="visual-studio-releases"></a><span data-ttu-id="18d1a-102">Visual Studio 版本</span><span class="sxs-lookup"><span data-stu-id="18d1a-102">Visual Studio Releases</span></span>

<span data-ttu-id="18d1a-103">建议始终使用最新版本的 Visual Studio，因为它包含适用于 .NET、NuGet 和实体框架的最新工具。</span><span class="sxs-lookup"><span data-stu-id="18d1a-103">We recommend to always use the latest version of Visual Studio because it contains the latest tools for .NET, NuGet, and Entity Framework.</span></span>
<span data-ttu-id="18d1a-104">事实上，实体框架文档中的各种示例和演练都假设你使用的是最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="18d1a-104">In fact, the various samples and walkthroughs across the Entity Framework documentation assume that you are using a recent version of Visual Studio.</span></span>

<span data-ttu-id="18d1a-105">不过，只要考虑一些差异，就有可能使用不同版本的 Visual Studio 的较旧版本实体框架：</span><span class="sxs-lookup"><span data-stu-id="18d1a-105">It is possible however to use older versions of Visual Studio with different versions of Entity Framework as long as you take into account some differences:</span></span>

## <a name="visual-studio-2017-157-and-newer"></a><span data-ttu-id="18d1a-106">Visual Studio 2017 15.7 及更高版本</span><span class="sxs-lookup"><span data-stu-id="18d1a-106">Visual Studio 2017 15.7 and newer</span></span>

- <span data-ttu-id="18d1a-107">此版本的 Visual Studio 包括实体框架工具和 EF 6.2 运行时的最新版本，不需要执行其他设置步骤。</span><span class="sxs-lookup"><span data-stu-id="18d1a-107">This version of Visual Studio includes the latest release of Entity Framework tools and the EF 6.2 runtime, and does not require additional setup steps.</span></span>
<span data-ttu-id="18d1a-108">有关这些版本的详细信息，请参阅[新增功能](~/ef6/what-is-new/index.md)。</span><span class="sxs-lookup"><span data-stu-id="18d1a-108">See [What's New](~/ef6/what-is-new/index.md) for more details on these releases.</span></span>
- <span data-ttu-id="18d1a-109">使用 EF 工具将实体框架添加到新项目将自动添加 EF 6.2 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-109">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.2 NuGet package.</span></span>
<span data-ttu-id="18d1a-110">可以手动安装或升级到联机提供的任何 EF NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-110">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="18d1a-111">默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例为 LocalDB 实例，名为 MSSQLLocalDB。</span><span class="sxs-lookup"><span data-stu-id="18d1a-111">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="18d1a-112">应使用的连接字符串的服务器部分为 "（localdb）\\MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="18d1a-112">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="18d1a-113">在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。</span><span class="sxs-lookup"><span data-stu-id="18d1a-113">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a><span data-ttu-id="18d1a-114">Visual Studio 2015 到 Visual Studio 2017 15。6</span><span class="sxs-lookup"><span data-stu-id="18d1a-114">Visual Studio 2015 to Visual Studio 2017 15.6</span></span>

- <span data-ttu-id="18d1a-115">这些版本的 Visual Studio 包括实体框架工具和运行时 ef6.1.3。</span><span class="sxs-lookup"><span data-stu-id="18d1a-115">These versions of Visual Studio include Entity Framework tools and runtime 6.1.3.</span></span>
<span data-ttu-id="18d1a-116">有关这些版本的详细信息，请参阅[以前的版本](~/ef6/what-is-new/past-releases.md#ef-613)。</span><span class="sxs-lookup"><span data-stu-id="18d1a-116">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="18d1a-117">使用 EF 工具向新项目添加实体框架会自动添加 EF ef6.1.3 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-117">Adding Entity Framework to new projects using the EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="18d1a-118">可以手动安装或升级到联机提供的任何 EF NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-118">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="18d1a-119">默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例为 LocalDB 实例，名为 MSSQLLocalDB。</span><span class="sxs-lookup"><span data-stu-id="18d1a-119">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="18d1a-120">应使用的连接字符串的服务器部分为 "（localdb）\\MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="18d1a-120">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="18d1a-121">在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。</span><span class="sxs-lookup"><span data-stu-id="18d1a-121">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  


## <a name="visual-studio-2013"></a><span data-ttu-id="18d1a-122">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="18d1a-122">Visual Studio 2013</span></span>
- <span data-ttu-id="18d1a-123">此版本的 Visual Studio 包括实体框架工具和运行时的旧版本。</span><span class="sxs-lookup"><span data-stu-id="18d1a-123">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="18d1a-124">建议使用 Microsoft 下载中心中提供[的安装程序](https://www.microsoft.com/download/details.aspx?id=40762)Entity Framework Tools ef6.1.3 升级到。</span><span class="sxs-lookup"><span data-stu-id="18d1a-124">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="18d1a-125">有关这些版本的详细信息，请参阅[以前的版本](~/ef6/what-is-new/past-releases.md#ef-613)。</span><span class="sxs-lookup"><span data-stu-id="18d1a-125">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="18d1a-126">使用升级后的 EF 工具向新项目添加实体框架会自动添加 EF ef6.1.3 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-126">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="18d1a-127">可以手动安装或升级到联机提供的任何 EF NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-127">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="18d1a-128">默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例为 LocalDB 实例，名为 MSSQLLocalDB。</span><span class="sxs-lookup"><span data-stu-id="18d1a-128">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called MSSQLLocalDB.</span></span>
<span data-ttu-id="18d1a-129">应使用的连接字符串的服务器部分为 "（localdb）\\MSSQLLocalDB"。</span><span class="sxs-lookup"><span data-stu-id="18d1a-129">The server section of connection string you should use is "(localdb)\\MSSQLLocalDB".</span></span>
<span data-ttu-id="18d1a-130">在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。</span><span class="sxs-lookup"><span data-stu-id="18d1a-130">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2012"></a><span data-ttu-id="18d1a-131">Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="18d1a-131">Visual Studio 2012</span></span>

- <span data-ttu-id="18d1a-132">此版本的 Visual Studio 包括实体框架工具和运行时的旧版本。</span><span class="sxs-lookup"><span data-stu-id="18d1a-132">This version of Visual Studio includes and older version of Entity Framework tools and runtime.</span></span>
<span data-ttu-id="18d1a-133">建议使用 Microsoft 下载中心中提供[的安装程序](https://www.microsoft.com/download/details.aspx?id=40762)Entity Framework Tools ef6.1.3 升级到。</span><span class="sxs-lookup"><span data-stu-id="18d1a-133">It is recommended that you upgrade to Entity Framework Tools 6.1.3, using [the installer](https://www.microsoft.com/download/details.aspx?id=40762) available in the Microsoft Download Center.</span></span>
<span data-ttu-id="18d1a-134">有关这些版本的详细信息，请参阅[以前的版本](~/ef6/what-is-new/past-releases.md#ef-613)。</span><span class="sxs-lookup"><span data-stu-id="18d1a-134">See [Past Releases](~/ef6/what-is-new/past-releases.md#ef-613) for more details on these releases.</span></span>
- <span data-ttu-id="18d1a-135">使用升级后的 EF 工具向新项目添加实体框架会自动添加 EF ef6.1.3 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-135">Adding Entity Framework to new projects using the upgraded EF tools will automatically add the EF 6.1.3 NuGet package.</span></span>
<span data-ttu-id="18d1a-136">可以手动安装或升级到联机提供的任何 EF NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="18d1a-136">You can manually install or upgrade to any EF NuGet package available online.</span></span>
- <span data-ttu-id="18d1a-137">默认情况下，此版本的 Visual Studio 中可用的 SQL Server 实例是名为 "v 11.0" 的 LocalDB 实例。</span><span class="sxs-lookup"><span data-stu-id="18d1a-137">By default, the SQL Server instance available with this version of Visual Studio is a LocalDB instance called v11.0.</span></span>
<span data-ttu-id="18d1a-138">应使用的连接字符串的服务器部分为 "（localdb）\\v 11.0"。</span><span class="sxs-lookup"><span data-stu-id="18d1a-138">The server section of connection string you should use is "(localdb)\\v11.0".</span></span>
<span data-ttu-id="18d1a-139">在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。</span><span class="sxs-lookup"><span data-stu-id="18d1a-139">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>  

## <a name="visual-studio-2010"></a><span data-ttu-id="18d1a-140">Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="18d1a-140">Visual Studio 2010</span></span>

- <span data-ttu-id="18d1a-141">此版本的 Visual Studio 中提供的 Entity Framework Tools 版本与实体框架6运行时不兼容，因此无法升级。</span><span class="sxs-lookup"><span data-stu-id="18d1a-141">The version of Entity Framework Tools available with this version of Visual Studio is not compatible with the Entity Framework 6 runtime and cannot be upgraded.</span></span>
- <span data-ttu-id="18d1a-142">默认情况下，实体框架工具会将实体框架4.0 添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="18d1a-142">By default, the Entity Framework tools will add Entity Framework 4.0 to your projects.</span></span>
<span data-ttu-id="18d1a-143">若要使用任何更高版本的 EF 创建应用程序，首先需要安装[NuGet 包管理器扩展](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager)。</span><span class="sxs-lookup"><span data-stu-id="18d1a-143">In order to create applications using any newer versions of EF, you will first need to install the [NuGet Package Manager extension](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager).</span></span>
- <span data-ttu-id="18d1a-144">默认情况下，EF 工具版本中的所有代码生成都基于 EntityObject 和实体框架4。</span><span class="sxs-lookup"><span data-stu-id="18d1a-144">By default, all code generation in the version of EF tools is based on EntityObject and Entity Framework 4.</span></span>
<span data-ttu-id="18d1a-145">建议通过安装[C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC)或[Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET)的 DbContext 代码生成模板，将代码生成切换为基于 DbContext 和实体框架5。</span><span class="sxs-lookup"><span data-stu-id="18d1a-145">We recommend that you switch the code generation to be based on DbContext and Entity Framework 5, by installing the DbContext code generation templates for [C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) or [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET).</span></span>
- <span data-ttu-id="18d1a-146">安装 NuGet 包管理器扩展后，可以手动安装或升级到任何联机可用的 EF NuGet 包，并将 EF6 与 Code First 一起使用，这不需要设计器。</span><span class="sxs-lookup"><span data-stu-id="18d1a-146">Once you have installed the NuGet Package Manager extensions, you can manually install or upgrade to any EF NuGet package available online and use EF6 with Code First, which does not require a designer.</span></span>
- <span data-ttu-id="18d1a-147">默认情况下，此版本的 Visual Studio 中提供的 SQL Server 实例 SQL Server Express 称为 SQLEXPRESS。</span><span class="sxs-lookup"><span data-stu-id="18d1a-147">By default, the SQL Server instance available with this version of Visual Studio is SQL Server Express named SQLEXPRESS.</span></span>
<span data-ttu-id="18d1a-148">应使用的连接字符串的服务器部分是 ""。\\SQLEXPRESS "。</span><span class="sxs-lookup"><span data-stu-id="18d1a-148">The server section of connection string you should use is ".\\SQLEXPRESS".</span></span>
<span data-ttu-id="18d1a-149">在代码中C#指定连接字符串时，请记住使用前缀为 `@` 或双反斜杠 "\\\\" 的逐字字符串。</span><span class="sxs-lookup"><span data-stu-id="18d1a-149">Remember to use a verbatim string prefixed with `@` or double back-slashes "\\\\" when specifying a connection string in C# code.</span></span>
