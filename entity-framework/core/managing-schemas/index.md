---
title: 管理数据库架构 - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 2da17865cb0192fb3e6e3396e4ca5f31fde9c52a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412732"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="e4b64-102">管理数据库架构</span><span class="sxs-lookup"><span data-stu-id="e4b64-102">Managing Database Schemas</span></span>

<span data-ttu-id="e4b64-103">EF Core 提供两种主要方法来保持 EF Core 模型和数据库架构同步。至于我们应该选用哪个方法，请确定你是希望以 EF Core 模型为准还是以数据库为准。</span><span class="sxs-lookup"><span data-stu-id="e4b64-103">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="e4b64-104">如果希望以 EF Core 模型为准，请使用[迁移][1]。</span><span class="sxs-lookup"><span data-stu-id="e4b64-104">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="e4b64-105">对 EF Core 模型进行更改时，此方法会以增量方式将相应架构更改应用到数据库，以使数据库保持与 EF Core 模型兼容。</span><span class="sxs-lookup"><span data-stu-id="e4b64-105">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="e4b64-106">如果希望以数据库架构为准，请使用[反向工程][2]。</span><span class="sxs-lookup"><span data-stu-id="e4b64-106">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="e4b64-107">使用此方法，可通过将数据库架构反向工程到 EF Core 模型来生成相应的 DbContext 和实体类型。</span><span class="sxs-lookup"><span data-stu-id="e4b64-107">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="e4b64-108">[创建和删除 API][3] 也可从 EF Core 模型创建数据库架构。</span><span class="sxs-lookup"><span data-stu-id="e4b64-108">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="e4b64-109">但是，它们主要适用于允许删除数据库的场景，比如测试、原型制作等。</span><span class="sxs-lookup"><span data-stu-id="e4b64-109">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
