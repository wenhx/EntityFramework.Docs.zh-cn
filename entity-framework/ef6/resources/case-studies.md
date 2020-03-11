---
title: 实体框架的案例研究-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: d7982a3f89ac1e57b48039d828f287adf6dc5068
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414457"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>实体框架的 Microsoft 案例研究
此页上的案例研究重点介绍了几个实体框架的实际生产项目。
> [!NOTE]
> 这些案例研究的详细版本在 Microsoft 网站上不再可用。 因此，这些链接已被删除。

## <a name="epicor"></a>Epicor
Epicor 是一家大型的全球软件公司（超过400个开发人员），可为超过150个国家/地区的公司开发企业资源规划（ERP）解决方案。
其旗舰版产品 Epicor 9 基于面向服务的体系结构（SOA），该体系结构使用 .NET Framework。
面对大量客户请求以提供对语言集成查询（LINQ）的支持，并希望在其后端 SQL Server 上减少负载，团队决定升级到 Visual Studio 2010 和 .NET Framework 4.0。
使用实体框架4.0，它们能够实现这些目标，同时大大简化了开发和维护工作。
具体而言，实体框架的丰富 T4 支持使其能够完全控制其生成的代码，并自动生成性能保存功能，如预编译查询和缓存。

> "我们最近使用现有代码执行了一些性能测试，并可以减少 90% SQL Server 的请求数。
这是因为 ADO.NET 实体框架 4. " – Erik Johnson，副总裁，产品研究  

## <a name="veracity-solutions"></a>精确性解决方案
收购了一个事件规划软件系统，该系统会很难维护和扩展精确性的长期解决方案，使用 Visual Studio 2010 将其重新编写为内置于 Silverlight 4 的功能强大且易于使用的丰富 Internet 应用程序。
使用 .NET RIA 服务，他们能够在实体框架上快速构建一个服务层，以避免在不同的层对常见验证和身份验证逻辑进行代码重复和允许。  

> "我们在第一次推出时实体框架销售，而实体框架4已证实更好。
工具经过改进，可以更轻松地操作定义概念模型、存储模型和这些模型之间的映射的 .edmx 文件。使用实体框架，我可以将数据访问层作为一天的工作，并在我开始时进行构建。
实体框架是我们事实上的数据访问层;我不知道什么人不会使用它。 " – Joe McBride，资深开发人员

## <a name="nec-display-solutions-of-america"></a>北美洲显示的 NEC 解决方案
NEC 需要为基于数字位置的广告输入市场，并提供一种解决方案，让广告商和网络所有者受益并增加自己的收入。
为此，它会启动一对 web 应用程序，以自动执行传统 ad 活动中需要的手动过程。
这些站点是使用 ASP.NET、Silverlight 3、AJAX 和 WCF 生成的，以及在数据访问层中的实体框架与 SQL Server 2008 进行通信。

> "借助 SQL Server，我们认为我们可以获得所需的吞吐量，以提供实时信息和可靠性来帮助广告商和网络，从而帮助确保关键任务应用程序中的信息始终可用"-Mike CorcoranIT 主管

## <a name="darwin-dimensions"></a>达尔文维度
使用范围广泛的 Microsoft 技术，达尔文的团队设置为创建 Evolver-一个在线虚拟形象门户，使用者可以使用它创建令人惊叹的、逼真的头像，以便在游戏、动画和社交网络页面上使用。
随着实体框架的工作效率优势，并请求 Windows Workflow Foundation （WF）和 Windows Server AppFabric （一种高度可缩放的内存中应用程序缓存）等组件，团队能够在35% 的时间内提供令人惊叹的产品开发时间。
即使团队成员拆分到多个国家/地区，团队仍遵循每周发布的敏捷开发流程。

 > "我们不会尝试创建技术来实现技术。 作为一种启动方式，我们需要利用可节省时间和资金的技术，这一点非常重要。
 .NET 是用于快速、经济高效的开发的选择。 – Zachary Olsen，建筑师  

## <a name="silverware"></a>Silverware
由于为中小型餐厅组开发销售销售点（POS）解决方案的经验超过了15年，Silverware 的开发团队将其产品设置为通过更多的企业级功能来增强其产品，以吸引更大餐馆链。
使用最新版本的 Microsoft 开发工具，能够比以前更快地构建新解决方案。
LINQ 和实体框架等关键新功能使得从 Crystal 报表移到 SQL Server 2008 和 SQL Server Reporting Services （SSRS），以实现其数据存储和报告需求。

> "有效的数据管理是 SilverWare 成功的关键，这就是我们决定采用 SQL Reporting 的原因。" -IT/软件工程主管 Nicholas Romanidis
