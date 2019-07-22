---
title: 工具和扩展 - EF Core
author: ErikEJ
ms.date: 01/07/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 08231cd93002a6d1b3cebe20f4f7cf57ea085af2
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306409"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="7504f-102">EF Core 工具和扩展</span><span class="sxs-lookup"><span data-stu-id="7504f-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="7504f-103">这些工具和扩展为 Framework Core 2.0 及更高版本提供了附加功能。</span><span class="sxs-lookup"><span data-stu-id="7504f-103">These tools and extensions provide additional functionality for Entity Framework Core 2.0 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="7504f-104">扩展由各种源构建，不作为 Entity Framework Core 项目的一部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="7504f-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="7504f-105">考虑使用第三方扩展时，请务必评估其质量、授权、兼容性和支持等因素，确保其符合要求。</span><span class="sxs-lookup"><span data-stu-id="7504f-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span>

## <a name="tools"></a><span data-ttu-id="7504f-106">工具</span><span class="sxs-lookup"><span data-stu-id="7504f-106">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="7504f-107">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="7504f-107">LLBLGen Pro</span></span>

<span data-ttu-id="7504f-108">LLBLGen Pro 是一种实体建模解决方案，包含对 Entity Framework 和 Entity Framework Core 的支持。</span><span class="sxs-lookup"><span data-stu-id="7504f-108">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="7504f-109">借助它可轻松通过 Database First 或 Model First 定义实体模型并将其映射到数据库中，使你可以立即开始编写查询。</span><span class="sxs-lookup"><span data-stu-id="7504f-109">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span>

