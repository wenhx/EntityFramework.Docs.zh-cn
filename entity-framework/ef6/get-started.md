---
title: 开始使用 Entity Framework 6 - EF6
description: 开始使用 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
uid: ef6/get-started
ms.openlocfilehash: a03fa403bedc260def3f8110a028e972824cb756
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618204"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="df620-103">开始使用 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="df620-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="df620-104">本指南包含指向精选文章、演练和视频的链接集合，这些内容可帮助你快速入门。</span><span class="sxs-lookup"><span data-stu-id="df620-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="df620-105">基础知识</span><span class="sxs-lookup"><span data-stu-id="df620-105">Fundamentals</span></span>

* [<span data-ttu-id="df620-106">获取实体框架</span><span class="sxs-lookup"><span data-stu-id="df620-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="df620-107">此处可学习如何将 Entity Framework 添加到应用程序。如果要使用 EF 设计器，请确保在 Visual Studio 中安装它。</span><span class="sxs-lookup"><span data-stu-id="df620-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="df620-108">创建模型：Code First、EF 设计器和 EF 工作流</span><span class="sxs-lookup"><span data-stu-id="df620-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="df620-109">是否希望指定 EF 模型编写代码或绘制方框和线条？</span><span class="sxs-lookup"><span data-stu-id="df620-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="df620-110">是否要使用 EF 来将对象映射到现有数据库，或希望 EF 创建为对象量身打造的数据库？</span><span class="sxs-lookup"><span data-stu-id="df620-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="df620-111">本文介绍了使用 EF6 的两种不同方法：EF 设计器和 Code First。</span><span class="sxs-lookup"><span data-stu-id="df620-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="df620-112">请确保关注讨论内容并查看有关不同之处的视频。</span><span class="sxs-lookup"><span data-stu-id="df620-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="df620-113">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="df620-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="df620-114">DbContext 是需要学习其使用方法的第一个也是最重要的一个 EF 类型。</span><span class="sxs-lookup"><span data-stu-id="df620-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="df620-115">它可用作数据库查询的启动板，并可跟踪对对象作出的更改，以便持续存回数据库。</span><span class="sxs-lookup"><span data-stu-id="df620-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="df620-116">提出问题</span><span class="sxs-lookup"><span data-stu-id="df620-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="df620-117">了解如何获取专家的帮助，并向社区贡献自己的答案。</span><span class="sxs-lookup"><span data-stu-id="df620-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="df620-118">参与</span><span class="sxs-lookup"><span data-stu-id="df620-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="df620-119">Entity Framework 6 采用开放式开发模型。</span><span class="sxs-lookup"><span data-stu-id="df620-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="df620-120">访问我们的 GitHub 存储库，了解如何帮助改进 EF。</span><span class="sxs-lookup"><span data-stu-id="df620-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="df620-121">Code First 资源</span><span class="sxs-lookup"><span data-stu-id="df620-121">Code First resources</span></span>

  - [<span data-ttu-id="df620-122">对现有数据库工作流采用 Code First</span><span class="sxs-lookup"><span data-stu-id="df620-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="df620-123">对新的数据库工作流采用 Code First</span><span class="sxs-lookup"><span data-stu-id="df620-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="df620-124">使用 Code First 映射枚举</span><span class="sxs-lookup"><span data-stu-id="df620-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="df620-125">使用 Code First 映射空间类型</span><span class="sxs-lookup"><span data-stu-id="df620-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="df620-126">编写自定义 Code First 约定</span><span class="sxs-lookup"><span data-stu-id="df620-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="df620-127">将 Code First Fluent 配置与 Visual Basic 配合使用</span><span class="sxs-lookup"><span data-stu-id="df620-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="df620-128">Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="df620-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="df620-129">团队环境中的 Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="df620-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="df620-130">[自动 Code First 迁移](xref:ef6/modeling/code-first/migrations/automatic)（不再推荐）</span><span class="sxs-lookup"><span data-stu-id="df620-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="df620-131">EF 设计器资源</span><span class="sxs-lookup"><span data-stu-id="df620-131">EF Designer resources</span></span>
  - [<span data-ttu-id="df620-132">Database First 工作流</span><span class="sxs-lookup"><span data-stu-id="df620-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="df620-133">Model First 工作流</span><span class="sxs-lookup"><span data-stu-id="df620-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="df620-134">映射枚举</span><span class="sxs-lookup"><span data-stu-id="df620-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="df620-135">映射空间类型</span><span class="sxs-lookup"><span data-stu-id="df620-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="df620-136">每个层次结构一张表继承映射</span><span class="sxs-lookup"><span data-stu-id="df620-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="df620-137">每个类型一张表继承映射</span><span class="sxs-lookup"><span data-stu-id="df620-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="df620-138">用于更新的存储过程映射</span><span class="sxs-lookup"><span data-stu-id="df620-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="df620-139">用于查询的存储过程映射</span><span class="sxs-lookup"><span data-stu-id="df620-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="df620-140">实体拆分</span><span class="sxs-lookup"><span data-stu-id="df620-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="df620-141">表拆分</span><span class="sxs-lookup"><span data-stu-id="df620-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="df620-142">[定义查询](xref:ef6/modeling/designer/advanced/defining-query)（高级）</span><span class="sxs-lookup"><span data-stu-id="df620-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="df620-143">[表值函数](xref:ef6/modeling/designer/advanced/tvfs)（高级）</span><span class="sxs-lookup"><span data-stu-id="df620-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="df620-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="df620-144">Other resources</span></span>
  - [<span data-ttu-id="df620-145">异步查询和保存</span><span class="sxs-lookup"><span data-stu-id="df620-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="df620-146">使用 WinForms 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="df620-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="df620-147">使用 WPF 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="df620-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="df620-148">[使用自跟踪实体的断开连接的方案](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough)（不再推荐）</span><span class="sxs-lookup"><span data-stu-id="df620-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
