---
title: Entity Framework Core 路线图
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: a12d628a28515f0c6710bfa59bc6dcdf41fcb58b
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688584"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="c2efd-102">Entity Framework Core 路线图</span><span class="sxs-lookup"><span data-stu-id="c2efd-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c2efd-103">请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。</span><span class="sxs-lookup"><span data-stu-id="c2efd-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

### <a name="ef-core-22"></a><span data-ttu-id="c2efd-104">EF Core 2.2</span><span class="sxs-lookup"><span data-stu-id="c2efd-104">EF Core 2.2</span></span>

<span data-ttu-id="c2efd-105">此版本将包含多个 Bug 修复和少量新功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-105">This release will include many bug fixes, and relatively small number of new features.</span></span> <span data-ttu-id="c2efd-106">此版本计划于 2018 年底发布。</span><span class="sxs-lookup"><span data-stu-id="c2efd-106">This release is planned for end of year 2018.</span></span> <span data-ttu-id="c2efd-107">若要详细了解此版本，请参阅 [EF Core 2.2 中的新变化](xref:core/what-is-new/ef-core-2.2)。</span><span class="sxs-lookup"><span data-stu-id="c2efd-107">Details about this release are included in [What's new in EF Core 2.2](xref:core/what-is-new/ef-core-2.2).</span></span> 

### <a name="ef-core-30"></a><span data-ttu-id="c2efd-108">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="c2efd-108">EF Core 3.0</span></span>

