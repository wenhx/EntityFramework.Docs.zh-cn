---
title: EF Core 版本规划
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning.md
ms.openlocfilehash: 71045b8d49c319a73f74443612bedd84ee33ab8a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413539"
---
# <a name="release-planning-process"></a>版本规划过程

我们常常会被问到如何选择将添加到特定版本的特定功能。
该文档概述了我们采用的过程。
随着我们找到更好的规划方法，该过程在不断演变，但总体思路仍保持不变。

## <a name="different-kinds-of-releases"></a>不同类型的版本

不同类型的版本包含不同类型的更改。
这反过来意味着对不同类型的版本而言，版本规划是不同的。

### <a name="patch-releases"></a>修补程序版本

修补程序版本只更改了版本的“修补程序”部分。
例如，EF Core 3.1.1 是修补在 EF Core 3.1.0 中发现的问题的版本   。

修补程序版本旨在修复关键客户 bug。
这意味着修补程序版本中没有新功能。
在特殊情况下，修补程序版本中不允许包含 API 更改。

在修补程序版本中进行更改的门槛很高。
这是因为有必要保证修补程序版本不会引入新的 bug。
因此，决策过程着重于高价值和低风险。

在下述情况中，我们修补问题的可能性更大：
  * 该问题会影响多个客户
  * 该问题与上一版本相比属于性能退化
  * 失败会导致数据损坏

在下述情况中，我们修补问题的可能性更小：
  * 有合理的解决方法
  * 此修补程序极可能损坏其他某些功能
  * bug 处于极端情况

在[长期支持 (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 版本的整个生存期内，这一门槛逐渐升高。 这是因为 LTS 版本着重于稳定性。

关于是否修复某问题的最终决定由 Microsoft 的 .NET 主管作出。

### <a name="minor-releases"></a>次要版本

次要版本只更改了版本的“次要”部分。
例如，EF Core 3.1.0 是对 EF Core 3.0.0 进行改进的一个版本   。

次要版本：
* 旨在提升上一版本的质量和功能
* 通常包含 bug 修复和新功能
* 不包含有意的中断性变更
* 有一些推送给 NuGet 的预发行预览版

### <a name="major-releases"></a>主要版本

主要版本更改的是 EF“主要”版本号。
例如，EF Core 3.0.0 是与 EF Core 2.2.x 相比性能大幅提升的一个主要版本  。

主要版本：
* 旨在提升上一版本的质量和功能
* 通常包含 bug 修复和新功能
  * 某些新功能可能在根本上更改了 EF Core 工作的方式
* 通常包含有意的中断性变更
  * 就我们知道的而言，中断性变更是 EF Core 演变的必要部分
  * 但是，由于可能对客户造成影响，我们在进行任何中断性变更方面考虑谨慎。 过程，我们在中断性变更方面过于激进。 而今后，我们将努力最大程度减少会使应用程序中断的更改，同时努力减少会使数据库提供程序和扩展中断的更改。
* 有很多推送给 NuGet 的预发行预览版

## <a name="planning-for-majorminor-releases"></a>主要/次要版本规划

### <a name="github-issue-tracking"></a>GitHub 问题跟踪

对于所有 EF Core 规划来说，GitHub ([https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)) 都是可靠来源。

GitHub 上的问题具有：

* 状态
  * [Open](https://github.com/dotnet/efcore/issues) 问题尚未得到解决。
  * [Closed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) 问题已得到解决。
    * 所有已解决的问题都[标记了 closed-fixed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed)。 标记有 closed-fixed 的问题已得到修复且已合并，但可能尚未发布。
    * 其他 `closed-` 标签指出了关闭问题的其他原因。 例如，重复问题标记有 closed-duplicate。
* 类型
  * [Bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) 表示错误。
  * [增强功能](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement)表示新功能或现有功能中的更优功能。
* 里程碑
  * [没有里程碑的问题](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone)由团队进行处理。 尚未作出有关如何处理问题的决定，或者对决定的某项更改正在探讨中。
  * [积压工作 (backlog) 里程碑中的问题](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog)是指 EF 团队将考虑在某个未来版本中处理的项目
    * 积压工作 (backlog) 中的问题可能[标记有 consider-for-next-release](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release)，这是指该工作项在下一版本列表中的排名靠前。
  * 经版本控制的里程碑中的待解决问题是指团队计划在该版本中处理的项目。 例如，[这些是我们计划针对 EF Core 5.0 进行处理的问题](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0)。
  * 经版本控制的里程碑中的已关闭问题是指对该版本而言已完成的问题。 请注意，该版本可能尚未发布。 例如，[这些是我们计划针对 EF Core 3.0 已完成的问题](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed)。
* 请投票！
  * 投票是指出某个问题对你而言很重要的最佳方式。
  * 只需向问题添加“大拇指朝上”👍 即可进行投票。 例如，[这些是得票数靠前的问题](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)
  * 如果你认为添加评论会提高重要性，还请添加描述你为何需要该功能的特定原因。 评论“+1”（即赞同）或给出类似评论不会提高重要性。

### <a name="the-planning-process"></a>规划过程

与从积压工作 (backlog) 中提取被请求最多次的请求而言，规划过程的参与度更高。
这是因为我们会用多种方式从多名利益干系人那里收集反馈。
然后，我们会根据下列内容调整版本：

* 客户输入的内容
* 其他利益干系人输入的内容
* 战略方向
* 可用资源
* 计划

我们提出的一些问题是：

1. **我们认为有多少开发人员会使用该功能？该功能会使他们的应用程序/体验有多大的改善？** 为了回答这个问题，我们收集众多来源（其中包括对问题的评论和投票）的反馈。 另一方面是与重要客户的具体互动。

2. **在尚未实现此功能的情况下，用户可用的变通方法是什么？** 例如，许多开发人员可以映射联接表，以解决缺少本机多对多支持的问题。 显然，并非所有开发人员都希望这样做，但很多开发人员都可以这样做，而这也作为决策的一个考虑因素。

3. **实现此功能是否有助于 EF Core 的体系结构发展，从而帮助我们实现其他功能？** 我们往往偏爱充当其他功能的构建基块的功能。 例如，属性包实体可有助于提供多对多支持，而且实体构造函数也启用了延迟加载支持。

4. **功能是可扩展性点吗？** 我们往往偏爱扩展点（而不是常规功能），因为它们能让开发人员挂钩自己的行为，并补偿缺少的任何功能。

5. **该功能与其他产品结合使用时的增效作用如何？** 我们往往偏爱能够实现或显著改善结合使用 EF Core 与其他产品（如 .NET Core、最新版 Visual Studio、Microsoft Azure 等）的体验的功能。

6. **从事功能开发工作的人员的技能如何？如何能最充分地利用这些资源？** EF 团队的每个成员以及我们的社区参与者都拥有各个领域的不同程度经验，我们需要相应地进行计划。 即便我们希望“全员就位”开发特定功能（如 GroupBy 转换或多对多支持），这也是不切实际的。
