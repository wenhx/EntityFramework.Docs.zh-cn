---
title: 数据种子设定的 EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 5c056c600f696ad1443ddb7b8c95c4b0ead06d21
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414571"
---
# <a name="data-seeding"></a><span data-ttu-id="c20a6-102">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="c20a6-102">Data Seeding</span></span>

<span data-ttu-id="c20a6-103">数据种子是用初始数据集填充数据库的过程。</span><span class="sxs-lookup"><span data-stu-id="c20a6-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="c20a6-104">可以通过多种方式在 EF Core 中完成此操作：</span><span class="sxs-lookup"><span data-stu-id="c20a6-104">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="c20a6-105">模型种子数据</span><span class="sxs-lookup"><span data-stu-id="c20a6-105">Model seed data</span></span>
* <span data-ttu-id="c20a6-106">手动迁移自定义</span><span class="sxs-lookup"><span data-stu-id="c20a6-106">Manual migration customization</span></span>
* <span data-ttu-id="c20a6-107">自定义初始化逻辑</span><span class="sxs-lookup"><span data-stu-id="c20a6-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="c20a6-108">模型种子数据</span><span class="sxs-lookup"><span data-stu-id="c20a6-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="c20a6-109">此功能是 EF Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="c20a6-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="c20a6-110">与在 EF6 中不同，在 EF Core 中，种子设定数据可以作为模型配置的一部分与实体类型相关联。</span><span class="sxs-lookup"><span data-stu-id="c20a6-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="c20a6-111">然后 EF Core[迁移](xref:core/managing-schemas/migrations/index)可以自动计算在将数据库升级到新版本的模型时需要应用的插入、更新或删除操作。</span><span class="sxs-lookup"><span data-stu-id="c20a6-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="c20a6-112">迁移仅在确定应该执行哪种操作以使种子数据进入所需状态时才考虑模型更改。</span><span class="sxs-lookup"><span data-stu-id="c20a6-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="c20a6-113">因此，在迁移外部执行的数据更改可能会丢失或导致错误。</span><span class="sxs-lookup"><span data-stu-id="c20a6-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="c20a6-114">例如，这将为 `OnModelCreating`中的 `Blog` 配置种子数据：</span><span class="sxs-lookup"><span data-stu-id="c20a6-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="c20a6-115">若要添加具有关系的实体，需要指定外键值：</span><span class="sxs-lookup"><span data-stu-id="c20a6-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="c20a6-116">如果实体类型具有隐藏状态的任何属性，则可以使用匿名类提供值：</span><span class="sxs-lookup"><span data-stu-id="c20a6-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="c20a6-117">拥有的实体类型可以采用类似的方式进行种子设定：</span><span class="sxs-lookup"><span data-stu-id="c20a6-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="c20a6-118">有关更多上下文，请参阅[完整的示例项目](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)。</span><span class="sxs-lookup"><span data-stu-id="c20a6-118">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="c20a6-119">将数据添加到模型后，应使用[迁移](xref:core/managing-schemas/migrations/index)来应用更改。</span><span class="sxs-lookup"><span data-stu-id="c20a6-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="c20a6-120">如果需要在自动部署中应用迁移，可以创建一个可在执行前预览的[SQL 脚本](xref:core/managing-schemas/migrations/index#generate-sql-scripts)。</span><span class="sxs-lookup"><span data-stu-id="c20a6-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="c20a6-121">或者，可以使用 `context.Database.EnsureCreated()` 创建包含种子数据的新数据库，例如测试数据库，或使用内存中提供程序或任何非关系数据库时。</span><span class="sxs-lookup"><span data-stu-id="c20a6-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="c20a6-122">请注意，如果数据库已存在，`EnsureCreated()` 将不会更新数据库中的架构或种子数据。</span><span class="sxs-lookup"><span data-stu-id="c20a6-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="c20a6-123">对于关系数据库，如果您计划使用迁移，则不应调用 `EnsureCreated()`。</span><span class="sxs-lookup"><span data-stu-id="c20a6-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="c20a6-124">模型种子数据的限制</span><span class="sxs-lookup"><span data-stu-id="c20a6-124">Limitations of model seed data</span></span>

<span data-ttu-id="c20a6-125">此类型的种子数据由迁移管理，需要在不连接到数据库的情况下生成用于更新数据库中已存在的数据的脚本。</span><span class="sxs-lookup"><span data-stu-id="c20a6-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="c20a6-126">这会施加一些限制：</span><span class="sxs-lookup"><span data-stu-id="c20a6-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="c20a6-127">主键值需要指定，即使它通常由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="c20a6-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="c20a6-128">它用于检测迁移间的数据更改。</span><span class="sxs-lookup"><span data-stu-id="c20a6-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="c20a6-129">如果以任何方式更改了主键，则将删除以前的种子数据。</span><span class="sxs-lookup"><span data-stu-id="c20a6-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="c20a6-130">因此，此功能最适用于不需要在迁移外更改的静态数据，并且不依赖于数据库中的任何其他内容（例如，邮政编码）。</span><span class="sxs-lookup"><span data-stu-id="c20a6-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="c20a6-131">如果你的方案包括以下任何一种情况，则建议使用上一部分中所述的自定义初始化逻辑：</span><span class="sxs-lookup"><span data-stu-id="c20a6-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="c20a6-132">用于测试的临时数据</span><span class="sxs-lookup"><span data-stu-id="c20a6-132">Temporary data for testing</span></span>
* <span data-ttu-id="c20a6-133">依赖于数据库状态的数据</span><span class="sxs-lookup"><span data-stu-id="c20a6-133">Data that depends on database state</span></span>
* <span data-ttu-id="c20a6-134">需要由数据库生成的键值的数据，包括使用替代密钥作为标识的实体</span><span class="sxs-lookup"><span data-stu-id="c20a6-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="c20a6-135">需要自定义转换的数据（不由[值转换](xref:core/modeling/value-conversions)处理），如某些密码哈希</span><span class="sxs-lookup"><span data-stu-id="c20a6-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="c20a6-136">需要调用外部 API 的数据，例如 ASP.NET Core 标识角色和用户创建</span><span class="sxs-lookup"><span data-stu-id="c20a6-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="c20a6-137">手动迁移自定义</span><span class="sxs-lookup"><span data-stu-id="c20a6-137">Manual migration customization</span></span>

<span data-ttu-id="c20a6-138">添加迁移时，对使用 `HasData` 指定的数据所做的更改将转换为对 `InsertData()`、`UpdateData()`和 `DeleteData()`的调用。</span><span class="sxs-lookup"><span data-stu-id="c20a6-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="c20a6-139">解决 `HasData` 的某些限制的一种方法是手动将这些调用或[自定义操作](xref:core/managing-schemas/migrations/operations)添加到迁移。</span><span class="sxs-lookup"><span data-stu-id="c20a6-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="c20a6-140">自定义初始化逻辑</span><span class="sxs-lookup"><span data-stu-id="c20a6-140">Custom initialization logic</span></span>

<span data-ttu-id="c20a6-141">执行数据种子设定的一种简单而有效的方法是在主应用程序逻辑开始执行之前使用[`DbContext.SaveChanges()`](xref:core/saving/index) 。</span><span class="sxs-lookup"><span data-stu-id="c20a6-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="c20a6-142">种子设定代码不应是正常应用执行的一部分，因为这可能会导致多个实例运行时出现并发性问题，并且还要求应用有权修改数据库架构。</span><span class="sxs-lookup"><span data-stu-id="c20a6-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="c20a6-143">根据部署的约束，可以通过不同的方式执行初始化代码：</span><span class="sxs-lookup"><span data-stu-id="c20a6-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="c20a6-144">在本地运行初始化应用程序</span><span class="sxs-lookup"><span data-stu-id="c20a6-144">Running the initialization app locally</span></span>
* <span data-ttu-id="c20a6-145">将初始化应用与主应用一起部署，调用初始化例程，禁用或删除初始化应用。</span><span class="sxs-lookup"><span data-stu-id="c20a6-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="c20a6-146">通常可以使用[发布配置文件](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)自动完成此配置。</span><span class="sxs-lookup"><span data-stu-id="c20a6-146">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
