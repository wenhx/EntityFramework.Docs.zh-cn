---
title: 新增功能 - EF6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 568790d9c9bb7dd2213907bef8fa090710cd3ba0
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149127"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="6d4b3-102">EF6 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="6d4b3-102">What's New in EF6</span></span>

<span data-ttu-id="6d4b3-103">强烈建议使用最新发布的实体框架版本，以确保获得最新功能和最高稳定性。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="6d4b3-104">但我们也意识到用户可能需要使用以前的版本，或想要尝试最新预发行版中的新改进。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="6d4b3-105">若要安装特定版本的 EF，请参阅[获取实体框架](~/ef6/fundamentals/install.md)。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

## <a name="ef-630"></a><span data-ttu-id="6d4b3-106">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="6d4b3-106">EF 6.3.0</span></span>

<span data-ttu-id="6d4b3-107">EF 6.3.0 运行时已于 2019 年 9 月发布到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-107">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="6d4b3-108">此版本的主要目标是帮助将使用 EF 6 的现有应用程序迁移到 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-108">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="6d4b3-109">社区还提供了多个 bug 修复和增强功能。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-109">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="6d4b3-110">有关详细信息，请参阅每个 6.3.0 [里程碑](https://github.com/aspnet/EntityFramework6/milestones?state=closed)中关闭的问题。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-110">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="6d4b3-111">下面是一些更值得注意的事项：</span><span class="sxs-lookup"><span data-stu-id="6d4b3-111">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="6d4b3-112">支持 .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="6d4b3-112">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="6d4b3-113">除了 .NET Framework 4.x，EntityFramework 包现在还面向 .NET Standard 2.1</span><span class="sxs-lookup"><span data-stu-id="6d4b3-113">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x</span></span>
  - <span data-ttu-id="6d4b3-114">已重新编写迁移命令以在进程外执行，并使用 SDK 样式的项目</span><span class="sxs-lookup"><span data-stu-id="6d4b3-114">The migrations commands have been rewritten to execute out of process and work with SDK-style projects</span></span>
- <span data-ttu-id="6d4b3-115">支持 SQL Server HierarchyId</span><span class="sxs-lookup"><span data-stu-id="6d4b3-115">Support for SQL Server HierarchyId</span></span>
- <span data-ttu-id="6d4b3-116">提高了与 Roslyn 和 NuGet PackageReference 的兼容性</span><span class="sxs-lookup"><span data-stu-id="6d4b3-116">Improved compatibility with Roslyn and NuGet PackageReference</span></span>
- <span data-ttu-id="6d4b3-117">添加了 ef6.exe，用于启用、添加、编写脚本和应用程序集中的迁移。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-117">Added ef6.exe for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="6d4b3-118">这会替换 migrate.exe</span><span class="sxs-lookup"><span data-stu-id="6d4b3-118">This replaces migrate.exe</span></span>

## <a name="past-releases"></a><span data-ttu-id="6d4b3-119">过去的版本</span><span class="sxs-lookup"><span data-stu-id="6d4b3-119">Past Releases</span></span>

<span data-ttu-id="6d4b3-120">[过去的版本](past-releases.md)页包含过去所有版本的 EF 的存档以及各版本中引入的主要功能。</span><span class="sxs-lookup"><span data-stu-id="6d4b3-120">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
