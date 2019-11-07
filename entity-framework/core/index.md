---
title: Entity Framework Core 概述 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e6127f775d6bbbdf81debf5519388fe252fe079d
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655625"
---
# <a name="entity-framework-core"></a><span data-ttu-id="9d1c6-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9d1c6-102">Entity Framework Core</span></span>

<span data-ttu-id="9d1c6-103">Entity Framework (EF) Core 是轻量化、可扩展、[开源](https://github.com/aspnet/EntityFrameworkCore)和跨平台版的常用 Entity Framework 数据访问技术。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="9d1c6-104">EF Core 可用作对象关系映射程序 (O/RM)，以便于 .NET 开发人员能够使用 .NET 对象来处理数据库，这样就不必经常编写大部分数据访问代码了。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="9d1c6-105">EF Core 支持多个数据库引擎，请参阅[数据库提供程序](providers/index.md)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="9d1c6-106">模型</span><span class="sxs-lookup"><span data-stu-id="9d1c6-106">The Model</span></span>

<span data-ttu-id="9d1c6-107">对于 EF Core，使用模型执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="9d1c6-108">模型由实体类和表示数据库会话的上下文对象构成，可便于用户查询和保存数据。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="9d1c6-109">有关详细信息，请参阅[创建模型](modeling/index.md)。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="9d1c6-110">可以根据现有数据库生成模型，手动将模型编码为与数据库匹配，也可以使用 [EF 迁移](managing-schemas/migrations/index.md)根据模型创建数据库，然后在模型随时间推移发生更改时改进它。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="9d1c6-111">查询</span><span class="sxs-lookup"><span data-stu-id="9d1c6-111">Querying</span></span>

<span data-ttu-id="9d1c6-112">使用语言集成查询 (LINQ) 从数据库检索实体类的实例。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="9d1c6-113">有关详细信息，请参阅[查询数据](querying/index.md)。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-113">See [Querying Data](querying/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="9d1c6-114">保存数据</span><span class="sxs-lookup"><span data-stu-id="9d1c6-114">Saving Data</span></span>

<span data-ttu-id="9d1c6-115">使用实体类的实例在数据库中创建、删除和修改数据。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="9d1c6-116">有关详细信息，请参阅[保存数据](saving/index.md)。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-116">See [Saving Data](saving/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="9d1c6-117">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9d1c6-117">Next steps</span></span>

<span data-ttu-id="9d1c6-118">有关介绍性教程，请参阅 [Entity Framework Core 入门](get-started/index.md)。</span><span class="sxs-lookup"><span data-stu-id="9d1c6-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>
