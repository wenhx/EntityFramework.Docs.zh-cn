---
title: Entity Framework 6 概述 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8ae74d63-6bad-4686-b325-bbf9d68f3743
uid: ef6/index
ms.openlocfilehash: 28a13879416a52cbe8035c23013f16390c75c4c9
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78412752"
---
# <a name="entity-framework-6"></a><span data-ttu-id="07360-102">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="07360-102">Entity Framework 6</span></span>
<span data-ttu-id="07360-103">实体框架 6 (EF6) 是经试验和测试的关系映射器 (O/RM)，适用于 .NET 的对象，其功能和稳定性经过了多年的开发和调试。</span><span class="sxs-lookup"><span data-stu-id="07360-103">Entity Framework 6 (EF6) is a tried and tested object-relational mapper (O/RM) for .NET with many years of feature development and stabilization.</span></span>

<span data-ttu-id="07360-104">作为 O/RM，EF6 降低了关系方面和面向对象的方面之间的阻抗不匹配，使开发人员能够使用表示应用程序域的强类型 .NET 对象来编写应用程序，该应用程序可与存储在关系数据库中的数据交互，同时使开发人员无需再编写大部分的数据访问“管道”代码。</span><span class="sxs-lookup"><span data-stu-id="07360-104">As an O/RM, EF6 reduces the impedance mismatch between the relational and object-oriented worlds, enabling developers to write applications that interact with data stored in relational databases using strongly-typed .NET objects that represent the application's domain, and eliminating the need for a large portion of the data access "plumbing" code that they usually need to write.</span></span>