[<span data-ttu-id="7504f-110">网站</span><span class="sxs-lookup"><span data-stu-id="7504f-110">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="7504f-111">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="7504f-111">Devart Entity Developer</span></span>

<span data-ttu-id="7504f-112">Devart Entity Developer 是一种用于 ADO.NET 实体框架、NHibernate、LinqConnect、Telerik 数据访问和 LINQ to SQL 的强大 ORM 设计器。</span><span class="sxs-lookup"><span data-stu-id="7504f-112">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="7504f-113">它支持 EF Core 模型的直观设计、使用“模型优先”或“数据库优先”的方式，还支持 C# 或 Visual Basic 代码生成。</span><span class="sxs-lookup"><span data-stu-id="7504f-113">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> 

[<span data-ttu-id="7504f-114">网站</span><span class="sxs-lookup"><span data-stu-id="7504f-114">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a><span data-ttu-id="7504f-115">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="7504f-115">EF Core Power Tools</span></span>

<span data-ttu-id="7504f-116">EF Core Power Tools 是一种 Visual Studio 2017 扩展，它在简单用户界面中公开各种 EF Core 设计时任务。</span><span class="sxs-lookup"><span data-stu-id="7504f-116">EF Core Power Tools is a Visual Studio 2017 extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="7504f-117">其中包括对现有数据库和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 中的 DbContext 和实体类的反向工程、对数据库迁移的管理，以及模型可视化效果。</span><span class="sxs-lookup"><span data-stu-id="7504f-117">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span>

[<span data-ttu-id="7504f-118">GitHub wiki</span><span class="sxs-lookup"><span data-stu-id="7504f-118">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="7504f-119">实体框架可视化编辑器</span><span class="sxs-lookup"><span data-stu-id="7504f-119">Entity Framework Visual Editor</span></span>

<span data-ttu-id="7504f-120">Entity Framework Visual Editor 是一种 Visual Studio 扩展，其中增添了 ORM 设计器用于 EF 6 和 EF Core 类的可视化设计。</span><span class="sxs-lookup"><span data-stu-id="7504f-120">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="7504f-121">代码是通过 T4 模板生成的，因此可自定义来满足任意需求。</span><span class="sxs-lookup"><span data-stu-id="7504f-121">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="7504f-122">它支持继承、单向和双向关联，支持枚举，还能用颜色标识类并添加文本块来解释潜在不可预测的设计部分。</span><span class="sxs-lookup"><span data-stu-id="7504f-122">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span>

[<span data-ttu-id="7504f-123">市场</span><span class="sxs-lookup"><span data-stu-id="7504f-123">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="7504f-124">CatFactory</span><span class="sxs-lookup"><span data-stu-id="7504f-124">CatFactory</span></span>

<span data-ttu-id="7504f-125">CatFactory 是一种面向 .NET Core 的基架引擎，它可自动基于 SQL Server 数据库生成 DbContext 类、实体、映射配置和存储库类。</span><span class="sxs-lookup"><span data-stu-id="7504f-125">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span>

[<span data-ttu-id="7504f-126">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-126">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="7504f-127">LoreSoft 的 Entity Framework Core 生成器</span><span class="sxs-lookup"><span data-stu-id="7504f-127">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="7504f-128">Entity Framework Core Generator (efg) 是一种 .NET Core CLI 工具，可基于现有数据库生成 EF Core 模型，其功能与 `dotnet ef dbcontext scaffold` 很相似，但它还支持通过区域替换或分析映射文件来实现安全代码的[重新生成](https://efg.loresoft.com/en/latest/regeneration/)。</span><span class="sxs-lookup"><span data-stu-id="7504f-128">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="7504f-129">此工具支持生成视图模型、验证和对象映射器代码。</span><span class="sxs-lookup"><span data-stu-id="7504f-129">This tool supports generating view models, validation, and object mapper code.</span></span> 

<span data-ttu-id="7504f-130">[教程](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[文档](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="7504f-130">[Tutorial](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="7504f-131">扩展</span><span class="sxs-lookup"><span data-stu-id="7504f-131">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="7504f-132">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="7504f-132">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="7504f-133">一个插件库，它可用于将 EF Core 执行的数据更改自动记录到历史记录表中。</span><span class="sxs-lookup"><span data-stu-id="7504f-133">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span>

[<span data-ttu-id="7504f-134">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-134">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a><span data-ttu-id="7504f-135">Microsoft.EntityFrameworkCore.DynamicLinq</span><span class="sxs-lookup"><span data-stu-id="7504f-135">Microsoft.EntityFrameworkCore.DynamicLinq</span></span>

<span data-ttu-id="7504f-136">System.Linq.Dynamic 的一个 .NET Core/.NET Standard 端口，其中包含 EF Core 异步支持。</span><span class="sxs-lookup"><span data-stu-id="7504f-136">A .NET Core / .NET Standard port of System.Linq.Dynamic that includes async support with EF Core.</span></span>
<span data-ttu-id="7504f-137">System.Linq.Dynamic 最初用作一个 Microsoft 示例，它展示了如何基于字符串表达式（而非代码）动态构造 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="7504f-137">System.Linq.Dynamic originated as a Microsoft sample that shows how to construct LINQ queries dynamically from string expressions rather than code.</span></span>

[<span data-ttu-id="7504f-138">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-138">GitHub repository</span></span>](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efsecondlevelcachecore"></a><span data-ttu-id="7504f-139">EFSecondLevelCache.Core</span><span class="sxs-lookup"><span data-stu-id="7504f-139">EFSecondLevelCache.Core</span></span>

<span data-ttu-id="7504f-140">一个扩展，它可将 EF Core 查询的结果存储到二级缓存中，使后续执行相同查询时无需访问数据库，而是直接从缓存中检索数据。</span><span class="sxs-lookup"><span data-stu-id="7504f-140">An extension that enables storing the results of EF Core queries into a second-level cache, so that subsequent executions of the same queries can avoid accessing the database and retrieve the data directly from the cache.</span></span>

[<span data-ttu-id="7504f-141">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-141">GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="entityframeworkcoreprimarykey"></a><span data-ttu-id="7504f-142">EntityFrameworkCore.PrimaryKey</span><span class="sxs-lookup"><span data-stu-id="7504f-142">EntityFrameworkCore.PrimaryKey</span></span>

<span data-ttu-id="7504f-143">借助此库，可从任何用作字典的实体中检索主键（包括复合键）的值。</span><span class="sxs-lookup"><span data-stu-id="7504f-143">This library allows retrieving the values of primary key (including composite keys) from any entity as a dictionary.</span></span>

[<span data-ttu-id="7504f-144">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-144">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcoretypedoriginalvalues"></a><span data-ttu-id="7504f-145">EntityFrameworkCore.TypedOriginalValues</span><span class="sxs-lookup"><span data-stu-id="7504f-145">EntityFrameworkCore.TypedOriginalValues</span></span>

<span data-ttu-id="7504f-146">借助此库，可对实体属性的原始值进行强类型访问。</span><span class="sxs-lookup"><span data-stu-id="7504f-146">This library enables strongly typed access to the original values of entity properties.</span></span> 

[<span data-ttu-id="7504f-147">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-147">GitHub repository</span></span>](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a><span data-ttu-id="7504f-148">Geco</span><span class="sxs-lookup"><span data-stu-id="7504f-148">Geco</span></span>

<span data-ttu-id="7504f-149">Geco（生成器控制台）是一个基于控制台项目的简单代码生成器，它在 .NET Core 上运行并使用 C# 内插字符串来生成代码。</span><span class="sxs-lookup"><span data-stu-id="7504f-149">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="7504f-150">Geco 提供面向 EF Core 的反向模型生成器，并支持复数形式、单数形式和可编辑的模板。</span><span class="sxs-lookup"><span data-stu-id="7504f-150">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="7504f-151">它还支持种子数据脚本生成器、脚本运行器和数据库清理器。</span><span class="sxs-lookup"><span data-stu-id="7504f-151">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span>

[<span data-ttu-id="7504f-152">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-152">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a><span data-ttu-id="7504f-153">LinqKit.Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7504f-153">LinqKit.Microsoft.EntityFrameworkCore</span></span>

<span data-ttu-id="7504f-154">LinqKit.Microsoft.EntityFrameworkCore 是 LINQKit 库的 EF Core 兼容版本。</span><span class="sxs-lookup"><span data-stu-id="7504f-154">LinqKit.Microsoft.EntityFrameworkCore is an EF Core-compatible version of the LINQKit library.</span></span> <span data-ttu-id="7504f-155">LINQKit 是 LINQ 的一组免费扩展，面向的是 SQL 和 Entity Framework 超级用户。</span><span class="sxs-lookup"><span data-stu-id="7504f-155">LINQKit is a free set of extensions for LINQ to SQL and Entity Framework power users.</span></span> <span data-ttu-id="7504f-156">它可实现高级功能，例如谓词表达式的动态构建，以及在子查询中使用表达式变量。</span><span class="sxs-lookup"><span data-stu-id="7504f-156">It enables advanced functionality like dynamic building of predicate expressions, and using expression variables in subqueries.</span></span>  

[<span data-ttu-id="7504f-157">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-157">GitHub repository</span></span>](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="7504f-158">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7504f-158">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="7504f-159">NeinLinq 扩展了 Entity Framewor 等 LINQ 提供程序，让用户能够使用可转换谓词和选择器重复使用函数、重新编写查询并构建动态查询。</span><span class="sxs-lookup"><span data-stu-id="7504f-159">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span>

[<span data-ttu-id="7504f-160">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-160">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="7504f-161">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="7504f-161">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="7504f-162">Microsoft.EntityFrameworkCore 的一个插件，它支持存储库、工作模式单元，并支持多个具有具有所支持分布式事务的数据库。</span><span class="sxs-lookup"><span data-stu-id="7504f-162">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span>

[<span data-ttu-id="7504f-163">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-163">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="7504f-164">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="7504f-164">EFCore.BulkExtensions</span></span>

<span data-ttu-id="7504f-165">用于批量操作（插入、更新和删除）的 EF Core 插件。</span><span class="sxs-lookup"><span data-stu-id="7504f-165">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span>

[<span data-ttu-id="7504f-166">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-166">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="7504f-167">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="7504f-167">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="7504f-168">将设计时复数形式添加到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="7504f-168">Adds design-time pluralization to EF Core.</span></span>

[<span data-ttu-id="7504f-169">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-169">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="pomelofoundationpomeloentityframeworkcoreextensionstosql"></a><span data-ttu-id="7504f-170">PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql</span><span class="sxs-lookup"><span data-stu-id="7504f-170">PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql</span></span>

<span data-ttu-id="7504f-171">一个简单扩展方法，它获取 EF Core 将在简单方案中为给定 LINQ 查询生成的 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="7504f-171">A simple extension method that obtains the SQL statement EF Core would generate for a given LINQ query in simple scenarios.</span></span> <span data-ttu-id="7504f-172">仅可在简单方案中使用 ToSql 方法，因为 EF Core 可为一个 LINQ 查询生成多个 SQL 语句，而具体的 SQL 语句由参数值而定。</span><span class="sxs-lookup"><span data-stu-id="7504f-172">The ToSql method is limited to simple scenarios because EF Core can generate more than one SQL statement for a single LINQ query, and different SQL statements depending on parameter values.</span></span>

[<span data-ttu-id="7504f-173">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-173">GitHub repository</span></span>](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="7504f-174">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="7504f-174">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="7504f-175">恢复 EF Core 的 [Index] 属性（带有用于模型构建的扩展）。</span><span class="sxs-lookup"><span data-stu-id="7504f-175">Revival of [Index] attribute for EF Core (with extension for model building).</span></span>

[<span data-ttu-id="7504f-176">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-176">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="7504f-177">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="7504f-177">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="7504f-178">提供一个面向 EF Core 内存中数据库提供程序的包装器。</span><span class="sxs-lookup"><span data-stu-id="7504f-178">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="7504f-179">使其功能与关系提供程序更类似。</span><span class="sxs-lookup"><span data-stu-id="7504f-179">Makes it act more like a relational provider.</span></span>

[<span data-ttu-id="7504f-180">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-180">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="7504f-181">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="7504f-181">EFCore.TemporalSupport</span></span>

<span data-ttu-id="7504f-182">EF Core 临时支持的一个实现。</span><span class="sxs-lookup"><span data-stu-id="7504f-182">An implementation of temporal support for EF Core.</span></span>

[<span data-ttu-id="7504f-183">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-183">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="7504f-184">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="7504f-184">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="7504f-185">EF Core 的高性能二级查询缓存。</span><span class="sxs-lookup"><span data-stu-id="7504f-185">A high-performance second-level query cache for EF Core.</span></span>

[<span data-ttu-id="7504f-186">GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7504f-186">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="7504f-187">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="7504f-187">Entity Framework Plus</span></span>

<span data-ttu-id="7504f-188">扩展 DbContext 的功能，例如：包括筛选器、审核、缓存、查询未来、成批删除、批量更新等。</span><span class="sxs-lookup"><span data-stu-id="7504f-188">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span>

<span data-ttu-id="7504f-189">[网站](https://entityframework-plus.net/)
[GitHub 存储库](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="7504f-189">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="7504f-190">实体框架扩展</span><span class="sxs-lookup"><span data-stu-id="7504f-190">Entity Framework Extensions</span></span>

<span data-ttu-id="7504f-191">通过高性能批量操作扩展 DbContext：BulkSaveChanges、BulkInsert、BulkUpdate、BulkDelete、BulkMerge 等。</span><span class="sxs-lookup"><span data-stu-id="7504f-191">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span>

[<span data-ttu-id="7504f-192">网站</span><span class="sxs-lookup"><span data-stu-id="7504f-192">Website</span></span>](https://entityframework-extensions.net/)
