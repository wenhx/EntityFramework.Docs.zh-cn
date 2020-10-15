---
title: 从 EF6 移植到 EF Core - EF
description: 有关将应用程序从 Entity Framework 6 移植到 Entity Framework Core 的常规信息
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 0dfb4cc5f7c65aa081d0175708a0db95b0688e50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064206"
---
# <a name="porting-from-ef6-to-ef-core"></a><span data-ttu-id="24d0f-103">从 EF6 移植到 EF Core</span><span class="sxs-lookup"><span data-stu-id="24d0f-103">Porting from EF6 to EF Core</span></span>

<span data-ttu-id="24d0f-104">由于 EF Core 有一些根本性变化，我们不建议尝试将 EF6 应用程序迁移至 EF Core，除非你有令人信服的理由进行此项更改。</span><span class="sxs-lookup"><span data-stu-id="24d0f-104">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span>
<span data-ttu-id="24d0f-105">你应该将从 EF6 移至 EF Core 视作移植而不是升级。</span><span class="sxs-lookup"><span data-stu-id="24d0f-105">You should view the move from EF6 to EF Core as a port rather than an upgrade.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="24d0f-106">启动移植过程前，务必验证 EF Core 符合应用程序的数据访问要求。</span><span class="sxs-lookup"><span data-stu-id="24d0f-106">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="24d0f-107">缺少的功能</span><span class="sxs-lookup"><span data-stu-id="24d0f-107">Missing features</span></span>

<span data-ttu-id="24d0f-108">请确保 EF Core 包含需要在应用程序中使用的所有功能。</span><span class="sxs-lookup"><span data-stu-id="24d0f-108">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="24d0f-109">有关 EF Core 与 EF6 中功能集的详细比较，请参阅[功能比较](xref:efcore-and-ef6/index)。</span><span class="sxs-lookup"><span data-stu-id="24d0f-109">See [Feature Comparison](xref:efcore-and-ef6/index) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="24d0f-110">若缺少所需的任何功能，请确保在移植到 EF Core 前弥补这些功能的缺失。</span><span class="sxs-lookup"><span data-stu-id="24d0f-110">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="24d0f-111">行为更改</span><span class="sxs-lookup"><span data-stu-id="24d0f-111">Behavior changes</span></span>

<span data-ttu-id="24d0f-112">下表包含 EF6 和 EF Core 之间的一些行为变更，非完整列表。</span><span class="sxs-lookup"><span data-stu-id="24d0f-112">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="24d0f-113">移植应用程序时，务必牢记它们，因为这些可能会更改应用程序的行为方式，而在交换到 EF Core 后它们不会显示为编译错误。</span><span class="sxs-lookup"><span data-stu-id="24d0f-113">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="24d0f-114">DbSet.Add/Attach 和图形行为</span><span class="sxs-lookup"><span data-stu-id="24d0f-114">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="24d0f-115">在 EF6 中，在实体上调用 `DbSet.Add()` 将递归搜索导航属性引用的所有实体。</span><span class="sxs-lookup"><span data-stu-id="24d0f-115">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="24d0f-116">所找到的上下文未在跟踪的任何实体也会被标记为“已添加”。</span><span class="sxs-lookup"><span data-stu-id="24d0f-116">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="24d0f-117">`DbSet.Attach()` 的行为相同，但会将所有实体标记为“未更改”。</span><span class="sxs-lookup"><span data-stu-id="24d0f-117">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="24d0f-118">**EF Core 执行相似的递归搜索，但有些规则略有不同。**</span><span class="sxs-lookup"><span data-stu-id="24d0f-118">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="24d0f-119">根实体始终处于请求的状态（对于 `DbSet.Add` 为“已添加”，对于 `DbSet.Attach` 为“未更改”）。</span><span class="sxs-lookup"><span data-stu-id="24d0f-119">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="24d0f-120">**适用于递归搜索导航属性期间找到的所有实体：**</span><span class="sxs-lookup"><span data-stu-id="24d0f-120">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="24d0f-121">**若实体的主键由存储生成**</span><span class="sxs-lookup"><span data-stu-id="24d0f-121">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="24d0f-122">若主键未设置为值，状态将设置为“已添加”。</span><span class="sxs-lookup"><span data-stu-id="24d0f-122">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="24d0f-123">若为主键值的属性类型分配了 CLR 默认值（如为 `int` 分配 `0`，为 `string` 分配 `null` 等），则视为“未设置”它。</span><span class="sxs-lookup"><span data-stu-id="24d0f-123">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="24d0f-124">若主键设置为值，状态将设置为“未更改”。</span><span class="sxs-lookup"><span data-stu-id="24d0f-124">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="24d0f-125">若主键非由数据库生成，则将实体置于与根相同的状态。</span><span class="sxs-lookup"><span data-stu-id="24d0f-125">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="24d0f-126">Code First 数据库初始化</span><span class="sxs-lookup"><span data-stu-id="24d0f-126">Code First database initialization</span></span>

<span data-ttu-id="24d0f-127">**EF6 包含许多关于执行数据库连接和数据库初始化的功能。这些规则包括：**</span><span class="sxs-lookup"><span data-stu-id="24d0f-127">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="24d0f-128">若未执行任何配置，EF6 将从 SQL Express 或 LocalDb 选择数据库。</span><span class="sxs-lookup"><span data-stu-id="24d0f-128">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="24d0f-129">若应用程序 `App/Web.config` 文件包含与上下文名称相同的连接字符串，将使用此连接。</span><span class="sxs-lookup"><span data-stu-id="24d0f-129">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="24d0f-130">如果数据库不存在，则会创建一个。</span><span class="sxs-lookup"><span data-stu-id="24d0f-130">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="24d0f-131">若数据库中不存在模型中的任何表，则会将当前模型的架构添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="24d0f-131">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="24d0f-132">若启用了迁移，则会使用它们创建数据库。</span><span class="sxs-lookup"><span data-stu-id="24d0f-132">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="24d0f-133">若数据库存在，并且 EF6 此前已创建架构，则将检查架构是否与当前模型兼容。</span><span class="sxs-lookup"><span data-stu-id="24d0f-133">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="24d0f-134">若在创建架构后模型已更改，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="24d0f-134">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="24d0f-135">**EF Core 不执行任何此类功能。**</span><span class="sxs-lookup"><span data-stu-id="24d0f-135">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="24d0f-136">必须在代码中显式配置数据库连接。</span><span class="sxs-lookup"><span data-stu-id="24d0f-136">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="24d0f-137">不执行初始化。</span><span class="sxs-lookup"><span data-stu-id="24d0f-137">No initialization is performed.</span></span> <span data-ttu-id="24d0f-138">必须使用 `DbContext.Database.Migrate()` 应用迁移（或使用 `DbContext.Database.EnsureCreated()`/`EnsureDeleted()` 创建/删除数据库，而不使用迁移）。</span><span class="sxs-lookup"><span data-stu-id="24d0f-138">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="24d0f-139">Code First 表命名约定</span><span class="sxs-lookup"><span data-stu-id="24d0f-139">Code First table naming convention</span></span>

<span data-ttu-id="24d0f-140">EF6 通过复数形式服务运行实体类名，来评估实体映射到的默认表名称。</span><span class="sxs-lookup"><span data-stu-id="24d0f-140">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="24d0f-141">EF Core 使用派生的上下文上公开实体的 `DbSet` 属性的名称。</span><span class="sxs-lookup"><span data-stu-id="24d0f-141">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="24d0f-142">若实体不包含 `DbSet` 属性，则使用类名。</span><span class="sxs-lookup"><span data-stu-id="24d0f-142">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
