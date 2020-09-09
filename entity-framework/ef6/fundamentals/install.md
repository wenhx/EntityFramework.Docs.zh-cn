---
title: 获取实体框架-EF6
description: 获取实体框架6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
uid: ef6/fundamentals/install
ms.openlocfilehash: bfd016a93de73ada4487a454ec7abd9251aeecfe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616247"
---
# <a name="get-entity-framework"></a><span data-ttu-id="6b69a-103">获取实体框架</span><span class="sxs-lookup"><span data-stu-id="6b69a-103">Get Entity Framework</span></span>
<span data-ttu-id="6b69a-104">实体框架由适用于 Visual Studio 的 EF 工具和 EF 运行时组成。</span><span class="sxs-lookup"><span data-stu-id="6b69a-104">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="6b69a-105">适用于 Visual Studio 的 EF 工具</span><span class="sxs-lookup"><span data-stu-id="6b69a-105">EF Tools for Visual Studio</span></span>

<span data-ttu-id="6b69a-106">Visual Studio 的 Entity Framework Tools 包括 EF 设计器和 EF 模型向导，并是数据库优先和模型优先工作流所必需的。</span><span class="sxs-lookup"><span data-stu-id="6b69a-106">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="6b69a-107">所有最新版本的 Visual Studio 中都包含 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="6b69a-107">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="6b69a-108">如果执行 Visual Studio 的自定义安装，则需要确保选择项 "实体框架6工具"，方法是选择包含它的工作负荷，或将其选择为单个组件。</span><span class="sxs-lookup"><span data-stu-id="6b69a-108">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="6b69a-109">对于某些早期版本的 Visual Studio，更新的 EF 工具可作为下载。</span><span class="sxs-lookup"><span data-stu-id="6b69a-109">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="6b69a-110">请参阅 [Visual Studio 版本](xref:ef6/what-is-new/visual-studio) ，了解有关如何获取适用于你的 Visual studio 版本的最新版本 EF 工具的指南。</span><span class="sxs-lookup"><span data-stu-id="6b69a-110">See [Visual Studio Versions](xref:ef6/what-is-new/visual-studio) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="6b69a-111">EF 运行时</span><span class="sxs-lookup"><span data-stu-id="6b69a-111">EF Runtime</span></span>

<span data-ttu-id="6b69a-112">实体框架的最新版本可作为 [EntityFramework NuGet 包](https://nuget.org/packages/EntityFramework/)提供。</span><span class="sxs-lookup"><span data-stu-id="6b69a-112">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="6b69a-113">如果你不熟悉 NuGet 包管理器，我们建议你阅读 [Nuget 概述](/nuget/consume-packages/overview-and-workflow)。</span><span class="sxs-lookup"><span data-stu-id="6b69a-113">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="6b69a-114">安装 EF NuGet 包</span><span class="sxs-lookup"><span data-stu-id="6b69a-114">Installing the EF NuGet Package</span></span>

<span data-ttu-id="6b69a-115">可以通过右键单击项目的 "**引用**" 文件夹并选择 "**管理 NuGet 包 ...** " 来安装 EntityFramework 包。</span><span class="sxs-lookup"><span data-stu-id="6b69a-115">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![管理 NuGet 包](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="6b69a-117">从包管理器控制台安装</span><span class="sxs-lookup"><span data-stu-id="6b69a-117">Installing from Package Manager Console</span></span>

<span data-ttu-id="6b69a-118">或者，你可以通过在 [包管理器控制台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中运行以下命令来安装 EntityFramework。</span><span class="sxs-lookup"><span data-stu-id="6b69a-118">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="6b69a-119">安装特定版本的 EF</span><span class="sxs-lookup"><span data-stu-id="6b69a-119">Installing a specific version of EF</span></span>

<span data-ttu-id="6b69a-120">从 EF 4.1 开始，已发布了 EF 运行时的新版本作为 [EntityFramework NuGet 包](https://www.nuget.org/packages/EntityFramework/)。</span><span class="sxs-lookup"><span data-stu-id="6b69a-120">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="6b69a-121">可以通过在 Visual Studio 的 [包管理器控制台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中运行以下命令，将这些版本中的任何一种添加到基于 .NET Framework 的项目中：</span><span class="sxs-lookup"><span data-stu-id="6b69a-121">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="6b69a-122">请注意， `<number>` 表示要安装的 EF 的特定版本。</span><span class="sxs-lookup"><span data-stu-id="6b69a-122">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="6b69a-123">例如，6.2.0 是 EF 6.2 的编号版本。</span><span class="sxs-lookup"><span data-stu-id="6b69a-123">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="6b69a-124">4.1 之前的 EF 运行时是 .NET Framework 的一部分，不能单独安装。</span><span class="sxs-lookup"><span data-stu-id="6b69a-124">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="6b69a-125">安装最新预览版</span><span class="sxs-lookup"><span data-stu-id="6b69a-125">Installing the Latest Preview</span></span>

<span data-ttu-id="6b69a-126">上述方法将为你介绍实体框架的最新完全受支持的版本。</span><span class="sxs-lookup"><span data-stu-id="6b69a-126">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="6b69a-127">实体框架提供了预发布版本的预发布版本，我们希望你尝试并向我们提供反馈。</span><span class="sxs-lookup"><span data-stu-id="6b69a-127">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="6b69a-128">若要安装最新的 EntityFramework 预览版，可以在 "管理 NuGet 包" 窗口中选择 " **包括预发行** 版"。</span><span class="sxs-lookup"><span data-stu-id="6b69a-128">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="6b69a-129">如果没有可用的预发布版本，则会自动获取实体框架的最新完全受支持版本。</span><span class="sxs-lookup"><span data-stu-id="6b69a-129">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![包括预发行版](~/ef6/media/includeprerelease.png)

<span data-ttu-id="6b69a-131">或者，你可以在 [包管理器控制台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="6b69a-131">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
