---
title: 管理数据库架构 - EF Core
description: 使用 Entity Framework Core 管理数据库架构的策略的概述
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619497"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="a6dfc-103">管理数据库架构</span><span class="sxs-lookup"><span data-stu-id="a6dfc-103">Managing Database Schemas</span></span>

<span data-ttu-id="a6dfc-104">EF Core 提供两种主要方法来保持 EF Core 模型和数据库架构同步。至于我们应该选用哪个方法，请确定你是希望以 EF Core 模型为准还是以数据库为准。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-104">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="a6dfc-105">如果希望以 EF Core 模型为准，请使用[迁移][1]。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-105">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="a6dfc-106">对 EF Core 模型进行更改时，此方法会以增量方式将相应架构更改应用到数据库，以使数据库保持与 EF Core 模型兼容。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-106">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="a6dfc-107">如果希望以数据库架构为准，请使用[反向工程][2]。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-107">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="a6dfc-108">使用此方法，可通过将数据库架构反向工程到 EF Core 模型来生成相应的 DbContext 和实体类型。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-108">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="a6dfc-109">[创建和删除 API][3] 也可从 EF Core 模型创建数据库架构。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-109">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="a6dfc-110">但是，它们主要适用于允许删除数据库的场景，比如测试、原型制作等。</span><span class="sxs-lookup"><span data-stu-id="a6dfc-110">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
