---
title: 工具和扩展 - EF Core
author: ErikEJ
ms.date: 12/17/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: e3806f7161fecfe66450d3e08f97caf3d2c84cf3
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634243"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="d9bc1-102">EF Core 工具和扩展</span><span class="sxs-lookup"><span data-stu-id="d9bc1-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="d9bc1-103">这些工具和扩展为 Framework Core 2.1 及更高版本提供了附加功能。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="d9bc1-104">扩展由各种源构建，不作为 Entity Framework Core 项目的一部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="d9bc1-105">考虑使用第三方扩展时，请务必评估其质量、授权、兼容性和支持等因素，确保其符合要求。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="d9bc1-106">具体而言，为更早版本的 EF Core 构建的扩展可能需要更新，然后才适用于最新版本。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="d9bc1-107">工具</span><span class="sxs-lookup"><span data-stu-id="d9bc1-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="d9bc1-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="d9bc1-108">LLBLGen Pro</span></span>

<span data-ttu-id="d9bc1-109">LLBLGen Pro 是一种实体建模解决方案，包含对 Entity Framework 和 Entity Framework Core 的支持。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="d9bc1-110">借助它可轻松通过 Database First 或 Model First 定义实体模型并将其映射到数据库中，使你可以立即开始编写查询。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="d9bc1-111">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-111">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-112">网站</span><span class="sxs-lookup"><span data-stu-id="d9bc1-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="d9bc1-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="d9bc1-113">Devart Entity Developer</span></span>

<span data-ttu-id="d9bc1-114">Devart Entity Developer 是一种用于 ADO.NET 实体框架、NHibernate、LinqConnect、Telerik 数据访问和 LINQ to SQL 的强大 ORM 设计器。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="d9bc1-115">它支持 EF Core 模型的直观设计、使用“模型优先”或“数据库优先”的方式，还支持 C# 或 Visual Basic 代码生成。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="d9bc1-116">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-116">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-117">网站</span><span class="sxs-lookup"><span data-stu-id="d9bc1-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="d9bc1-118">用于 Entity Framework 的 nHydrate ORM</span><span class="sxs-lookup"><span data-stu-id="d9bc1-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="d9bc1-119">为 Entity Framework 创建强类型的可扩展类的 ORM。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="d9bc1-120">生成的代码为 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="d9bc1-121">二者没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-121">There is no difference.</span></span> <span data-ttu-id="d9bc1-122">这不能替代 EF 或自定义 ORM。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="d9bc1-123">它是一种视觉对象建模层，可让团队管理复杂的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="d9bc1-124">它适用于 Git 等 SCM 软件，允许多用户访问你的模型，并最大限度减少冲突。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="d9bc1-125">安装程序可跟踪模型更改并创建升级脚本。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="d9bc1-126">对于 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-126">For EF Core: 3.</span></span>

