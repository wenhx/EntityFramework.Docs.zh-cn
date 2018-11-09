---
title: 创建和删除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/10/2017
ms.openlocfilehash: 336f6fd655603a2474a58dfef377e121d9b04c3a
ms.sourcegitcommit: a088421ecac4f5dc5213208170490181ae2f5f0f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285634"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="63895-102">创建和删除 Api</span><span class="sxs-lookup"><span data-stu-id="63895-102">Create and Drop APIs</span></span>

<span data-ttu-id="63895-103">EnsureCreated 和 EnsureDeleted 方法提供的轻量替代[迁移](migrations/index.md)用于管理数据库架构。</span><span class="sxs-lookup"><span data-stu-id="63895-103">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](migrations/index.md) for managing the database schema.</span></span> <span data-ttu-id="63895-104">暂时性和时在架构更改，可以删除数据时，这是非常有用的。</span><span class="sxs-lookup"><span data-stu-id="63895-104">This is useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="63895-105">例如在原型制作，在测试中，或于本地缓存。</span><span class="sxs-lookup"><span data-stu-id="63895-105">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="63895-106">某些提供程序 （尤其是非关系的） 不支持迁移。</span><span class="sxs-lookup"><span data-stu-id="63895-106">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="63895-107">对于这些数据，EnsureCreated 通常是初始化数据库架构的最简单方法。</span><span class="sxs-lookup"><span data-stu-id="63895-107">For these, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="63895-108">EnsureCreated 和迁移不很好地协同工作。</span><span class="sxs-lookup"><span data-stu-id="63895-108">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="63895-109">如果您使用的迁移，不使用 EnsureCreated 初始化架构。</span><span class="sxs-lookup"><span data-stu-id="63895-109">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="63895-110">EnsureCreated 从过渡迁移到不是无缝体验。</span><span class="sxs-lookup"><span data-stu-id="63895-110">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="63895-111">若要实现此目的的 simpelest 方法是删除数据库，然后重新创建该使用迁移。</span><span class="sxs-lookup"><span data-stu-id="63895-111">The simpelest way to achieve this is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="63895-112">如果你希望在将来使用迁移，最好只是开始而不是使用 EnsureCreated 的迁移。</span><span class="sxs-lookup"><span data-stu-id="63895-112">If you anticipate using Migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="63895-113">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="63895-113">EnsureDeleted</span></span>

<span data-ttu-id="63895-114">如果存在，EnsureDeleted 方法将删除数据库。</span><span class="sxs-lookup"><span data-stu-id="63895-114">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="63895-115">如果你没有相应权限，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="63895-115">If you don't have the appropiate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="63895-116">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="63895-116">EnsureCreated</span></span>

<span data-ttu-id="63895-117">如果它不存在，并且初始化数据库架构，EnsureCreated 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="63895-117">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="63895-118">如果存在任何表 （包括表的另一个 DbContext 类），该架构不会进行初始化。</span><span class="sxs-lookup"><span data-stu-id="63895-118">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="63895-119">此外提供了这些方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="63895-119">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="63895-120">SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="63895-120">SQL Script</span></span>

<span data-ttu-id="63895-121">若要使用的 EnsureCreated SQL，您可以使用 GenerateCreateScript 方法。</span><span class="sxs-lookup"><span data-stu-id="63895-121">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="63895-122">多个 DbContext 类</span><span class="sxs-lookup"><span data-stu-id="63895-122">Multiple DbContext classes</span></span>

<span data-ttu-id="63895-123">在数据库中存在的表时，仅适用于 EnsureCreated。</span><span class="sxs-lookup"><span data-stu-id="63895-123">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="63895-124">如果需要可以编写自己的检查以查看架构需要进行初始化和使用基础 IRelationalDatabaseCreator 服务初始化架构。</span><span class="sxs-lookup"><span data-stu-id="63895-124">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