<span data-ttu-id="c2efd-109">我们计划发布与 .NET Core 3.0 和 ASP.NET 3.0 对接的主要 EF Core 版本，但尚未完成它的[发布计划流程](#release-planning-process)。</span><span class="sxs-lookup"><span data-stu-id="c2efd-109">We plan to have major EF Core release aligned with .NET Core 3.0 and ASP.NET 3.0, but we haven't completed the [release planning process](#release-planning-process) for it.</span></span>

<span data-ttu-id="c2efd-110">若要查看暂时分配给 3.0 的工作项，请[在我们的问题跟踪程序中使用此查询](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="c2efd-110">Use [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc) to see work items tentatively assigned to 3.0.</span></span>

## <a name="schedule"></a><span data-ttu-id="c2efd-111">计划</span><span class="sxs-lookup"><span data-stu-id="c2efd-111">Schedule</span></span>

<span data-ttu-id="c2efd-112">EF Core 的计划与 [.NET Core 计划](https://github.com/dotnet/core/blob/master/roadmap.md)以及 [ASP.NET Core 计划](https://github.com/aspnet/Home/wiki/Roadmap)同步。</span><span class="sxs-lookup"><span data-stu-id="c2efd-112">The schedule for EF Core is in-sync with the [.NET Core schedule](https://github.com/dotnet/core/blob/master/roadmap.md) and [ASP.NET Core schedule](https://github.com/aspnet/Home/wiki/Roadmap).</span></span>

## <a name="backlog"></a><span data-ttu-id="c2efd-113">积压工作 (backlog)</span><span class="sxs-lookup"><span data-stu-id="c2efd-113">Backlog</span></span>

<span data-ttu-id="c2efd-114">在我们的问题跟踪程序中，[积压工作 (backlog) 里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)包含我们希望今后能解决的问题，或我们认为社区中的某人可以解决的问题。</span><span class="sxs-lookup"><span data-stu-id="c2efd-114">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we expect to work on someday, or that we think someone from the community could tackle.</span></span>
<span data-ttu-id="c2efd-115">客户可随时提交对这些问题的评论和投票。</span><span class="sxs-lookup"><span data-stu-id="c2efd-115">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="c2efd-116">建议着手解决其中任意问题的参与者先开始讨论如何处理这些问题。</span><span class="sxs-lookup"><span data-stu-id="c2efd-116">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="c2efd-117">我们从来没有保证过，将努力在特定版本的 EF Core 中提供任何给定功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-117">There is never a guarantee that we will work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="c2efd-118">与所有软件项目一样，优先级、发布计划和可用资源可能会随时发生变化。</span><span class="sxs-lookup"><span data-stu-id="c2efd-118">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="c2efd-119">不过，如果我们打算在特定时间范围内解决某个问题，我们将会把问题分配到发布里程碑，而不是积压工作 (backlog) 里程碑。</span><span class="sxs-lookup"><span data-stu-id="c2efd-119">But if we intend to resolve an issue in a specific timeframe, we'll assign it to a release milestone instead of the backlog milestone.</span></span>
<span data-ttu-id="c2efd-120">在[发布计划流程](#release-planning-process)中，我们会定期在积压工作 (backlog) 里程碑和发布里程碑之间移动问题。</span><span class="sxs-lookup"><span data-stu-id="c2efd-120">We routinely move issues between the backlog and release milestones as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="c2efd-121">如果我们不打算解决某个问题，可能会关闭它。</span><span class="sxs-lookup"><span data-stu-id="c2efd-121">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="c2efd-122">不过，对于之前关闭的问题，如果我们获得了它的新信息，也可以重新考虑解决它。</span><span class="sxs-lookup"><span data-stu-id="c2efd-122">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="c2efd-123">版本规划过程</span><span class="sxs-lookup"><span data-stu-id="c2efd-123">Release planning process</span></span>

<span data-ttu-id="c2efd-124">我们常常会被问到如何选择将添加到特定版本的特定功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-124">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="c2efd-125">积压工作 (backlog) 绝不会自动转换成发布计划。</span><span class="sxs-lookup"><span data-stu-id="c2efd-125">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="c2efd-126">如果 EF6 中有某项功能，也并不意味着需要在 EF Core 中实现此功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-126">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="c2efd-127">很难详细说明我们遵循的整个发布计划流程。</span><span class="sxs-lookup"><span data-stu-id="c2efd-127">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="c2efd-128">大多数都是在讨论具体功能、机会和优先级，而且流程本身也会随着每次发布而发生演变。</span><span class="sxs-lookup"><span data-stu-id="c2efd-128">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="c2efd-129">不过，我们可以总结在确定接下来工作内容时尝试回答的常见问题：</span><span class="sxs-lookup"><span data-stu-id="c2efd-129">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="c2efd-130">**我们认为有多少开发人员会使用该功能？该功能会使他们的应用程序/体验有多大的改善？**</span><span class="sxs-lookup"><span data-stu-id="c2efd-130">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="c2efd-131">为了回答这个问题，我们收集众多来源（其中包括对问题的评论和投票）的反馈。</span><span class="sxs-lookup"><span data-stu-id="c2efd-131">To answer this, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="c2efd-132">**在尚未实现此功能的情况下，用户可用的变通方法是什么？**</span><span class="sxs-lookup"><span data-stu-id="c2efd-132">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="c2efd-133">例如，许多开发人员可以映射联接表，以解决缺少本机多对多支持的问题。</span><span class="sxs-lookup"><span data-stu-id="c2efd-133">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="c2efd-134">显然，并非所有开发人员都希望这样做，但很多开发人员都可以这样做，而这也作为决策的一个考虑因素。</span><span class="sxs-lookup"><span data-stu-id="c2efd-134">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="c2efd-135">**实现此功能是否有助于 EF Core 的体系结构发展，从而帮助我们实现其他功能？**</span><span class="sxs-lookup"><span data-stu-id="c2efd-135">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="c2efd-136">我们往往偏爱充当其他功能的构建基块的功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-136">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="c2efd-137">例如，属性包实体可有助于提供多对多支持，而且实体构造函数也启用了延迟加载支持。</span><span class="sxs-lookup"><span data-stu-id="c2efd-137">For example, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span> 

4. <span data-ttu-id="c2efd-138">**功能是可扩展性点吗？**</span><span class="sxs-lookup"><span data-stu-id="c2efd-138">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="c2efd-139">我们往往偏爱扩展点（而不是常规功能），因为它们能让开发人员挂钩自己的行为，并补偿缺少的任何功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-139">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span> 

5. <span data-ttu-id="c2efd-140">**该功能与其他产品结合使用时的增效作用如何？**</span><span class="sxs-lookup"><span data-stu-id="c2efd-140">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="c2efd-141">我们往往偏爱能够实现或显著改善结合使用 EF Core 与其他产品（如 .NET Core、最新版 Visual Studio、Microsoft Azure 等）的体验的功能。</span><span class="sxs-lookup"><span data-stu-id="c2efd-141">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="c2efd-142">**从事功能开发工作的人员的技能如何？如何最充分地利用这些资源？**</span><span class="sxs-lookup"><span data-stu-id="c2efd-142">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="c2efd-143">EF 团队的每个成员以及我们的社区参与者都拥有各个领域的不同程度经验，我们需要相应地进行计划。</span><span class="sxs-lookup"><span data-stu-id="c2efd-143">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="c2efd-144">即便我们希望“全员就位”开发特定功能（如 GroupBy 转换或多对多支持），这也是不切实际的。</span><span class="sxs-lookup"><span data-stu-id="c2efd-144">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="c2efd-145">如前所述，发布计划流程在每次发布时都会发生演变。</span><span class="sxs-lookup"><span data-stu-id="c2efd-145">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="c2efd-146">今后，我们将尝试为社区成员提供更多机会，让他们能够对发布计划提供意见。</span><span class="sxs-lookup"><span data-stu-id="c2efd-146">In the future we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="c2efd-147">例如，我们希望查看功能和发布计划本身的设计草稿能够变得更简单。</span><span class="sxs-lookup"><span data-stu-id="c2efd-147">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>