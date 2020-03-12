---
title: 开始使用 Entity Framework 6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
uid: ef6/get-started
ms.openlocfilehash: 74ae347af3c386639631f28ccb2ddbe9f444953a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413491"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="f99b3-102">开始使用 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="f99b3-102">Get started with Entity Framework 6</span></span>

<span data-ttu-id="f99b3-103">本指南包含指向精选文章、演练和视频的链接集合，这些内容可帮助你快速入门。</span><span class="sxs-lookup"><span data-stu-id="f99b3-103">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="f99b3-104">基础知识</span><span class="sxs-lookup"><span data-stu-id="f99b3-104">Fundamentals</span></span>

* [<span data-ttu-id="f99b3-105">获取 Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f99b3-105">Get Entity Framework</span></span>](~/ef6/fundamentals/install.md)

  <span data-ttu-id="f99b3-106">此处可学习如何将 Entity Framework 添加到应用程序。如果要使用 EF 设计器，请确保在 Visual Studio 中安装它。</span><span class="sxs-lookup"><span data-stu-id="f99b3-106">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="f99b3-107">创建模型：Code First、EF 设计器和 EF 工作流</span><span class="sxs-lookup"><span data-stu-id="f99b3-107">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](~/ef6/modeling/index.md)

  <span data-ttu-id="f99b3-108">是否希望指定 EF 模型编写代码或绘制方框和线条？</span><span class="sxs-lookup"><span data-stu-id="f99b3-108">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="f99b3-109">是否要使用 EF 来将对象映射到现有数据库，或希望 EF 创建为对象量身打造的数据库？</span><span class="sxs-lookup"><span data-stu-id="f99b3-109">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="f99b3-110">本文介绍了使用 EF6 的两种不同方法：EF 设计器和 Code First。</span><span class="sxs-lookup"><span data-stu-id="f99b3-110">Here your learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="f99b3-111">请确保关注讨论内容并查看有关不同之处的视频。</span><span class="sxs-lookup"><span data-stu-id="f99b3-111">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="f99b3-112">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="f99b3-112">Working with DbContext</span></span>](~/ef6/fundamentals/working-with-dbcontext.md)

  <span data-ttu-id="f99b3-113">DbContext 是需要学习其使用方法的第一个也是最重要的一个 EF 类型。</span><span class="sxs-lookup"><span data-stu-id="f99b3-113">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="f99b3-114">它可用作数据库查询的启动板，并可跟踪对对象作出的更改，以便持续存回数据库。</span><span class="sxs-lookup"><span data-stu-id="f99b3-114">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="f99b3-115">提出问题</span><span class="sxs-lookup"><span data-stu-id="f99b3-115">Ask a Question</span></span>](~/ef6/resources/get-help.md)

  <span data-ttu-id="f99b3-116">了解如何获取专家的帮助，并向社区贡献自己的答案。</span><span class="sxs-lookup"><span data-stu-id="f99b3-116">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="f99b3-117">参与</span><span class="sxs-lookup"><span data-stu-id="f99b3-117">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="f99b3-118">Entity Framework 6 采用开放式开发模型。</span><span class="sxs-lookup"><span data-stu-id="f99b3-118">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="f99b3-119">访问我们的 GitHub 存储库，了解如何帮助改进 EF。</span><span class="sxs-lookup"><span data-stu-id="f99b3-119">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="f99b3-120">Code First 资源</span><span class="sxs-lookup"><span data-stu-id="f99b3-120">Code First resources</span></span>

  - [<span data-ttu-id="f99b3-121">对现有数据库工作流采用 Code First</span><span class="sxs-lookup"><span data-stu-id="f99b3-121">Code First to an Existing Database Workflow</span></span>](~/ef6/modeling/code-first/workflows/existing-database.md)
  - [<span data-ttu-id="f99b3-122">对新的数据库工作流采用 Code First</span><span class="sxs-lookup"><span data-stu-id="f99b3-122">Code First to a New Database Workflow</span></span>](~/ef6/modeling/code-first/workflows/new-database.md)
  - [<span data-ttu-id="f99b3-123">使用 Code First 映射枚举</span><span class="sxs-lookup"><span data-stu-id="f99b3-123">Mapping Enums Using Code First</span></span>](~/ef6/modeling/code-first/data-types/enums.md)
  - [<span data-ttu-id="f99b3-124">使用 Code First 映射空间类型</span><span class="sxs-lookup"><span data-stu-id="f99b3-124">Mapping Spatial Types Using Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)
  - [<span data-ttu-id="f99b3-125">编写自定义 Code First 约定</span><span class="sxs-lookup"><span data-stu-id="f99b3-125">Writing Custom Code First Conventions</span></span>](~/ef6/modeling/code-first/conventions/custom.md)
  - [<span data-ttu-id="f99b3-126">将 Code First Fluent 配置与 Visual Basic 配合使用</span><span class="sxs-lookup"><span data-stu-id="f99b3-126">Using Code First Fluent Configuration with Visual Basic</span></span>](~/ef6/modeling/code-first/fluent/vb.md)
  - [<span data-ttu-id="f99b3-127">Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="f99b3-127">Code First Migrations</span></span>](~/ef6/modeling/code-first/migrations/index.md)
  - [<span data-ttu-id="f99b3-128">团队环境中的 Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="f99b3-128">Code First Migrations in Team Environments</span></span>](~/ef6/modeling/code-first/migrations/teams.md)
  - <span data-ttu-id="f99b3-129">[自动 Code First 迁移](~/ef6/modeling/code-first/migrations/automatic.md)（不再推荐）</span><span class="sxs-lookup"><span data-stu-id="f99b3-129">[Automatic Code First Migrations](~/ef6/modeling/code-first/migrations/automatic.md) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="f99b3-130">EF 设计器资源</span><span class="sxs-lookup"><span data-stu-id="f99b3-130">EF Designer resources</span></span>
  - [<span data-ttu-id="f99b3-131">Database First 工作流</span><span class="sxs-lookup"><span data-stu-id="f99b3-131">Database First Workflow</span></span>](~/ef6/modeling/designer/workflows/database-first.md)
  - [<span data-ttu-id="f99b3-132">Model First 工作流</span><span class="sxs-lookup"><span data-stu-id="f99b3-132">Model First Workflow</span></span>](~/ef6/modeling/designer/workflows/model-first.md)
  - [<span data-ttu-id="f99b3-133">映射枚举</span><span class="sxs-lookup"><span data-stu-id="f99b3-133">Mapping Enums</span></span>](~/ef6/modeling/designer/data-types/enums.md)
  - [<span data-ttu-id="f99b3-134">映射空间类型</span><span class="sxs-lookup"><span data-stu-id="f99b3-134">Mapping Spatial Types</span></span>](~/ef6/modeling/designer/data-types/spatial.md)
  - [<span data-ttu-id="f99b3-135">每个层次结构一张表继承映射</span><span class="sxs-lookup"><span data-stu-id="f99b3-135">Table-Per Hierarchy Inheritance Mapping</span></span>](~/ef6/modeling/designer/inheritance/tph.md)
  - [<span data-ttu-id="f99b3-136">每个类型一张表继承映射</span><span class="sxs-lookup"><span data-stu-id="f99b3-136">Table-Per Type Inheritance Mapping</span></span>](~/ef6/modeling/designer/inheritance/tpt.md)
  - [<span data-ttu-id="f99b3-137">用于更新的存储过程映射</span><span class="sxs-lookup"><span data-stu-id="f99b3-137">Stored Procedure Mapping for Updates</span></span>](~/ef6/modeling/designer/stored-procedures/cud.md)
  - [<span data-ttu-id="f99b3-138">用于查询的存储过程映射</span><span class="sxs-lookup"><span data-stu-id="f99b3-138">Stored Procedure Mapping for Query</span></span>](~/ef6/modeling/designer/stored-procedures/query.md)
  - [<span data-ttu-id="f99b3-139">实体拆分</span><span class="sxs-lookup"><span data-stu-id="f99b3-139">Entity Splitting</span></span>](~/ef6/modeling/designer/entity-splitting.md)
  - [<span data-ttu-id="f99b3-140">表拆分</span><span class="sxs-lookup"><span data-stu-id="f99b3-140">Table Splitting</span></span>](~/ef6/modeling/designer/table-splitting.md)
  - <span data-ttu-id="f99b3-141">[定义查询](~/ef6/modeling/designer/advanced/defining-query.md)（高级）</span><span class="sxs-lookup"><span data-stu-id="f99b3-141">[Defining Query](~/ef6/modeling/designer/advanced/defining-query.md) (Advanced)</span></span>
  - <span data-ttu-id="f99b3-142">[表值函数](~/ef6/modeling/designer/advanced/tvfs.md)（高级）</span><span class="sxs-lookup"><span data-stu-id="f99b3-142">[Table-Valued Functions](~/ef6/modeling/designer/advanced/tvfs.md) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="f99b3-143">其他资源</span><span class="sxs-lookup"><span data-stu-id="f99b3-143">Other resources</span></span>
  - [<span data-ttu-id="f99b3-144">异步查询和保存</span><span class="sxs-lookup"><span data-stu-id="f99b3-144">Async Query and Save</span></span>](~/ef6/fundamentals/async.md)
  - [<span data-ttu-id="f99b3-145">使用 WinForms 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="f99b3-145">Databinding with WinForms</span></span>](~/ef6/fundamentals/databinding/winforms.md)
  - [<span data-ttu-id="f99b3-146">使用 WPF 进行数据绑定</span><span class="sxs-lookup"><span data-stu-id="f99b3-146">Databinding with WPF</span></span>](~/ef6/fundamentals/databinding/wpf.md)
  - <span data-ttu-id="f99b3-147">[使用自跟踪实体的断开连接的方案](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough.md)（不再推荐）</span><span class="sxs-lookup"><span data-stu-id="f99b3-147">[Disconnected scenarios with Self-Tracking Entities](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough.md) (This is no longer recommended)</span></span>
