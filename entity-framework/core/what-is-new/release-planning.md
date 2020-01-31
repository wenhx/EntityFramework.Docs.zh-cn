---
title: EF Core 版本规划
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/release_planning.md
ms.openlocfilehash: c60040aa4acc33ba8b5a54b619539b275690f70e
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125366"
---
# <a name="release-planning-process"></a><span data-ttu-id="1bb16-102">版本规划过程</span><span class="sxs-lookup"><span data-stu-id="1bb16-102">Release planning process</span></span>

<span data-ttu-id="1bb16-103">我们常常会被问到如何选择将添加到特定版本的特定功能。</span><span class="sxs-lookup"><span data-stu-id="1bb16-103">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="1bb16-104">积压工作 (backlog) 绝不会自动转换成发布计划。</span><span class="sxs-lookup"><span data-stu-id="1bb16-104">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="1bb16-105">如果 EF6 中有某项功能，也并不意味着需要在 EF Core 中实现此功能。</span><span class="sxs-lookup"><span data-stu-id="1bb16-105">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="1bb16-106">很难详细说明我们遵循的整个发布计划流程。</span><span class="sxs-lookup"><span data-stu-id="1bb16-106">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="1bb16-107">大多数都是在讨论具体功能、机会和优先级，而且流程本身也会随着每次发布而发生演变。</span><span class="sxs-lookup"><span data-stu-id="1bb16-107">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="1bb16-108">不过，我们可以总结在确定接下来工作内容时尝试回答的常见问题：</span><span class="sxs-lookup"><span data-stu-id="1bb16-108">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="1bb16-109">**我们认为有多少开发人员会使用该功能？该功能会使他们的应用程序/体验有多大的改善？**</span><span class="sxs-lookup"><span data-stu-id="1bb16-109">**How many developers we think will use the feature and how much better will it make their applications or experience?**</span></span> <span data-ttu-id="1bb16-110">为了回答这个问题，我们收集众多来源（其中包括对问题的评论和投票）的反馈。</span><span class="sxs-lookup"><span data-stu-id="1bb16-110">To answer this question, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="1bb16-111">**在尚未实现此功能的情况下，用户可用的变通方法是什么？**</span><span class="sxs-lookup"><span data-stu-id="1bb16-111">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="1bb16-112">例如，许多开发人员可以映射联接表，以解决缺少本机多对多支持的问题。</span><span class="sxs-lookup"><span data-stu-id="1bb16-112">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="1bb16-113">显然，并非所有开发人员都希望这样做，但很多开发人员都可以这样做，而这也作为决策的一个考虑因素。</span><span class="sxs-lookup"><span data-stu-id="1bb16-113">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="1bb16-114">**实现此功能是否有助于 EF Core 的体系结构发展，从而帮助我们实现其他功能？**</span><span class="sxs-lookup"><span data-stu-id="1bb16-114">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="1bb16-115">我们往往偏爱充当其他功能的构建基块的功能。</span><span class="sxs-lookup"><span data-stu-id="1bb16-115">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="1bb16-116">例如，属性包实体可有助于提供多对多支持，而且实体构造函数也启用了延迟加载支持。</span><span class="sxs-lookup"><span data-stu-id="1bb16-116">For instance, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="1bb16-117">**功能是可扩展性点吗？**</span><span class="sxs-lookup"><span data-stu-id="1bb16-117">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="1bb16-118">我们往往偏爱扩展点（而不是常规功能），因为它们能让开发人员挂钩自己的行为，并补偿缺少的任何功能。</span><span class="sxs-lookup"><span data-stu-id="1bb16-118">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="1bb16-119">**该功能与其他产品结合使用时的增效作用如何？**</span><span class="sxs-lookup"><span data-stu-id="1bb16-119">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="1bb16-120">我们往往偏爱能够实现或显著改善结合使用 EF Core 与其他产品（如 .NET Core、最新版 Visual Studio、Microsoft Azure 等）的体验的功能。</span><span class="sxs-lookup"><span data-stu-id="1bb16-120">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, and so on.</span></span>

6. <span data-ttu-id="1bb16-121">**从事功能开发工作的人员的技能如何？如何最充分地利用这些资源？**</span><span class="sxs-lookup"><span data-stu-id="1bb16-121">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="1bb16-122">EF 团队的每个成员以及我们的社区参与者都拥有各个领域的不同程度经验，我们需要相应地进行计划。</span><span class="sxs-lookup"><span data-stu-id="1bb16-122">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="1bb16-123">即便我们希望“全员就位”开发特定功能（如 GroupBy 转换或多对多支持），这也是不切实际的。</span><span class="sxs-lookup"><span data-stu-id="1bb16-123">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="1bb16-124">如前所述，发布计划流程在每次发布时都会发生演变。</span><span class="sxs-lookup"><span data-stu-id="1bb16-124">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="1bb16-125">今后，我们将尝试为社区成员提供更多机会，让他们能够对发布计划提供意见。</span><span class="sxs-lookup"><span data-stu-id="1bb16-125">In the future, we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="1bb16-126">例如，我们希望查看功能和发布计划本身的设计草稿能够变得更简单。</span><span class="sxs-lookup"><span data-stu-id="1bb16-126">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>

## <a name="backlog"></a><span data-ttu-id="1bb16-127">积压工作 (backlog)</span><span class="sxs-lookup"><span data-stu-id="1bb16-127">Backlog</span></span>

<span data-ttu-id="1bb16-128">在我们的问题跟踪程序中，[积压工作 (backlog) 里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)包含我们希望今后能解决的问题，或我们认为社区中的某人可以解决的问题。</span><span class="sxs-lookup"><span data-stu-id="1bb16-128">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we either expect to work on someday, or we think someone from the community could tackle.</span></span>
<span data-ttu-id="1bb16-129">客户可随时提交对这些问题的评论和投票。</span><span class="sxs-lookup"><span data-stu-id="1bb16-129">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="1bb16-130">建议着手解决其中任意问题的参与者先开始讨论如何处理这些问题。</span><span class="sxs-lookup"><span data-stu-id="1bb16-130">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="1bb16-131">我们从来没有保证过，将努力在特定版本的 EF Core 中提供任何给定功能。</span><span class="sxs-lookup"><span data-stu-id="1bb16-131">There's never a guarantee that we'll work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="1bb16-132">与所有软件项目一样，优先级、发布计划和可用资源可能会随时发生变化。</span><span class="sxs-lookup"><span data-stu-id="1bb16-132">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="1bb16-133">不过，如果我们打算在特定时间范围内解决某个问题，我们将会把问题分配到发布里程碑，而不是积压工作 (backlog) 里程碑。</span><span class="sxs-lookup"><span data-stu-id="1bb16-133">But if we intend to resolve an issue in a specific time-frame, we'll assign it to a release milestone instead of the backlog milestone.</span></span>

<span data-ttu-id="1bb16-134">如果我们不打算解决某个问题，可能会关闭它。</span><span class="sxs-lookup"><span data-stu-id="1bb16-134">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="1bb16-135">不过，对于之前关闭的问题，如果我们获得了它的新信息，也可以重新考虑解决它。</span><span class="sxs-lookup"><span data-stu-id="1bb16-135">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>
