---
title: Entity Framework Core 路线图
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: 7eba9e1a8e145ef407f844ff3a3ab3069495b7ae
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058729"
---
# <a name="entity-framework-core-roadmap"></a>Entity Framework Core 路线图

> [!IMPORTANT]
> 请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。

### <a name="ef-core-30"></a>EF Core 3.0

随着 EF Core 2.2 的发布，我们现在的重点是 EF Core 3.0，它将与 .NET Core 3.0 和 ASP.NET 3.0 版本保持一致。

我们尚未完成任何新功能，因此，于 2018 年 12 月 [向 NuGet 库发布的 EF Core 3.0 预览版 1 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.0-preview.18572.1) 仅包含 [bug 修复、较小的改进以及我们在准备 3.0 版本时所做的更改](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed+label%3Aclosed-fixed)。

事实上，我们仍然需要完善 3.0 版本的[发布计划](#release-planning-process)，以确保我们能在分配的时间内完成相应的功能集。
当我们对此更为清楚的时候，我们会分享更多信息，但以下是我们想要致力于开发的一些高级主题和功能：

- **LINQ 改进 ([#12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795))**：LINQ 可便于编写数据库查询，而无需离开所选的语言，同时还能利用丰富的类型信息来获取 IntelliSense 和编译时类型检查。
  不过，LINQ 也支持编写数量不限的复杂查询，而这对于 LINQ 提供程序来说，一直都是一项巨大挑战。
  在之前的几个 EF Core 版本中，我们找出了能够转换为 SQL 的查询的部分，并允许查询的其余部分在客户端的内存中执行，从而部分解决了这一问题。
  在某些情况下，此客户端执行是可取的，但在其他许多情况下，这可能会导致低效的查询，直到应用程序被部署到生产时才被发现。
  在 EF Core 3.0 中，我们计划对我们的 LINQ 实现的工作原理以及测试它的方式进行重大更改。
  目标是使其更为可靠（例如，避免修补程序版本中的查询中断）、使其能够将更多表达式正确地转换为 SQL、使其在更多场景中生成更有效的查询以及防止无法检测到效率低下的查询。

- **Cosmos DB 支持 ([#8443](https://github.com/aspnet/EntityFrameworkCore/issues/8443))**：我们正在致力于开发适用于 EF Core 的 Cosmos DB 提供程序，以便开发人员能够熟悉 EF 编程模型，从而轻松地将 Azure Cosmos DB 定目标为应用程序数据库。
  目标是利用 Cosmos DB 的一些优势，如全局分发、“始终开启”可用性、弹性可伸缩性和低延迟，甚至包括 .NET 开发人员可以更轻松地访问它。
  此提供程序将针对 Cosmos DB 中的 SQL API 启用大部分 EF Core 功能，如自动更改跟踪、LINQ 和值转换。 我们在 EF Core 2.2 之前开始这一开发，并且[我们已发布了一些提供程序的预览版](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。
  新的计划是与 EF Core 3.0 一起继续开发提供程序。   

- **C# 8.0 支持 ([#12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047))**：我们希望客户利用 [C# 8.0 中推出的一些新功能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)，如在使用 EF Core 的同时使用异步流（对每个都有 await）和可以为 null 的引用类型。

- **将工程数据库视图反向为查询类型 ([#1679](https://github.com/aspnet/EntityFrameworkCore/issues/1679))**：在 EF Core 2.1 中，我们添加了对查询类型的支持，该功能可以表示可从数据库中读取的数据，但无法对其进行更新。
  查询类型非常适合于映射数据库视图，因此，在 EF Core 3.0 中，我们想要自动创建数据库视图的查询类型。

- **属性包实体（[#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) 和 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)）**：此功能可以启用在索引的属性（而非常规属性）中存储数据的实体，并能够使用同一 .NET 类的实例（可能像 `Dictionary<string, object>` 一样简单）来表示同一 EF Core 模型中的不同的实体类型。
  此功能是支持多对多关系而无需联接实体的基石，是针对 EF Core 提出改进要求最多的一项功能。

- **.NET Core 上的 EF 6.3 ([EF6 #271](https://github.com/aspnet/EntityFramework6/issues/271))**：我们知道许多现有应用程序使用以前版本的 EF，而仅为了利用 .NET Core 将其移植到 EF Core 有时需要大量工作。
  为此，我们将对下一版本的 EF 6 进行调整，以在 .NET Core 3.0 上运行。
  我们这样做是为了在尽可能减少更改的情况下推动现有应用程序的移植。
  这样做会存在一些限制（例如，它将需要新提供程序、不会启用对 SQL Server 的空间支持），且没有针对 EF 6 计划的新功能。

同时，若要查看暂时分配给 3.0 的工作项，可以[在我们的问题跟踪程序中使用此查询](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)。

## <a name="schedule"></a>计划

EF Core 的计划与 [.NET Core 计划](https://github.com/dotnet/core/blob/master/roadmap.md)以及 [ASP.NET Core 计划](https://github.com/aspnet/Home/wiki/Roadmap)同步。

## <a name="backlog"></a>积压工作 (backlog)

在我们的问题跟踪程序中，[积压工作 (backlog) 里程碑](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)包含我们希望今后能解决的问题，或我们认为社区中的某人可以解决的问题。
客户可随时提交对这些问题的评论和投票。
建议着手解决其中任意问题的参与者先开始讨论如何处理这些问题。

我们从来没有保证过，将努力在特定版本的 EF Core 中提供任何给定功能。
与所有软件项目一样，优先级、发布计划和可用资源可能会随时发生变化。
不过，如果我们打算在特定时间范围内解决某个问题，我们将会把问题分配到发布里程碑，而不是积压工作 (backlog) 里程碑。
在[发布计划流程](#release-planning-process)中，我们会定期在积压工作 (backlog) 里程碑和发布里程碑之间移动问题。

如果我们不打算解决某个问题，可能会关闭它。
不过，对于之前关闭的问题，如果我们获得了它的新信息，也可以重新考虑解决它。

## <a name="release-planning-process"></a>版本规划过程

我们常常会被问到如何选择将添加到特定版本的特定功能。
积压工作 (backlog) 绝不会自动转换成发布计划。
如果 EF6 中有某项功能，也并不意味着需要在 EF Core 中实现此功能。

很难详细说明我们遵循的整个发布计划流程。
大多数都是在讨论具体功能、机会和优先级，而且流程本身也会随着每次发布而发生演变。
不过，我们可以总结在确定接下来工作内容时尝试回答的常见问题：

1. **我们认为有多少开发人员会使用该功能？该功能会使他们的应用程序/体验有多大的改善？** 为了回答这个问题，我们收集众多来源（其中包括对问题的评论和投票）的反馈。

2. **在尚未实现此功能的情况下，用户可用的变通方法是什么？** 例如，许多开发人员可以映射联接表，以解决缺少本机多对多支持的问题。 显然，并非所有开发人员都希望这样做，但很多开发人员都可以这样做，而这也作为决策的一个考虑因素。

3. **实现此功能是否有助于 EF Core 的体系结构发展，从而帮助我们实现其他功能？** 我们往往偏爱充当其他功能的构建基块的功能。 例如，属性包实体可有助于提供多对多支持，而且实体构造函数也启用了延迟加载支持。 

4. **功能是可扩展性点吗？** 我们往往偏爱扩展点（而不是常规功能），因为它们能让开发人员挂钩自己的行为，并补偿缺少的任何功能。 

5. **该功能与其他产品结合使用时的增效作用如何？** 我们往往偏爱能够实现或显著改善结合使用 EF Core 与其他产品（如 .NET Core、最新版 Visual Studio、Microsoft Azure 等）的体验的功能。

6. **从事功能开发工作的人员的技能如何？如何最充分地利用这些资源？** EF 团队的每个成员以及我们的社区参与者都拥有各个领域的不同程度经验，我们需要相应地进行计划。 即便我们希望“全员就位”开发特定功能（如 GroupBy 转换或多对多支持），这也是不切实际的。

如前所述，发布计划流程在每次发布时都会发生演变。
今后，我们将尝试为社区成员提供更多机会，让他们能够对发布计划提供意见。
例如，我们希望查看功能和发布计划本身的设计草稿能够变得更简单。