<span data-ttu-id="07360-105">EF6 可实现许多热门 O/RM 功能：</span><span class="sxs-lookup"><span data-stu-id="07360-105">EF6 implements many popular O/RM features:</span></span>
- <span data-ttu-id="07360-106">不依赖于任何 EF 类型的 [POCO](xref:ef6/resources/glossary#poco) 实体类的映射</span><span class="sxs-lookup"><span data-stu-id="07360-106">Mapping of [POCO](xref:ef6/resources/glossary#poco) entity classes which do not depend on any EF types</span></span>
- <span data-ttu-id="07360-107">自动更改跟踪</span><span class="sxs-lookup"><span data-stu-id="07360-107">Automatic change tracking</span></span>
- <span data-ttu-id="07360-108">标识解析和工作单元</span><span class="sxs-lookup"><span data-stu-id="07360-108">Identity resolution and Unit of Work</span></span>
- <span data-ttu-id="07360-109">预先、延迟和显式加载</span><span class="sxs-lookup"><span data-stu-id="07360-109">Eager, lazy and explicit loading</span></span>
- <span data-ttu-id="07360-110">使用 [LINQ（语言集成查询）](https://aka.ms/AA6hsvu)转换强类型查询</span><span class="sxs-lookup"><span data-stu-id="07360-110">Translation of strongly-typed queries using [LINQ (Language INtegrated Query)](https://aka.ms/AA6hsvu)</span></span>
- <span data-ttu-id="07360-111">丰富的映射功能，可支持：</span><span class="sxs-lookup"><span data-stu-id="07360-111">Rich mapping capabilities, including support for:</span></span>
  - <span data-ttu-id="07360-112">一对一、一对多和多对多关系</span><span class="sxs-lookup"><span data-stu-id="07360-112">One-to-one, one-to-many and many-to-many relationships</span></span>
  - <span data-ttu-id="07360-113">继承（每个层次结构一张表、每个类型一张表和每个具体类一张表）</span><span class="sxs-lookup"><span data-stu-id="07360-113">Inheritance (table per hierarchy, table per type and table per concrete class)</span></span>
  - <span data-ttu-id="07360-114">复杂类型</span><span class="sxs-lookup"><span data-stu-id="07360-114">Complex types</span></span>
  - <span data-ttu-id="07360-115">存储过程</span><span class="sxs-lookup"><span data-stu-id="07360-115">Stored procedures</span></span>
- <span data-ttu-id="07360-116">通过可视化设计器创建实体模型。</span><span class="sxs-lookup"><span data-stu-id="07360-116">A visual designer to create entity models.</span></span>
- <span data-ttu-id="07360-117">通过编写代码创建实体模型的“Code First”体验。</span><span class="sxs-lookup"><span data-stu-id="07360-117">A "Code First" experience to create entity models by writing code.</span></span>
- <span data-ttu-id="07360-118">既可从现有数据库生成模型，然后手动编辑，也可从头开始创建模型，然后用于生成新的数据库。</span><span class="sxs-lookup"><span data-stu-id="07360-118">Models can either be generated from existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.</span></span>
- <span data-ttu-id="07360-119">与 .NET Framework 应用程序模型（包括 ASP.NET）集成，并通过数据绑定与 WPF 和 WinForms 集成。</span><span class="sxs-lookup"><span data-stu-id="07360-119">Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.</span></span>
- <span data-ttu-id="07360-120">基于 ADO.NET 的数据库连接和可用于连接到 SQL Server、Oracle、MySQL、SQLite、PostgreSQL、DB2 等的众多[提供程序](xref:ef6/fundamentals/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="07360-120">Database connectivity based on ADO.NET and numerous [providers](xref:ef6/fundamentals/providers/index) available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span></span>

## <a name="should-i-use-ef6-or-ef-core"></a><span data-ttu-id="07360-121">应使用 EF6 还是 EF Core？</span><span class="sxs-lookup"><span data-stu-id="07360-121">Should I use EF6 or EF Core?</span></span>

<span data-ttu-id="07360-122">EF Core 是更现代、可扩展的轻量级实体框架版本，与 EF6 的功能和优点非常相似。</span><span class="sxs-lookup"><span data-stu-id="07360-122">EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6.</span></span>
<span data-ttu-id="07360-123">EF Core 则完全进行了重写，包含许多 EF6 没有的新功能，但还是缺少 EF6 中最高级的一些映射功能。</span><span class="sxs-lookup"><span data-stu-id="07360-123">EF Core is a complete rewrite and contains many new features not available in EF6, although it also still lacks some of the most advanced mapping capabilities of EF6.</span></span>
<span data-ttu-id="07360-124">如果功能集与需求匹配，请考虑在新应用程序中使用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="07360-124">Consider using EF Core in new applications if the feature set matches your requirements.</span></span>
<span data-ttu-id="07360-125">[比较 EF Core 和 EF6](xref:efcore-and-ef6/index)中更详细地讨论了此选项。</span><span class="sxs-lookup"><span data-stu-id="07360-125">[Compare EF Core & EF6](xref:efcore-and-ef6/index) examines this choice in greater detail.</span></span>

## <a name="get-started"></a>[<span data-ttu-id="07360-126">入门</span><span class="sxs-lookup"><span data-stu-id="07360-126">Get Started</span></span>](xref:ef6/get-started)

<span data-ttu-id="07360-127">将 EntityFramework NuGet 包添加到项目或安装[适用于 Visual Studio 的 Entity Framework Tools](https://aka.ms/AA6i8c5)。</span><span class="sxs-lookup"><span data-stu-id="07360-127">Add the EntityFramework NuGet package to your project or install the [Entity Framework Tools for Visual Studio](https://aka.ms/AA6i8c5).</span></span> <span data-ttu-id="07360-128">然后观看视频、阅读教程和高级文档，以充分利用 EF6。</span><span class="sxs-lookup"><span data-stu-id="07360-128">Then watch videos, read tutorials, and advanced documentation to help you make the most of EF6.</span></span>

## <a name="past-entity-framework-versions"></a><span data-ttu-id="07360-129">过去的实体框架版本</span><span class="sxs-lookup"><span data-stu-id="07360-129">Past Entity Framework Versions</span></span>

<span data-ttu-id="07360-130">本文档针对的是最新版本的实体框架 6 ，但其中大部分内容也适用于过去的版本。</span><span class="sxs-lookup"><span data-stu-id="07360-130">This is the documentation for the latest version of Entity Framework 6, although much of it also applies to past releases.</span></span>
<span data-ttu-id="07360-131">请查看[新增功能](xref:ef6/what-is-new/index)和[过去的版本](xref:ef6/what-is-new/past-releases)，了解 EF 版本和其中引入的功能的完整列表。</span><span class="sxs-lookup"><span data-stu-id="07360-131">Check out [What's New](xref:ef6/what-is-new/index) and [Past Releases](xref:ef6/what-is-new/past-releases) for a complete list of EF releases and the features they introduced.</span></span>
