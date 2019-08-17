---
title: 从 EF6 移植到 EF Core-验证要求
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3b66f3c-9d10-4974-a090-8ad093c9a53d
uid: efcore-and-ef6/porting/ensure-requirements
ms.openlocfilehash: 07caa39e8a56db71e493331ea9f0286309abc52a
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565347"
---
# <a name="before-porting-from-ef6-to-ef-core-validate-your-applications-requirements"></a><span data-ttu-id="89d38-102">从 EF6 迁移到 EF Core 之前:验证应用程序的要求</span><span class="sxs-lookup"><span data-stu-id="89d38-102">Before porting from EF6 to EF Core: Validate your Application's Requirements</span></span>

<span data-ttu-id="89d38-103">在开始迁移过程之前很重要，以验证 EF Core满足你的应用程序的数据访问要求。</span><span class="sxs-lookup"><span data-stu-id="89d38-103">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="89d38-104">缺少功能</span><span class="sxs-lookup"><span data-stu-id="89d38-104">Missing features</span></span>

<span data-ttu-id="89d38-105">请确保 EF Core具有所需应用程序中使用的所有功能。</span><span class="sxs-lookup"><span data-stu-id="89d38-105">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="89d38-106">请参阅[功能比较](../features.md)有关如何在 EF Core中设置的功能比较到 ef6 更高版本的详细比较。</span><span class="sxs-lookup"><span data-stu-id="89d38-106">See [Feature Comparison](../features.md) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="89d38-107">如果缺少任何所需的功能，请确保，你可以补偿缺少这些功能移植到 EF Core之前。</span><span class="sxs-lookup"><span data-stu-id="89d38-107">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="89d38-108">行为更改</span><span class="sxs-lookup"><span data-stu-id="89d38-108">Behavior changes</span></span>

<span data-ttu-id="89d38-109">这是从 EF6 和 EF Core之间的行为中的某些更改非详尽列表。</span><span class="sxs-lookup"><span data-stu-id="89d38-109">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="89d38-110">请务必记住这几点你端口作为你的应用程序因为它们可能更改你的应用程序的行为，但将不显示为编译错误后交换到 EF Core的方式。</span><span class="sxs-lookup"><span data-stu-id="89d38-110">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="89d38-111">DbSet 添加/附加和图形行为</span><span class="sxs-lookup"><span data-stu-id="89d38-111">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="89d38-112">在 EF6 中, `DbSet.Add()`对实体调用会导致递归搜索其导航属性中引用的所有实体。</span><span class="sxs-lookup"><span data-stu-id="89d38-112">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="89d38-113">找到并且尚未由上下文跟踪的任何实体也将标记为已添加。</span><span class="sxs-lookup"><span data-stu-id="89d38-113">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="89d38-114">`DbSet.Attach()`的行为相同, 只不过所有实体都标记为不变。</span><span class="sxs-lookup"><span data-stu-id="89d38-114">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="89d38-115">**EF Core执行类似的递归搜索，但使用一些略有不同的规则。**</span><span class="sxs-lookup"><span data-stu-id="89d38-115">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="89d38-116">根实体始终处于请求状态 (在中为添加`DbSet.Add` , 对于为`DbSet.Attach`不更改)。</span><span class="sxs-lookup"><span data-stu-id="89d38-116">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="89d38-117">**对于在递归搜索导航属性期间找到的实体:**</span><span class="sxs-lookup"><span data-stu-id="89d38-117">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="89d38-118">**如果实体的主键是已生成的存储**</span><span class="sxs-lookup"><span data-stu-id="89d38-118">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="89d38-119">如果主键未设置为值, 则将状态设置为 "已添加"。</span><span class="sxs-lookup"><span data-stu-id="89d38-119">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="89d38-120">如果将主键值分配给属性类型的 CLR `0`默认值 (例如`int` `null` `string`, 对于、、等), 则将其视为 "未设置"。</span><span class="sxs-lookup"><span data-stu-id="89d38-120">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="89d38-121">如果主键设置为值, 则状态将设置为 "未更改"。</span><span class="sxs-lookup"><span data-stu-id="89d38-121">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="89d38-122">如果主键不是数据库生成的, 则将实体置于与根相同的状态。</span><span class="sxs-lookup"><span data-stu-id="89d38-122">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="89d38-123">Code First 数据库初始化</span><span class="sxs-lookup"><span data-stu-id="89d38-123">Code First database initialization</span></span>

<span data-ttu-id="89d38-124">**EF6 在选择数据库连接和初始化数据库方面所执行的神奇的神奇。其中一些规则包括:**</span><span class="sxs-lookup"><span data-stu-id="89d38-124">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="89d38-125">如果未执行任何配置, EF6 将选择 SQL Express 或 LocalDb 上的数据库。</span><span class="sxs-lookup"><span data-stu-id="89d38-125">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="89d38-126">如果应用程序`App/Web.config`文件中包含与上下文名称相同的连接字符串, 则将使用此连接。</span><span class="sxs-lookup"><span data-stu-id="89d38-126">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="89d38-127">如果数据库不存在, 则创建它。</span><span class="sxs-lookup"><span data-stu-id="89d38-127">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="89d38-128">如果数据库中不存在来自模型的任何表, 则会将当前模型的架构添加到数据库中。</span><span class="sxs-lookup"><span data-stu-id="89d38-128">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="89d38-129">如果已启用迁移, 则会使用它们来创建数据库。</span><span class="sxs-lookup"><span data-stu-id="89d38-129">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="89d38-130">如果数据库存在并且 EF6 以前创建了架构, 则检查架构是否与当前模型兼容。</span><span class="sxs-lookup"><span data-stu-id="89d38-130">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="89d38-131">如果自创建架构以来模型发生了更改, 则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="89d38-131">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="89d38-132">**EF Core不执行任何此幻数。**</span><span class="sxs-lookup"><span data-stu-id="89d38-132">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="89d38-133">必须在代码中显式配置数据库连接。</span><span class="sxs-lookup"><span data-stu-id="89d38-133">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="89d38-134">不执行初始化。</span><span class="sxs-lookup"><span data-stu-id="89d38-134">No initialization is performed.</span></span> <span data-ttu-id="89d38-135">必须使用`DbContext.Database.Migrate()`来应用迁移 (或`DbContext.Database.EnsureCreated()`和, `EnsureDeleted()`以便在不使用迁移的情况下创建/删除数据库)。</span><span class="sxs-lookup"><span data-stu-id="89d38-135">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="89d38-136">Code First 表命名约定</span><span class="sxs-lookup"><span data-stu-id="89d38-136">Code First table naming convention</span></span>

<span data-ttu-id="89d38-137">EF6 通过复数形式服务运行实体类名称, 以计算实体映射到的默认表名称。</span><span class="sxs-lookup"><span data-stu-id="89d38-137">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="89d38-138">EF Core使用的名称的`DbSet`派生上下文公开了实体的属性。</span><span class="sxs-lookup"><span data-stu-id="89d38-138">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="89d38-139">如果该实体不具有`DbSet`属性, 则使用类名称。</span><span class="sxs-lookup"><span data-stu-id="89d38-139">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
