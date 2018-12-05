---
title: 数据种子设定的 EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 8f28dfea12461572ade8fbf3910ebd216dafb389
ms.sourcegitcommit: fa863883f1193d2118c2f9cee90808baa5e3e73e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52857424"
---
# <a name="data-seeding"></a><span data-ttu-id="10f24-102">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="10f24-102">Data Seeding</span></span>

<span data-ttu-id="10f24-103">数据种子设定是用一组初始的数据填充数据库的过程。</span><span class="sxs-lookup"><span data-stu-id="10f24-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="10f24-104">有几种方法可以在 EF Core 中完成此：</span><span class="sxs-lookup"><span data-stu-id="10f24-104">There are several ways this can be accomplished in EF Core:</span></span>
* <span data-ttu-id="10f24-105">模型种子数据</span><span class="sxs-lookup"><span data-stu-id="10f24-105">Model seed data</span></span>
* <span data-ttu-id="10f24-106">手动迁移自定义项</span><span class="sxs-lookup"><span data-stu-id="10f24-106">Manual migration customization</span></span>
* <span data-ttu-id="10f24-107">自定义初始化逻辑</span><span class="sxs-lookup"><span data-stu-id="10f24-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="10f24-108">模型种子数据</span><span class="sxs-lookup"><span data-stu-id="10f24-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="10f24-109">此功能是在 EF Core 2.1 的新增功能。</span><span class="sxs-lookup"><span data-stu-id="10f24-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="10f24-110">不同于在 EF6 中，EF Core 中设定数据种子会与作为模型配置的一部分的实体类型相关联。</span><span class="sxs-lookup"><span data-stu-id="10f24-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="10f24-111">然后 EF Core [迁移](xref:core/managing-schemas/migrations/index)什么插入、 更新或删除操作需要时将数据库升级到新版本的模型的应用可以自动计算。</span><span class="sxs-lookup"><span data-stu-id="10f24-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="10f24-112">确定应执行什么操作以将种子数据添加到所需的状态时，迁移只考虑模型更改。</span><span class="sxs-lookup"><span data-stu-id="10f24-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="10f24-113">因此对数据执行迁移之外的任何更改可能会丢失，或导致错误。</span><span class="sxs-lookup"><span data-stu-id="10f24-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="10f24-114">例如，这会将配置的种子数据`Blog`在`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="10f24-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="10f24-115">若要添加实体具有关系的外键值的需要指定：</span><span class="sxs-lookup"><span data-stu-id="10f24-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="10f24-116">如果实体类型具有卷影状态中的任何属性的匿名类可用于提供的值：</span><span class="sxs-lookup"><span data-stu-id="10f24-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="10f24-117">固有的实体类型可以以类似的方式进行种子设定：</span><span class="sxs-lookup"><span data-stu-id="10f24-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="10f24-118">请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)有关更多上下文。</span><span class="sxs-lookup"><span data-stu-id="10f24-118">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="10f24-119">将数据添加到模型中后,[迁移](xref:core/managing-schemas/migrations/index)应该用于将应用所做的更改。</span><span class="sxs-lookup"><span data-stu-id="10f24-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="10f24-120">如果你需要将迁移应用自动部署的一部分可以[创建一个 SQL 脚本](xref:core/managing-schemas/migrations/index#generate-sql-scripts)，可以预览之前执行。</span><span class="sxs-lookup"><span data-stu-id="10f24-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="10f24-121">或者，可以使用`context.Database.EnsureCreated()`若要创建新的数据库包含种子数据，例如对于测试数据库，或者在使用内存中提供程序或任何非关系数据库。</span><span class="sxs-lookup"><span data-stu-id="10f24-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="10f24-122">请注意，如果数据库已存在，`EnsureCreated()`既不会更新数据库中的架构和种子数据。</span><span class="sxs-lookup"><span data-stu-id="10f24-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="10f24-123">对于关系数据库不应调用`EnsureCreated()`如果你打算使用迁移。</span><span class="sxs-lookup"><span data-stu-id="10f24-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

<span data-ttu-id="10f24-124">此类型的种子数据由迁移，该脚本以更新已在数据库中的数据需要为其生成无需连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="10f24-124">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="10f24-125">这具有一些限制：</span><span class="sxs-lookup"><span data-stu-id="10f24-125">This imposes some restrictions:</span></span>
* <span data-ttu-id="10f24-126">需要指定即使它通常由数据库生成的主键值。</span><span class="sxs-lookup"><span data-stu-id="10f24-126">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="10f24-127">它将用于检测之间迁移的数据更改。</span><span class="sxs-lookup"><span data-stu-id="10f24-127">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="10f24-128">以前如果以任何方式更改主键植入的数据将被删除。</span><span class="sxs-lookup"><span data-stu-id="10f24-128">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="10f24-129">因此，此功能是最适用于具有不希望变化迁移之外，而不依赖任何其他数据库，例如 ZIP 代码中的静态数据。</span><span class="sxs-lookup"><span data-stu-id="10f24-129">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="10f24-130">如果你的方案包括以下任一建议使用自定义初始化逻辑中的最后一节所述：</span><span class="sxs-lookup"><span data-stu-id="10f24-130">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>
* <span data-ttu-id="10f24-131">临时数据以供测试</span><span class="sxs-lookup"><span data-stu-id="10f24-131">Temporary data for testing</span></span>
* <span data-ttu-id="10f24-132">取决于数据库状态的数据</span><span class="sxs-lookup"><span data-stu-id="10f24-132">Data that depends on database state</span></span>
* <span data-ttu-id="10f24-133">需要由数据库，包括备用键用作标识的实体生成的密钥值的数据</span><span class="sxs-lookup"><span data-stu-id="10f24-133">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="10f24-134">需要自定义转换的数据 (未由[值转换](xref:core/modeling/value-conversions))，如某些密码哈希</span><span class="sxs-lookup"><span data-stu-id="10f24-134">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="10f24-135">需要对外部 API 调用，例如 ASP.NET Core 标识的角色和用户创建的数据</span><span class="sxs-lookup"><span data-stu-id="10f24-135">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="10f24-136">手动迁移自定义项</span><span class="sxs-lookup"><span data-stu-id="10f24-136">Manual migration customization</span></span>

<span data-ttu-id="10f24-137">当添加迁移所做的更改与指定的数据`HasData`转换为调用`InsertData()`， `UpdateData()`，和`DeleteData()`。</span><span class="sxs-lookup"><span data-stu-id="10f24-137">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="10f24-138">解决的一些的限制的一种方法`HasData`是手动添加这些调用或[自定义操作](xref:core/managing-schemas/migrations/operations)到迁移相反。</span><span class="sxs-lookup"><span data-stu-id="10f24-138">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="10f24-139">自定义初始化逻辑</span><span class="sxs-lookup"><span data-stu-id="10f24-139">Custom initialization logic</span></span>

<span data-ttu-id="10f24-140">将使用一种简单、 功能强大的方法来执行数据种子设定[ `DbContext.SaveChanges()` ](xref:core/saving/index)逻辑开始执行之前在主应用程序。</span><span class="sxs-lookup"><span data-stu-id="10f24-140">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="10f24-141">因为当多个实例都运行，并且还需要有权修改数据库架构的应用，这可能会导致并发问题，种子设定代码不应正常的应用程序执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="10f24-141">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="10f24-142">具体取决于你的部署的约束可以以不同的方式执行初始化代码：</span><span class="sxs-lookup"><span data-stu-id="10f24-142">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>
* <span data-ttu-id="10f24-143">初始化应用程序在本地运行</span><span class="sxs-lookup"><span data-stu-id="10f24-143">Running the initialization app locally</span></span>
* <span data-ttu-id="10f24-144">正在初始化应用程序与主应用程序，调用初始化例程，然后禁用或删除初始化应用的部署。</span><span class="sxs-lookup"><span data-stu-id="10f24-144">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="10f24-145">通常可通过使用自动[发布配置文件](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="10f24-145">This can usually be automated by using [publish profiles](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
