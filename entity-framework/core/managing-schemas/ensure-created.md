---
title: 创建和删除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 32ac6cd043df73cd041780ec4c8805675adc5ab1
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811785"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="3b567-102">创建和删除 API</span><span class="sxs-lookup"><span data-stu-id="3b567-102">Create and Drop APIs</span></span>

<span data-ttu-id="3b567-103">EnsureCreated 和 EnsureDeleted 方法提供了用于[迁移](migrations/index.md)的轻型替代方法，用于管理数据库架构。</span><span class="sxs-lookup"><span data-stu-id="3b567-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="3b567-104">这些方法在以下情况下很有用：数据是暂时性的，可以在架构更改时删除。</span><span class="sxs-lookup"><span data-stu-id="3b567-104">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="3b567-105">例如，在原型中、在测试中，或在本地缓存中。</span><span class="sxs-lookup"><span data-stu-id="3b567-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="3b567-106">某些提供程序（尤其是非关系提供程序）不支持迁移。</span><span class="sxs-lookup"><span data-stu-id="3b567-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="3b567-107">对于这些提供程序，EnsureCreated 通常是初始化数据库架构的最简单方法。</span><span class="sxs-lookup"><span data-stu-id="3b567-107">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="3b567-108">EnsureCreated 和迁移不能很好地协同工作。</span><span class="sxs-lookup"><span data-stu-id="3b567-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="3b567-109">如果使用的是迁移，请不要使用 EnsureCreated 来初始化架构。</span><span class="sxs-lookup"><span data-stu-id="3b567-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="3b567-110">从 EnsureCreated 过渡到迁移并不是一种流畅的体验。</span><span class="sxs-lookup"><span data-stu-id="3b567-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="3b567-111">要执行此操作，最简单的方法是删除数据库，然后使用迁移重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="3b567-111">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="3b567-112">如果预计将来使用迁移，最好只开始迁移，而不是使用 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="3b567-112">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="3b567-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="3b567-113">EnsureDeleted</span></span>

<span data-ttu-id="3b567-114">EnsureDeleted 方法会删除数据库（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="3b567-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="3b567-115">如果你没有相应的权限，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3b567-115">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="3b567-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="3b567-116">EnsureCreated</span></span>

<span data-ttu-id="3b567-117">如果数据库不存在，EnsureCreated 将创建数据库并初始化数据库架构。</span><span class="sxs-lookup"><span data-stu-id="3b567-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="3b567-118">如果存在任何表（包括另一个 DbContext 类的表），则不会初始化该架构。</span><span class="sxs-lookup"><span data-stu-id="3b567-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="3b567-119">这些方法的异步版本也可用。</span><span class="sxs-lookup"><span data-stu-id="3b567-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="3b567-120">SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="3b567-120">SQL Script</span></span>

<span data-ttu-id="3b567-121">若要获取 EnsureCreated 使用的 SQL，可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="3b567-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="3b567-122">多个 DbContext 类</span><span class="sxs-lookup"><span data-stu-id="3b567-122">Multiple DbContext classes</span></span>

<span data-ttu-id="3b567-123">EnsureCreated 仅在数据库中不存在任何表时有效。</span><span class="sxs-lookup"><span data-stu-id="3b567-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="3b567-124">如果需要，您可以编写自己的检查来查看是否需要初始化架构，并使用基础 IRelationalDatabaseCreator 服务来初始化架构。</span><span class="sxs-lookup"><span data-stu-id="3b567-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
