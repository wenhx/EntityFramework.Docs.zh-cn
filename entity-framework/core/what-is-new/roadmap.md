---
title: Entity Framework Core 路线图
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: e9d1a707c643036fa15b1e0e46730eadefd65518
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562463"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="f0b3e-102">Entity Framework Core 路线图</span><span class="sxs-lookup"><span data-stu-id="f0b3e-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f0b3e-103">请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

## <a name="ef-core-30"></a><span data-ttu-id="f0b3e-104">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="f0b3e-104">EF Core 3.0</span></span>

<span data-ttu-id="f0b3e-105">EF Core 2.2 发布后，我们现在将重点放在 EF Core 3.0 上。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-105">With EF Core 2.2 out the door, our main focus is now EF Core 3.0.</span></span>
<span data-ttu-id="f0b3e-106">参见 [EF Core 3.0 中的新增功能](xref:core/what-is-new/ef-core-3.0/index)，了解发布内容中计划的[新功能](xref:core/what-is-new/ef-core-3.0/features)和[中断性变更](xref:core/what-is-new/ef-core-3.0/breaking-changes)。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-106">See [What's new in EF Core 3.0](xref:core/what-is-new/ef-core-3.0/index) for information on planned [new features](xref:core/what-is-new/ef-core-3.0/features) and intentional [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes) included in the release.</span></span>

## <a name="schedule"></a><span data-ttu-id="f0b3e-107">计划</span><span class="sxs-lookup"><span data-stu-id="f0b3e-107">Schedule</span></span>

<span data-ttu-id="f0b3e-108">EF Core 的发布计划与 [.NET Core 发布计划](https://github.com/dotnet/core/blob/master/roadmap.md)同步。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-108">The release schedule for EF Core is in-sync with the [.NET Core release schedule](https://github.com/dotnet/core/blob/master/roadmap.md).</span></span>

## <a name="backlog"></a><span data-ttu-id="f0b3e-109">积压工作 (backlog) </span><span class="sxs-lookup"><span data-stu-id="f0b3e-109">Backlog</span></span>

<span data-ttu-id="f0b3e-110">在我们的问题跟踪程序中，[积压工作 (backlog) 里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)包含我们希望今后能解决的问题，或我们认为社区中的某人可以解决的问题。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-110">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we expect to work on someday, or that we think someone from the community could tackle.</span></span>
<span data-ttu-id="f0b3e-111">客户可随时提交对这些问题的评论和投票。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-111">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="f0b3e-112">建议着手解决其中任意问题的参与者先开始讨论如何处理这些问题。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-112">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="f0b3e-113">我们从来没有保证过，将努力在特定版本的 EF Core 中提供任何给定功能。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-113">There is never a guarantee that we will work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="f0b3e-114">与所有软件项目一样，优先级、发布计划和可用资源可能会随时发生变化。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-114">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="f0b3e-115">不过，如果我们打算在特定时间范围内解决某个问题，我们将会把问题分配到发布里程碑，而不是积压工作 (backlog) 里程碑。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-115">But if we intend to resolve an issue in a specific timeframe, we'll assign it to a release milestone instead of the backlog milestone.</span></span>
<span data-ttu-id="f0b3e-116">在[发布计划流程](#release-planning-process)中，我们会定期在积压工作 (backlog) 里程碑和发布里程碑之间移动问题。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-116">We routinely move issues between the backlog and release milestones as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="f0b3e-117">如果我们不打算解决某个问题，可能会关闭它。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-117">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="f0b3e-118">不过，对于之前关闭的问题，如果我们获得了它的新信息，也可以重新考虑解决它。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-118">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="f0b3e-119">版本规划过程</span><span class="sxs-lookup"><span data-stu-id="f0b3e-119">Release planning process</span></span>

<span data-ttu-id="f0b3e-120">我们常常会被问到如何选择将添加到特定版本的特定功能。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-120">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="f0b3e-121">积压工作 (backlog) 绝不会自动转换成发布计划。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-121">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="f0b3e-122">如果 EF6 中有某项功能，也并不意味着需要在 EF Core 中实现此功能。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-122">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="f0b3e-123">很难详细说明我们遵循的整个发布计划流程。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-123">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="f0b3e-124">大多数都是在讨论具体功能、机会和优先级，而且流程本身也会随着每次发布而发生演变。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-124">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="f0b3e-125">不过，我们可以总结在确定接下来工作内容时尝试回答的常见问题：</span><span class="sxs-lookup"><span data-stu-id="f0b3e-125">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="f0b3e-126">**我们认为有多少开发人员会使用该功能？该功能会使他们的应用程序/体验有多大的改善？**</span><span class="sxs-lookup"><span data-stu-id="f0b3e-126">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="f0b3e-127">为了回答这个问题，我们收集众多来源（其中包括对问题的评论和投票）的反馈。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-127">To answer this, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="f0b3e-128">**在尚未实现此功能的情况下，用户可用的变通方法是什么？**</span><span class="sxs-lookup"><span data-stu-id="f0b3e-128">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="f0b3e-129">例如，许多开发人员可以映射联接表，以解决缺少本机多对多支持的问题。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-129">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="f0b3e-130">显然，并非所有开发人员都希望这样做，但很多开发人员都可以这样做，而这也作为决策的一个考虑因素。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-130">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="f0b3e-131">**实现此功能是否有助于 EF Core 的体系结构发展，从而帮助我们实现其他功能？**</span><span class="sxs-lookup"><span data-stu-id="f0b3e-131">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="f0b3e-132">我们往往偏爱充当其他功能的构建基块的功能。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-132">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="f0b3e-133">例如，属性包实体可有助于提供多对多支持，而且实体构造函数也启用了延迟加载支持。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-133">For example, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="f0b3e-134">**功能是可扩展性点吗？**</span><span class="sxs-lookup"><span data-stu-id="f0b3e-134">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="f0b3e-135">我们往往偏爱扩展点（而不是常规功能），因为它们能让开发人员挂钩自己的行为，并补偿缺少的任何功能。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-135">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="f0b3e-136">**该功能与其他产品结合使用时的增效作用如何？**</span><span class="sxs-lookup"><span data-stu-id="f0b3e-136">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="f0b3e-137">我们往往偏爱能够实现或显著改善结合使用 EF Core 与其他产品（如 .NET Core、最新版 Visual Studio、Microsoft Azure 等）的体验的功能。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-137">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="f0b3e-138">**从事功能开发工作的人员的技能如何？如何最充分地利用这些资源？**</span><span class="sxs-lookup"><span data-stu-id="f0b3e-138">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="f0b3e-139">EF 团队的每个成员以及我们的社区参与者都拥有各个领域的不同程度经验，我们需要相应地进行计划。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-139">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="f0b3e-140">即便我们希望“全员就位”开发特定功能（如 GroupBy 转换或多对多支持），这也是不切实际的。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-140">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="f0b3e-141">如前所述，发布计划流程在每次发布时都会发生演变。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-141">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="f0b3e-142">今后，我们将尝试为社区成员提供更多机会，让他们能够对发布计划提供意见。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-142">In the future we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="f0b3e-143">例如，我们希望查看功能和发布计划本身的设计草稿能够变得更简单。</span><span class="sxs-lookup"><span data-stu-id="f0b3e-143">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>
