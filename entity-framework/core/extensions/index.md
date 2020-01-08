---
title: 工具和扩展 - EF Core
author: ErikEJ
ms.date: 12/17/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: bab725afffe1fbf9f8c0abeef58579ac9dc842d2
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502077"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="8f987-102">EF Core 工具和扩展</span><span class="sxs-lookup"><span data-stu-id="8f987-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="8f987-103">这些工具和扩展为 Framework Core 2.1 及更高版本提供了附加功能。</span><span class="sxs-lookup"><span data-stu-id="8f987-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="8f987-104">扩展由各种源构建，不作为 Entity Framework Core 项目的一部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="8f987-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="8f987-105">考虑使用第三方扩展时，请务必评估其质量、授权、兼容性和支持等因素，确保其符合要求。</span><span class="sxs-lookup"><span data-stu-id="8f987-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="8f987-106">具体而言，为更早版本的 EF Core 构建的扩展可能需要更新，然后才适用于最新版本。</span><span class="sxs-lookup"><span data-stu-id="8f987-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="8f987-107">工具</span><span class="sxs-lookup"><span data-stu-id="8f987-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="8f987-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="8f987-108">LLBLGen Pro</span></span>

<span data-ttu-id="8f987-109">LLBLGen Pro 是一种实体建模解决方案，包含对 Entity Framework 和 Entity Framework Core 的支持。</span><span class="sxs-lookup"><span data-stu-id="8f987-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="8f987-110">借助它可轻松通过 Database First 或 Model First 定义实体模型并将其映射到数据库中，使你可以立即开始编写查询。</span><span class="sxs-lookup"><span data-stu-id="8f987-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="8f987-111">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-111">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-112">网站</span><span class="sxs-lookup"><span data-stu-id="8f987-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="8f987-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="8f987-113">Devart Entity Developer</span></span>

<span data-ttu-id="8f987-114">Devart Entity Developer 是一种用于 ADO.NET 实体框架、NHibernate、LinqConnect、Telerik 数据访问和 LINQ to SQL 的强大 ORM 设计器。</span><span class="sxs-lookup"><span data-stu-id="8f987-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="8f987-115">它支持 EF Core 模型的直观设计、使用“模型优先”或“数据库优先”的方式，还支持 C# 或 Visual Basic 代码生成。</span><span class="sxs-lookup"><span data-stu-id="8f987-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="8f987-116">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-116">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-117">网站</span><span class="sxs-lookup"><span data-stu-id="8f987-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="8f987-118">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="8f987-118">EF Core Power Tools</span></span>

<span data-ttu-id="8f987-119">EF Core Power Tools 是一种 Visual Studio 扩展，它在简单用户界面中公开各种 EF Core 设计时任务。</span><span class="sxs-lookup"><span data-stu-id="8f987-119">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="8f987-120">其中包括对现有数据库和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 中的 DbContext 和实体类的反向工程、对数据库迁移的管理，以及模型可视化效果。</span><span class="sxs-lookup"><span data-stu-id="8f987-120">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="8f987-121">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-121">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8f987-122">GitHub wiki</span><span class="sxs-lookup"><span data-stu-id="8f987-122">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="8f987-123">实体框架可视化编辑器</span><span class="sxs-lookup"><span data-stu-id="8f987-123">Entity Framework Visual Editor</span></span>

<span data-ttu-id="8f987-124">Entity Framework Visual Editor 是一种 Visual Studio 扩展，其中增添了 ORM 设计器用于 EF 6 和 EF Core 类的可视化设计。</span><span class="sxs-lookup"><span data-stu-id="8f987-124">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="8f987-125">代码是通过 T4 模板生成的，因此可自定义来满足任意需求。</span><span class="sxs-lookup"><span data-stu-id="8f987-125">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="8f987-126">它支持继承、单向和双向关联，支持枚举，还能用颜色标识类并添加文本块来解释潜在不可预测的设计部分。</span><span class="sxs-lookup"><span data-stu-id="8f987-126">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="8f987-127">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-127">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-128">市场</span><span class="sxs-lookup"><span data-stu-id="8f987-128">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="8f987-129">CatFactory</span><span class="sxs-lookup"><span data-stu-id="8f987-129">CatFactory</span></span>