[<span data-ttu-id="d9bc1-127">Github 站点</span><span class="sxs-lookup"><span data-stu-id="d9bc1-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="d9bc1-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="d9bc1-128">EF Core Power Tools</span></span>

<span data-ttu-id="d9bc1-129">EF Core Power Tools 是一种 Visual Studio 扩展，它在简单用户界面中公开各种 EF Core 设计时任务。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="d9bc1-130">其中包括对现有数据库和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 中的 DbContext 和实体类的反向工程、对数据库迁移的管理，以及模型可视化效果。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="d9bc1-131">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="d9bc1-132">GitHub wiki</span><span class="sxs-lookup"><span data-stu-id="d9bc1-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="d9bc1-133">实体框架可视化编辑器</span><span class="sxs-lookup"><span data-stu-id="d9bc1-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="d9bc1-134">Entity Framework Visual Editor 是一种 Visual Studio 扩展，其中增添了 ORM 设计器用于 EF 6 和 EF Core 类的可视化设计。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="d9bc1-135">代码是通过 T4 模板生成的，因此可自定义来满足任意需求。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="d9bc1-136">它支持继承、单向和双向关联，支持枚举，还能用颜色标识类并添加文本块来解释潜在不可预测的设计部分。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="d9bc1-137">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-137">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-138">市场</span><span class="sxs-lookup"><span data-stu-id="d9bc1-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="d9bc1-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="d9bc1-139">CatFactory</span></span>

<span data-ttu-id="d9bc1-140">CatFactory 是一种面向 .NET Core 的基架引擎，它可自动基于 SQL Server 数据库生成 DbContext 类、实体、映射配置和存储库类。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="d9bc1-141">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-141">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-142">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="d9bc1-143">LoreSoft 的 Entity Framework Core 生成器</span><span class="sxs-lookup"><span data-stu-id="d9bc1-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="d9bc1-144">Entity Framework Core Generator (efg) 是一种 .NET Core CLI 工具，可基于现有数据库生成 EF Core 模型，其功能与 `dotnet ef dbcontext scaffold` 很相似，但它还支持通过区域替换或分析映射文件来实现安全代码的[重新生成](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="d9bc1-145">此工具支持生成视图模型、验证和对象映射器代码。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="d9bc1-146">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-146">For EF Core: 2.</span></span>

<span data-ttu-id="d9bc1-147">[教程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文档](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="d9bc1-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="d9bc1-148">扩展</span><span class="sxs-lookup"><span data-stu-id="d9bc1-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="d9bc1-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="d9bc1-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="d9bc1-150">一个插件库，它可用于将 EF Core 执行的数据更改自动记录到历史记录表中。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="d9bc1-151">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-151">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-152">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="d9bc1-153">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="d9bc1-153">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="d9bc1-154">一个扩展，它可将 EF Core 查询的结果存储到二级缓存中，使后续执行相同查询时无需访问数据库，而是直接从缓存中检索数据。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-154">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span> <span data-ttu-id="d9bc1-155">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-155">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-156">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-156">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="geco"></a><span data-ttu-id="d9bc1-157">Geco</span><span class="sxs-lookup"><span data-stu-id="d9bc1-157">Geco</span></span>

<span data-ttu-id="d9bc1-158">Geco（生成器控制台）是一个基于控制台项目的简单代码生成器，它在 .NET Core 上运行并使用 C# 内插字符串来生成代码。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-158">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="d9bc1-159">Geco 提供面向 EF Core 的反向模型生成器，并支持复数形式、单数形式和可编辑的模板。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-159">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="d9bc1-160">它还支持种子数据脚本生成器、脚本运行器和数据库清理器。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-160">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="d9bc1-161">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-161">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-162">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-162">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="d9bc1-163">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="d9bc1-163">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="d9bc1-164">允许结合使用 Entity Framework Core 工具链和 Handlebars 模板对基于现有数据库反向工程处理的类进行自定义。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-164">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="d9bc1-165">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-165">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="d9bc1-166">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-166">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="d9bc1-167">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d9bc1-167">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="d9bc1-168">NeinLinq 扩展了 Entity Framewor 等 LINQ 提供程序，让用户能够使用可转换谓词和选择器重复使用函数、重新编写查询并构建动态查询。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-168">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="d9bc1-169">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-169">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="d9bc1-170">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-170">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="d9bc1-171">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="d9bc1-171">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="d9bc1-172">Microsoft.EntityFrameworkCore 的一个插件，它支持存储库、工作模式单元，并支持多个具有具有所支持分布式事务的数据库。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-172">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="d9bc1-173">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-173">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-174">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-174">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="d9bc1-175">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="d9bc1-175">EFCore.BulkExtensions</span></span>

<span data-ttu-id="d9bc1-176">用于批量操作（插入、更新和删除）的 EF Core 插件。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-176">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="d9bc1-177">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-177">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="d9bc1-178">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-178">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="d9bc1-179">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="d9bc1-179">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="d9bc1-180">添加设计时复数形式。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-180">Adds design-time pluralization.</span></span> <span data-ttu-id="d9bc1-181">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-181">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-182">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-182">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="d9bc1-183">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="d9bc1-183">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="d9bc1-184">恢复 [Index] 属性（带有用于模型构建的扩展）。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-184">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="d9bc1-185">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-185">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="d9bc1-186">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-186">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="d9bc1-187">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="d9bc1-187">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="d9bc1-188">提供一个面向 EF Core 内存中数据库提供程序的包装器。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-188">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="d9bc1-189">使其功能与关系提供程序更类似。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-189">Makes it act more like a relational provider.</span></span> <span data-ttu-id="d9bc1-190">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-190">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-191">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-191">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="d9bc1-192">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="d9bc1-192">EFCore.TemporalSupport</span></span>

<span data-ttu-id="d9bc1-193">对时态支持的实现。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-193">An implementation of temporal support.</span></span> <span data-ttu-id="d9bc1-194">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-194">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-195">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-195">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="d9bc1-196">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="d9bc1-196">EfCoreTemporalTable</span></span>

<span data-ttu-id="d9bc1-197">使用下列引入的扩展方法对你喜爱的数据库轻松执行时态查询：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-197">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="d9bc1-198">对于 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-198">For EF Core: 3.</span></span>

[<span data-ttu-id="d9bc1-199">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-199">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="d9bc1-200">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="d9bc1-200">EFCore.TimeTraveler</span></span>

<span data-ttu-id="d9bc1-201">允许使用你已定义的 EF Core 代码、实体和映射对 [SQL Server 时态历史记录](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)执行功能齐全的 Entity Framework Core 查询。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-201">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="d9bc1-202">通过将代码包装到 `using (TemporalQuery.AsOf(targetDateTime)) {...}` 中按时间顺序查看。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-202">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="d9bc1-203">对于 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-203">For EF Core: 3.</span></span>

[<span data-ttu-id="d9bc1-204">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-204">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="d9bc1-205">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="d9bc1-205">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="d9bc1-206">适用于 Entity Framework Core 的扩展库，使用 SQL Server 的开发人员可通过它轻松使用时态表。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-206">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="d9bc1-207">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-207">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-208">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-208">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="d9bc1-209">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="d9bc1-209">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="d9bc1-210">高性能二级查询缓存。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-210">A high-performance second-level query cache.</span></span> <span data-ttu-id="d9bc1-211">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="d9bc1-211">For EF Core: 2.</span></span>

[<span data-ttu-id="d9bc1-212">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-212">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="d9bc1-213">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="d9bc1-213">Entity Framework Plus</span></span>

<span data-ttu-id="d9bc1-214">扩展 DbContext 的功能，例如：包括筛选器、审核、缓存、查询未来、成批删除、批量更新等。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-214">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="d9bc1-215">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-215">For EF Core: 2, 3.</span></span>

<span data-ttu-id="d9bc1-216">[网站](https://entityframework-plus.net/)
[GitHub 存储库](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="d9bc1-216">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="d9bc1-217">实体框架扩展</span><span class="sxs-lookup"><span data-stu-id="d9bc1-217">Entity Framework Extensions</span></span>

<span data-ttu-id="d9bc1-218">通过高性能批量操作扩展 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-218">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="d9bc1-219">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-219">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="d9bc1-220">网站</span><span class="sxs-lookup"><span data-stu-id="d9bc1-220">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="d9bc1-221">Expressionify</span><span class="sxs-lookup"><span data-stu-id="d9bc1-221">Expressionify</span></span>

<span data-ttu-id="d9bc1-222">添加了对在 linq lambda 中调用扩展方法的支持。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-222">Add support for calling extension methods in linq lambdas.</span></span> <span data-ttu-id="d9bc1-223">对于 EF Core：3.1</span><span class="sxs-lookup"><span data-stu-id="d9bc1-223">For EF Core: 3.1</span></span>

[<span data-ttu-id="d9bc1-224">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="d9bc1-224">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="d9bc1-225">XLinq</span><span class="sxs-lookup"><span data-stu-id="d9bc1-225">XLinq</span></span>

<span data-ttu-id="d9bc1-226">适用于关系数据库的语言集成查询 (LINQ) 技术。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-226">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="d9bc1-227">它允许你使用 C# 编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-227">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="d9bc1-228">对于 EF Core：3.1</span><span class="sxs-lookup"><span data-stu-id="d9bc1-228">For EF Core: 3.1</span></span>

- <span data-ttu-id="d9bc1-229">完全支持使用 C# 创建查询：可在 lambda 表达式内使用多个语句，还可使用变量、函数等。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-229">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="d9bc1-230">与 SQL 之间不存在语义缺口。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-230">No semantic gap with SQL.</span></span> <span data-ttu-id="d9bc1-231">XLinq 将 SQL 语句（如 `SELECT`、`FROM`、`WHERE`）声明为第一类 C# 方法，将熟悉的语法与 intellisense、类型安全性和重构结合起来。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-231">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="d9bc1-232">因此，SQL 成为了“又一个”本地公开其 API 的类库，可以说是“集成了语言的 SQL”  。</span><span class="sxs-lookup"><span data-stu-id="d9bc1-232">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="d9bc1-233">网站</span><span class="sxs-lookup"><span data-stu-id="d9bc1-233">Website</span></span>](http://xlinq.live/)
