---
title: 新增功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: c49f4cba0066d1e218f11c3959d96f9cafa913f4
ms.sourcegitcommit: 7bc43f21e7bdd64926314ea949aae689f1911956
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266786"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="5cb73-102">EF6 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="5cb73-102">What's new in EF6</span></span>

<span data-ttu-id="5cb73-103">强烈建议使用最新发布的实体框架版本，以确保获得最新功能和最高稳定性。</span><span class="sxs-lookup"><span data-stu-id="5cb73-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="5cb73-104">但我们也意识到用户可能需要使用以前的版本，或想要尝试最新预发行版中的新改进。</span><span class="sxs-lookup"><span data-stu-id="5cb73-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="5cb73-105">若要安装特定版本的 EF，请参阅[获取实体框架](~/ef6/fundamentals/install.md)。</span><span class="sxs-lookup"><span data-stu-id="5cb73-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-630"></a><span data-ttu-id="5cb73-106">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="5cb73-106">EF 6.3.0</span></span>

<span data-ttu-id="5cb73-107">EF 6.3.0 运行时已于 2019 年 9 月发布到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="5cb73-107">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="5cb73-108">此版本的主要目标是帮助将使用 EF 6 的现有应用程序迁移到 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="5cb73-108">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="5cb73-109">社区还提供了多个 bug 修复和增强功能。</span><span class="sxs-lookup"><span data-stu-id="5cb73-109">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="5cb73-110">有关详细信息，请参阅每个 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中关闭的问题。</span><span class="sxs-lookup"><span data-stu-id="5cb73-110">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="5cb73-111">下面是一些更值得注意的事项：</span><span class="sxs-lookup"><span data-stu-id="5cb73-111">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="5cb73-112">支持 .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="5cb73-112">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="5cb73-113">除了 .NET Framework 4.x，EntityFramework 包现在还面向 .NET Standard 2.1。</span><span class="sxs-lookup"><span data-stu-id="5cb73-113">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="5cb73-114">这意味着 EF 6.3 是跨平台的，并在 Windows 之外的其他操作系统（如 Linux 和 macOS）上受支持。</span><span class="sxs-lookup"><span data-stu-id="5cb73-114">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="5cb73-115">已重新编写迁移命令以在进程外执行，并使用 SDK 样式的项目。</span><span class="sxs-lookup"><span data-stu-id="5cb73-115">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="5cb73-116">支持 SQL Server HierarchyId</span><span class="sxs-lookup"><span data-stu-id="5cb73-116">Support for SQL Server HierarchyId</span></span>
- <span data-ttu-id="5cb73-117">提高了与 Roslyn 和 NuGet PackageReference 的兼容性</span><span class="sxs-lookup"><span data-stu-id="5cb73-117">Improved compatibility with Roslyn and NuGet PackageReference</span></span>
- <span data-ttu-id="5cb73-118">添加了 `ef6.exe` 实体工具，用于启用、添加、编写脚本和应用程序集中的迁移。</span><span class="sxs-lookup"><span data-stu-id="5cb73-118">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="5cb73-119">这会替换 `migrate.exe`</span><span class="sxs-lookup"><span data-stu-id="5cb73-119">This replaces `migrate.exe`</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="5cb73-120">EF 设计器支持</span><span class="sxs-lookup"><span data-stu-id="5cb73-120">EF designer support</span></span>

<span data-ttu-id="5cb73-121">目前不支持直接在 .NET Core 或 .NET Standard 项目上使用 EF 设计器。</span><span class="sxs-lookup"><span data-stu-id="5cb73-121">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects.</span></span> 

<span data-ttu-id="5cb73-122">可以通过在同一解决方案中将 EDMX 文件以及为实体和 DbContext 生成的类作为链接文件添加到 .NET Core 3.0 或 .NET Standard 2.1 项目中来解决此限制。</span><span class="sxs-lookup"><span data-stu-id="5cb73-122">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="5cb73-123">在项目文件中，链接文件将如下所示：</span><span class="sxs-lookup"><span data-stu-id="5cb73-123">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="5cb73-124">请注意，EDMX 文件与 EntityDeploy 生成操作关联。</span><span class="sxs-lookup"><span data-stu-id="5cb73-124">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="5cb73-125">这是一个特殊的 MSBuild 任务（现已包含在 EF 6.3 包中），它负责将 EF 模型作为嵌入式资源添加到目标程序集中（或将其复制为输出文件夹中的文件，具体取决于 EDMX 中的“元数据项目处理”设置）。</span><span class="sxs-lookup"><span data-stu-id="5cb73-125">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="5cb73-126">有关如何进行此设置的详细信息，请参阅 [EDMX .NET Core 示例](https://aka.ms/EdmxDotNetCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="5cb73-126">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

## <a name="past-releases"></a><span data-ttu-id="5cb73-127">过去的版本</span><span class="sxs-lookup"><span data-stu-id="5cb73-127">Past Releases</span></span>

<span data-ttu-id="5cb73-128">[过去的版本](past-releases.md)页包含过去所有版本的 EF 的存档以及各版本中引入的主要功能。</span><span class="sxs-lookup"><span data-stu-id="5cb73-128">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