<span data-ttu-id="8f987-130">CatFactory 是一种面向 .NET Core 的基架引擎，它可自动基于 SQL Server 数据库生成 DbContext 类、实体、映射配置和存储库类。</span><span class="sxs-lookup"><span data-stu-id="8f987-130">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="8f987-131">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-131">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-132">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-132">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="8f987-133">LoreSoft 的 Entity Framework Core 生成器</span><span class="sxs-lookup"><span data-stu-id="8f987-133">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="8f987-134">Entity Framework Core Generator (efg) 是一种 .NET Core CLI 工具，可基于现有数据库生成 EF Core 模型，其功能与 `dotnet ef dbcontext scaffold` 很相似，但它还支持通过区域替换或分析映射文件来实现安全代码的[重新生成](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="8f987-134">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="8f987-135">此工具支持生成视图模型、验证和对象映射器代码。</span><span class="sxs-lookup"><span data-stu-id="8f987-135">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="8f987-136">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-136">For EF Core: 2.</span></span>

<span data-ttu-id="8f987-137">[教程](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[文档](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="8f987-137">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="8f987-138">扩展</span><span class="sxs-lookup"><span data-stu-id="8f987-138">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="8f987-139">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="8f987-139">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="8f987-140">一个插件库，它可用于将 EF Core 执行的数据更改自动记录到历史记录表中。</span><span class="sxs-lookup"><span data-stu-id="8f987-140">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="8f987-141">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-141">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-142">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-142">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="8f987-143">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="8f987-143">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="8f987-144">一个扩展，它可将 EF Core 查询的结果存储到二级缓存中，使后续执行相同查询时无需访问数据库，而是直接从缓存中检索数据。</span><span class="sxs-lookup"><span data-stu-id="8f987-144">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span> <span data-ttu-id="8f987-145">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-145">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-146">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-146">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="geco"></a><span data-ttu-id="8f987-147">Geco</span><span class="sxs-lookup"><span data-stu-id="8f987-147">Geco</span></span>

<span data-ttu-id="8f987-148">Geco（生成器控制台）是一个基于控制台项目的简单代码生成器，它在 .NET Core 上运行并使用 C# 内插字符串来生成代码。</span><span class="sxs-lookup"><span data-stu-id="8f987-148">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="8f987-149">Geco 提供面向 EF Core 的反向模型生成器，并支持复数形式、单数形式和可编辑的模板。</span><span class="sxs-lookup"><span data-stu-id="8f987-149">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="8f987-150">它还支持种子数据脚本生成器、脚本运行器和数据库清理器。</span><span class="sxs-lookup"><span data-stu-id="8f987-150">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="8f987-151">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-151">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-152">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-152">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="8f987-153">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="8f987-153">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="8f987-154">允许结合使用 Entity Framework Core 工具链和 Handlebars 模板对基于现有数据库反向工程处理的类进行自定义。</span><span class="sxs-lookup"><span data-stu-id="8f987-154">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="8f987-155">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-155">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8f987-156">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-156">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="8f987-157">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8f987-157">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="8f987-158">NeinLinq 扩展了 Entity Framewor 等 LINQ 提供程序，让用户能够使用可转换谓词和选择器重复使用函数、重新编写查询并构建动态查询。</span><span class="sxs-lookup"><span data-stu-id="8f987-158">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="8f987-159">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-159">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8f987-160">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-160">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="8f987-161">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="8f987-161">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="8f987-162">Microsoft.EntityFrameworkCore 的一个插件，它支持存储库、工作模式单元，并支持多个具有具有所支持分布式事务的数据库。</span><span class="sxs-lookup"><span data-stu-id="8f987-162">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="8f987-163">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-163">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-164">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-164">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="8f987-165">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="8f987-165">EFCore.BulkExtensions</span></span>

<span data-ttu-id="8f987-166">用于批量操作（插入、更新和删除）的 EF Core 插件。</span><span class="sxs-lookup"><span data-stu-id="8f987-166">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="8f987-167">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-167">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8f987-168">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-168">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="8f987-169">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="8f987-169">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="8f987-170">添加设计时复数形式。</span><span class="sxs-lookup"><span data-stu-id="8f987-170">Adds design-time pluralization.</span></span> <span data-ttu-id="8f987-171">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-171">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-172">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-172">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="8f987-173">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="8f987-173">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="8f987-174">恢复 [Index] 属性（带有用于模型构建的扩展）。</span><span class="sxs-lookup"><span data-stu-id="8f987-174">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="8f987-175">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8f987-176">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-176">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="8f987-177">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="8f987-177">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="8f987-178">提供一个面向 EF Core 内存中数据库提供程序的包装器。</span><span class="sxs-lookup"><span data-stu-id="8f987-178">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="8f987-179">使其功能与关系提供程序更类似。</span><span class="sxs-lookup"><span data-stu-id="8f987-179">Makes it act more like a relational provider.</span></span> <span data-ttu-id="8f987-180">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-180">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-181">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-181">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="8f987-182">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="8f987-182">EFCore.TemporalSupport</span></span>

<span data-ttu-id="8f987-183">对时态支持的实现。</span><span class="sxs-lookup"><span data-stu-id="8f987-183">An implementation of temporal support.</span></span> <span data-ttu-id="8f987-184">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-184">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-185">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-185">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="8f987-186">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="8f987-186">EfCoreTemporalTable</span></span>

<span data-ttu-id="8f987-187">使用下列引入的扩展方法对你喜爱的数据库轻松执行时态查询：`AsTemporalAll()`、`AsTemporalAsOf(date)`、`AsTemporalFrom(startDate, endDate)`、`AsTemporalBetween(startDate, endDate)`、`AsTemporalContained(startDate, endDate)`。</span><span class="sxs-lookup"><span data-stu-id="8f987-187">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="8f987-188">对于 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="8f987-188">For EF Core: 3.</span></span>

[<span data-ttu-id="8f987-189">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-189">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="8f987-190">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="8f987-190">EFCore.TimeTraveler</span></span>

<span data-ttu-id="8f987-191">允许使用你已定义的 EF Core 代码、实体和映射对 [SQL Server 时态历史记录](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel)执行功能齐全的 Entity Framework Core 查询。</span><span class="sxs-lookup"><span data-stu-id="8f987-191">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="8f987-192">通过将代码包装到 `using (TemporalQuery.AsOf(targetDateTime)) {...}` 中按时间顺序查看。</span><span class="sxs-lookup"><span data-stu-id="8f987-192">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="8f987-193">对于 EF Core：3.</span><span class="sxs-lookup"><span data-stu-id="8f987-193">For EF Core: 3.</span></span>

[<span data-ttu-id="8f987-194">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-194">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="8f987-195">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="8f987-195">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="8f987-196">适用于 Entity Framework Core 的扩展库，使用 SQL Server 的开发人员可通过它轻松使用时态表。</span><span class="sxs-lookup"><span data-stu-id="8f987-196">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="8f987-197">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-197">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-198">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-198">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="8f987-199">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="8f987-199">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="8f987-200">高性能二级查询缓存。</span><span class="sxs-lookup"><span data-stu-id="8f987-200">A high-performance second-level query cache.</span></span> <span data-ttu-id="8f987-201">对于 EF Core：2.</span><span class="sxs-lookup"><span data-stu-id="8f987-201">For EF Core: 2.</span></span>

[<span data-ttu-id="8f987-202">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="8f987-202">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="8f987-203">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="8f987-203">Entity Framework Plus</span></span>

<span data-ttu-id="8f987-204">扩展 DbContext 的功能，例如：包括筛选器、审核、缓存、查询未来、成批删除、批量更新等。</span><span class="sxs-lookup"><span data-stu-id="8f987-204">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="8f987-205">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-205">For EF Core: 2, 3.</span></span>

<span data-ttu-id="8f987-206">[网站](https://entityframework-plus.net/)
[GitHub 存储库](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="8f987-206">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="8f987-207">实体框架扩展</span><span class="sxs-lookup"><span data-stu-id="8f987-207">Entity Framework Extensions</span></span>

<span data-ttu-id="8f987-208">通过高性能批量操作扩展 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="8f987-208">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="8f987-209">对于 EF Core：2、3。</span><span class="sxs-lookup"><span data-stu-id="8f987-209">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8f987-210">网站</span><span class="sxs-lookup"><span data-stu-id="8f987-210">Website</span></span>](https://entityframework-extensions.net/)
