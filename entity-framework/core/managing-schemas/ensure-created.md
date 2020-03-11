---
title: 创建和删除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 32ac6cd043df73cd041780ec4c8805675adc5ab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414319"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="12d85-102">创建和删除 API</span><span class="sxs-lookup"><span data-stu-id="12d85-102">Create and Drop APIs</span></span>

<span data-ttu-id="12d85-103">EnsureCreated 和 EnsureDeleted 方法提供了用于[迁移](migrations/index.md)的轻型替代方法，用于管理数据库架构。</span><span class="sxs-lookup"><span data-stu-id="12d85-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="12d85-104">这些方法在以下情况下很有用：数据是暂时性的，可以在架构更改时删除。</span><span class="sxs-lookup"><span data-stu-id="12d85-104">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="12d85-105">例如在原型制作、测试期间或用于本地缓存。</span><span class="sxs-lookup"><span data-stu-id="12d85-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="12d85-106">某些提供程序（尤其是非关系的）不支持迁移。</span><span class="sxs-lookup"><span data-stu-id="12d85-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="12d85-107">对于这些提供程序，EnsureCreated 通常是初始化数据库架构的最简单方法。</span><span class="sxs-lookup"><span data-stu-id="12d85-107">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="12d85-108">EnsureCreated 和迁移不能很好地协同工作。</span><span class="sxs-lookup"><span data-stu-id="12d85-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="12d85-109">如果使用了“迁移”，则不要使用 EnsureCreated 来初始化架构。</span><span class="sxs-lookup"><span data-stu-id="12d85-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="12d85-110">从 EnsureCreated 到“迁移”的过渡不是无缝体验。</span><span class="sxs-lookup"><span data-stu-id="12d85-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="12d85-111">要执行此操作，最简单的方法是删除数据库，然后使用迁移重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="12d85-111">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="12d85-112">如果预计将来使用迁移，最好只开始迁移，而不是使用 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="12d85-112">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="12d85-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="12d85-113">EnsureDeleted</span></span>

<span data-ttu-id="12d85-114">如果数据库存在，EnsureDeleted 方法会将其删除。</span><span class="sxs-lookup"><span data-stu-id="12d85-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="12d85-115">如果你没有相应的权限，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="12d85-115">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="12d85-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="12d85-116">EnsureCreated</span></span>

<span data-ttu-id="12d85-117">如果数据库不存在，EnsureCreated 将创建该数据库，并初始化该数据库的架构。</span><span class="sxs-lookup"><span data-stu-id="12d85-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="12d85-118">如果存在任何表 （包括另一个 DbContext 类的表），则不会初始化架构。</span><span class="sxs-lookup"><span data-stu-id="12d85-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="12d85-119">这些方法的异步版本也可用。</span><span class="sxs-lookup"><span data-stu-id="12d85-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="12d85-120">SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="12d85-120">SQL Script</span></span>

<span data-ttu-id="12d85-121">若要获取 EnsureCreated 使用的 SQL，可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="12d85-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="12d85-122">多个 DbContext 类</span><span class="sxs-lookup"><span data-stu-id="12d85-122">Multiple DbContext classes</span></span>

<span data-ttu-id="12d85-123">仅当数据库中不存在表格时，EnsureCreated 才会起作用。</span><span class="sxs-lookup"><span data-stu-id="12d85-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="12d85-124">如果有必要，也可以编写自己的检查，来查看架构是否需要进行初始化，并可通过基础 IRelationalDatabaseCreator 服务来初始化架构。</span><span class="sxs-lookup"><span data-stu-id="12d85-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
