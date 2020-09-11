---
title: Entity Framework Core 概述 - EF Core
description: Entity Framework Core 的一般介绍概述
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619482"
---
# <a name="entity-framework-core"></a><span data-ttu-id="d3db1-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d3db1-103">Entity Framework Core</span></span>

<span data-ttu-id="d3db1-104">Entity Framework (EF) Core 是轻量化、可扩展、[开源](https://github.com/aspnet/EntityFrameworkCore)和跨平台版的常用 Entity Framework 数据访问技术。</span><span class="sxs-lookup"><span data-stu-id="d3db1-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="d3db1-105">EF Core 可用作对象关系映射程序 (O/RM)，以便于 .NET 开发人员能够使用 .NET 对象来处理数据库，这样就不必经常编写大部分数据访问代码了。</span><span class="sxs-lookup"><span data-stu-id="d3db1-105">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="d3db1-106">EF Core 支持多个数据库引擎，请参阅[数据库提供程序](xref:core/providers/index)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="d3db1-106">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="d3db1-107">模型</span><span class="sxs-lookup"><span data-stu-id="d3db1-107">The Model</span></span>

<span data-ttu-id="d3db1-108">对于 EF Core，使用模型执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="d3db1-108">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="d3db1-109">模型由实体类和表示数据库会话的上下文对象构成，可便于用户查询和保存数据。</span><span class="sxs-lookup"><span data-stu-id="d3db1-109">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="d3db1-110">有关详细信息，请参阅[创建模型](xref:core/modeling/index)。</span><span class="sxs-lookup"><span data-stu-id="d3db1-110">See [Creating a Model](xref:core/modeling/index) to learn more.</span></span>

<span data-ttu-id="d3db1-111">可以根据现有数据库生成模型，手动将模型编码为与数据库匹配，也可以使用 [EF 迁移](xref:core/managing-schemas/migrations/index)根据模型创建数据库，然后在模型随时间推移发生更改时改进它。</span><span class="sxs-lookup"><span data-stu-id="d3db1-111">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="d3db1-112">查询</span><span class="sxs-lookup"><span data-stu-id="d3db1-112">Querying</span></span>

<span data-ttu-id="d3db1-113">使用语言集成查询 (LINQ) 从数据库检索实体类的实例。</span><span class="sxs-lookup"><span data-stu-id="d3db1-113">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="d3db1-114">有关详细信息，请参阅[查询数据](xref:core/querying/index)。</span><span class="sxs-lookup"><span data-stu-id="d3db1-114">See [Querying Data](xref:core/querying/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="d3db1-115">保存数据</span><span class="sxs-lookup"><span data-stu-id="d3db1-115">Saving Data</span></span>

<span data-ttu-id="d3db1-116">使用实体类的实例在数据库中创建、删除和修改数据。</span><span class="sxs-lookup"><span data-stu-id="d3db1-116">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="d3db1-117">有关详细信息，请参阅[保存数据](xref:core/saving/index)。</span><span class="sxs-lookup"><span data-stu-id="d3db1-117">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="d3db1-118">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d3db1-118">Next steps</span></span>

<span data-ttu-id="d3db1-119">有关介绍性教程，请参阅 [Entity Framework Core 入门](xref:core/get-started/index)。</span><span class="sxs-lookup"><span data-stu-id="d3db1-119">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
