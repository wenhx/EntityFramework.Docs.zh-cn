---
title: EF Core 3.x 中的中断性变更 - EF Core
description: Entity Framework Core 3.x 中引入的中断性变更的完整列表
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: a656f3182c57689fea076ed2c7731e37fe1c4a28
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065662"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="a4c67-103">EF Core 3.x 中包含的中断性变更</span><span class="sxs-lookup"><span data-stu-id="a4c67-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="a4c67-104">以下 API 和行为更改有可能导致现有应用程序在升级到 3.x 时中断。</span><span class="sxs-lookup"><span data-stu-id="a4c67-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="a4c67-105">我们将仅影响数据库提供程序的更改记录在[提供程序更改](xref:core/providers/provider-log)下。</span><span class="sxs-lookup"><span data-stu-id="a4c67-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="a4c67-106">摘要</span><span class="sxs-lookup"><span data-stu-id="a4c67-106">Summary</span></span>

| <span data-ttu-id="a4c67-107">**中断性变更**</span><span class="sxs-lookup"><span data-stu-id="a4c67-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="a4c67-108">**影响**</span><span class="sxs-lookup"><span data-stu-id="a4c67-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="a4c67-109">不再在客户端上计算 LINQ 查询</span><span class="sxs-lookup"><span data-stu-id="a4c67-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="a4c67-110">高</span><span class="sxs-lookup"><span data-stu-id="a4c67-110">High</span></span>       |
| [<span data-ttu-id="a4c67-111">EF Core 3.0 面向 .NET Standard 2.1，而不是 .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="a4c67-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="a4c67-112">高</span><span class="sxs-lookup"><span data-stu-id="a4c67-112">High</span></span>      |
| [<span data-ttu-id="a4c67-113">EF Core 命令行工具 dotnet ef 不再是 .NET Core SDK 的一部分</span><span class="sxs-lookup"><span data-stu-id="a4c67-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="a4c67-114">高</span><span class="sxs-lookup"><span data-stu-id="a4c67-114">High</span></span>      |
| [<span data-ttu-id="a4c67-115">DetectChanges 遵循存储生成的键值</span><span class="sxs-lookup"><span data-stu-id="a4c67-115">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="a4c67-116">高</span><span class="sxs-lookup"><span data-stu-id="a4c67-116">High</span></span>      |
| [<span data-ttu-id="a4c67-117">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重命名</span><span class="sxs-lookup"><span data-stu-id="a4c67-117">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="a4c67-118">高</span><span class="sxs-lookup"><span data-stu-id="a4c67-118">High</span></span>      |
| [<span data-ttu-id="a4c67-119">查询类型与实体类型合并</span><span class="sxs-lookup"><span data-stu-id="a4c67-119">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="a4c67-120">高</span><span class="sxs-lookup"><span data-stu-id="a4c67-120">High</span></span>      |
| [<span data-ttu-id="a4c67-121">Entity Framework Core 不再是 ASP.NET Core 共享框架的一部分</span><span class="sxs-lookup"><span data-stu-id="a4c67-121">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="a4c67-122">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-122">Medium</span></span>      |
| [<span data-ttu-id="a4c67-123">默认情况下，现在会立即发生级联删除</span><span class="sxs-lookup"><span data-stu-id="a4c67-123">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="a4c67-124">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-124">Medium</span></span>      |
| [<span data-ttu-id="a4c67-125">单个查询中现在开始预先加载相关实体</span><span class="sxs-lookup"><span data-stu-id="a4c67-125">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="a4c67-126">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-126">Medium</span></span>      |
| [<span data-ttu-id="a4c67-127">DeleteBehavior.Restrict 具有更简洁的语义</span><span class="sxs-lookup"><span data-stu-id="a4c67-127">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="a4c67-128">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-128">Medium</span></span>      |
| [<span data-ttu-id="a4c67-129">从属类型关系的配置 API 已更改</span><span class="sxs-lookup"><span data-stu-id="a4c67-129">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="a4c67-130">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-130">Medium</span></span>      |
| [<span data-ttu-id="a4c67-131">每个属性使用独立的内存中整数键生成</span><span class="sxs-lookup"><span data-stu-id="a4c67-131">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="a4c67-132">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-132">Medium</span></span>      |
| [<span data-ttu-id="a4c67-133">无跟踪查询不再执行标识解析</span><span class="sxs-lookup"><span data-stu-id="a4c67-133">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="a4c67-134">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-134">Medium</span></span>      |
| [<span data-ttu-id="a4c67-135">元数据 API 更改</span><span class="sxs-lookup"><span data-stu-id="a4c67-135">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="a4c67-136">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-136">Medium</span></span>      |
| [<span data-ttu-id="a4c67-137">特定于提供程序的元数据 API 更改</span><span class="sxs-lookup"><span data-stu-id="a4c67-137">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="a4c67-138">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-138">Medium</span></span>      |
| [<span data-ttu-id="a4c67-139">UseRowNumberForPaging 已删除</span><span class="sxs-lookup"><span data-stu-id="a4c67-139">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="a4c67-140">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-140">Medium</span></span>      |
| [<span data-ttu-id="a4c67-141">FromSql 方法在与存储过程配合使用时，无法进行组合</span><span class="sxs-lookup"><span data-stu-id="a4c67-141">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="a4c67-142">中型</span><span class="sxs-lookup"><span data-stu-id="a4c67-142">Medium</span></span>      |
| [<span data-ttu-id="a4c67-143">只能在查询根上指定 FromSql 方法</span><span class="sxs-lookup"><span data-stu-id="a4c67-143">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="a4c67-144">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-144">Low</span></span>      |
| [<span data-ttu-id="a4c67-145">~~在调试级别记录查询执行~~已还原</span><span class="sxs-lookup"><span data-stu-id="a4c67-145">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="a4c67-146">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-146">Low</span></span>      |
| [<span data-ttu-id="a4c67-147">不再在实体实例上设置临时键值</span><span class="sxs-lookup"><span data-stu-id="a4c67-147">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="a4c67-148">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-148">Low</span></span>      |
| [<span data-ttu-id="a4c67-149">与主体共享表的依赖实体现为可选项</span><span class="sxs-lookup"><span data-stu-id="a4c67-149">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="a4c67-150">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-150">Low</span></span>      |
| [<span data-ttu-id="a4c67-151">与并发标记列共享表的所有实体均必须将其映射到属性</span><span class="sxs-lookup"><span data-stu-id="a4c67-151">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="a4c67-152">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-152">Low</span></span>      |
| [<span data-ttu-id="a4c67-153">如果没有所有者，则无法使用跟踪查询来查询从属实体</span><span class="sxs-lookup"><span data-stu-id="a4c67-153">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="a4c67-154">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-154">Low</span></span>      |
| [<span data-ttu-id="a4c67-155">对于所有派生的类型而言，从未映射的类型继承的属性现在会映射到一个列中</span><span class="sxs-lookup"><span data-stu-id="a4c67-155">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="a4c67-156">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-156">Low</span></span>      |
| [<span data-ttu-id="a4c67-157">外键属性约定不再匹配与主体属性相同的名称</span><span class="sxs-lookup"><span data-stu-id="a4c67-157">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="a4c67-158">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-158">Low</span></span>      |
| [<span data-ttu-id="a4c67-159">现在，如果在 TransactionScope 完成前不再使用数据库连接，则该连接会关闭</span><span class="sxs-lookup"><span data-stu-id="a4c67-159">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="a4c67-160">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-160">Low</span></span>      |
| [<span data-ttu-id="a4c67-161">默认情况下使用支持字段</span><span class="sxs-lookup"><span data-stu-id="a4c67-161">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="a4c67-162">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-162">Low</span></span>      |
| [<span data-ttu-id="a4c67-163">如果找到多个兼容的支持字段，则引发</span><span class="sxs-lookup"><span data-stu-id="a4c67-163">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="a4c67-164">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-164">Low</span></span>      |
| [<span data-ttu-id="a4c67-165">“仅字段”属性名应与字段名匹配</span><span class="sxs-lookup"><span data-stu-id="a4c67-165">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="a4c67-166">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-166">Low</span></span>      |
| [<span data-ttu-id="a4c67-167">AddDbContext/AddDbContextPool 不再调用 AddLogging 和 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="a4c67-167">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="a4c67-168">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-168">Low</span></span>      |
| [<span data-ttu-id="a4c67-169">AddEntityFramework\* 添加具有大小限制的 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="a4c67-169">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="a4c67-170">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-170">Low</span></span>      |
| [<span data-ttu-id="a4c67-171">DbContext.Entry 现在执行本地 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="a4c67-171">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="a4c67-172">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-172">Low</span></span>      |
| [<span data-ttu-id="a4c67-173">默认情况下，字符串和字节数组键不是客户端生成的</span><span class="sxs-lookup"><span data-stu-id="a4c67-173">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="a4c67-174">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-174">Low</span></span>      |
| [<span data-ttu-id="a4c67-175">ILoggerFactory 现在是一个在一定范围内有效的服务</span><span class="sxs-lookup"><span data-stu-id="a4c67-175">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="a4c67-176">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-176">Low</span></span>      |
| [<span data-ttu-id="a4c67-177">延迟加载代理不再假定导航属性已完全加载</span><span class="sxs-lookup"><span data-stu-id="a4c67-177">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="a4c67-178">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-178">Low</span></span>      |
| [<span data-ttu-id="a4c67-179">默认情况下，现在过度创建内部服务提供程序是一个错误</span><span class="sxs-lookup"><span data-stu-id="a4c67-179">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="a4c67-180">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-180">Low</span></span>      |
| [<span data-ttu-id="a4c67-181">使用单个字符串调用 HasOne/HasMany 的新行为</span><span class="sxs-lookup"><span data-stu-id="a4c67-181">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="a4c67-182">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-182">Low</span></span>      |
| [<span data-ttu-id="a4c67-183">多个异步方法的返回类型已从 Task 更改为 ValueTask</span><span class="sxs-lookup"><span data-stu-id="a4c67-183">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="a4c67-184">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-184">Low</span></span>      |
| [<span data-ttu-id="a4c67-185">关系式：TypeMapping 注释现在只是 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="a4c67-185">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="a4c67-186">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-186">Low</span></span>      |
| [<span data-ttu-id="a4c67-187">派生类型上的 ToTable 会引发异常</span><span class="sxs-lookup"><span data-stu-id="a4c67-187">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="a4c67-188">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-188">Low</span></span>      |
| [<span data-ttu-id="a4c67-189">EF Core 不再发送 pragma 来执行 SQLite FK</span><span class="sxs-lookup"><span data-stu-id="a4c67-189">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="a4c67-190">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-190">Low</span></span>      |
| [<span data-ttu-id="a4c67-191">Microsoft.EntityFrameworkCore.Sqlite 现在依赖于 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="a4c67-191">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="a4c67-192">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-192">Low</span></span>      |
| [<span data-ttu-id="a4c67-193">GUID 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="a4c67-193">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="a4c67-194">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-194">Low</span></span>      |
| [<span data-ttu-id="a4c67-195">Char 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="a4c67-195">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="a4c67-196">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-196">Low</span></span>      |
| [<span data-ttu-id="a4c67-197">现在使用固定区域性的日历生成迁移 ID</span><span class="sxs-lookup"><span data-stu-id="a4c67-197">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="a4c67-198">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-198">Low</span></span>      |
| [<span data-ttu-id="a4c67-199">已从 IDbContextOptionsExtension 中删除扩展信息/元数据</span><span class="sxs-lookup"><span data-stu-id="a4c67-199">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="a4c67-200">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-200">Low</span></span>      |
| [<span data-ttu-id="a4c67-201">已重命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="a4c67-201">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="a4c67-202">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-202">Low</span></span>      |
| [<span data-ttu-id="a4c67-203">阐明 API 的外键约束名称</span><span class="sxs-lookup"><span data-stu-id="a4c67-203">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="a4c67-204">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-204">Low</span></span>      |
| [<span data-ttu-id="a4c67-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync 已公开</span><span class="sxs-lookup"><span data-stu-id="a4c67-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="a4c67-206">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-206">Low</span></span>      |
| [<span data-ttu-id="a4c67-207">Microsoft.EntityFrameworkCore.Design 现在是 DevelopmentDependency 包</span><span class="sxs-lookup"><span data-stu-id="a4c67-207">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="a4c67-208">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-208">Low</span></span>      |
| [<span data-ttu-id="a4c67-209">SQLitePCL.raw 已更新为版本 2.0.0</span><span class="sxs-lookup"><span data-stu-id="a4c67-209">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="a4c67-210">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-210">Low</span></span>      |
| [<span data-ttu-id="a4c67-211">NetTopologySuite 已更新为版本 2.0.0</span><span class="sxs-lookup"><span data-stu-id="a4c67-211">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="a4c67-212">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-212">Low</span></span>      |
| [<span data-ttu-id="a4c67-213">使用 Microsoft.Data.SqlClient 而不是 System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="a4c67-213">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="a4c67-214">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-214">Low</span></span>      |
| [<span data-ttu-id="a4c67-215">必须配置多个不明确的自引用关系</span><span class="sxs-lookup"><span data-stu-id="a4c67-215">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="a4c67-216">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-216">Low</span></span>      |
| [<span data-ttu-id="a4c67-217">DbFunction.Schema 为 null 或者空字符串将其配置为位于模型的默认架构中</span><span class="sxs-lookup"><span data-stu-id="a4c67-217">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="a4c67-218">低</span><span class="sxs-lookup"><span data-stu-id="a4c67-218">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="a4c67-219">不再在客户端上计算 LINQ 查询</span><span class="sxs-lookup"><span data-stu-id="a4c67-219">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="a4c67-220">[跟踪问题 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另请参阅问题 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="a4c67-220">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="a4c67-221">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-221">**Old behavior**</span></span>

<span data-ttu-id="a4c67-222">在 3.0 之前，当 EF Core 无法将查询中的表达式转换为 SQL 或参数时，它会在客户端上自动计算表达式的值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-222">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="a4c67-223">默认情况下，客户端对潜在的昂贵表达式的计算仅触发警告。</span><span class="sxs-lookup"><span data-stu-id="a4c67-223">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="a4c67-224">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-224">**New behavior**</span></span>

<span data-ttu-id="a4c67-225">从 3.0 开始，EF Core 仅允许在客户端上计算顶级投影中的表达式（查询中的最后一个 `Select()` 调用）。</span><span class="sxs-lookup"><span data-stu-id="a4c67-225">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="a4c67-226">当查询的任何其他部分中的表达式无法转换为 SQL 或参数时，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-226">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="a4c67-227">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-227">**Why**</span></span>

<span data-ttu-id="a4c67-228">自动的客户端查询计算允许执行许多查询，即使它们的重要组成部分无法转换。</span><span class="sxs-lookup"><span data-stu-id="a4c67-228">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="a4c67-229">此行为可能导致意外且具有潜在破坏性的行为，这些行为可能仅在生产中变得明显。</span><span class="sxs-lookup"><span data-stu-id="a4c67-229">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="a4c67-230">例如，`Where()` 调用中无法转换的条件可能导致表中的所有行从数据库服务器传输且筛选器应用于客户端。</span><span class="sxs-lookup"><span data-stu-id="a4c67-230">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="a4c67-231">如果在开发中表中只包含几行，则不容易检测到这种情况，但是当应用程序转入生产环节时，由于表中可能包含数百万行，这种情况会非常严重。</span><span class="sxs-lookup"><span data-stu-id="a4c67-231">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="a4c67-232">在开发过程中，客户端求值警告也很容易被忽视。</span><span class="sxs-lookup"><span data-stu-id="a4c67-232">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="a4c67-233">除此之外，自动客户端计算可能会导致问题，其中改进特定表达式的查询转换会导致版本之间发生意外中断性变更。</span><span class="sxs-lookup"><span data-stu-id="a4c67-233">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="a4c67-234">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-234">**Mitigations**</span></span>

<span data-ttu-id="a4c67-235">如果无法完全转换查询，则以可转换的形式重写查询，或使用 `AsEnumerable()`、`ToList()` 或类似信息将数据显式返回客户端，然后可以进一步使用 LINQ 到对象处理。</span><span class="sxs-lookup"><span data-stu-id="a4c67-235">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="a4c67-236">EF Core 3.0 面向 .NET Standard 2.1，而不是 .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="a4c67-236">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="a4c67-237">跟踪问题 #15498</span><span class="sxs-lookup"><span data-stu-id="a4c67-237">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT]
> <span data-ttu-id="a4c67-238">EF Core 3.1 也面向 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="a4c67-238">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="a4c67-239">它重新支持 NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a4c67-239">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="a4c67-240">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-240">**Old behavior**</span></span>

<span data-ttu-id="a4c67-241">在 3.0 之前，EF Core 面向 .NET Standard 2.0，并在支持 .NET Standard 2.0 的所有平台上运行，包括 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a4c67-241">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="a4c67-242">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-242">**New behavior**</span></span>

<span data-ttu-id="a4c67-243">从 3.0 开始，EF Core 面向 .NET Standard 2.1，并且在支持 .NET Standard 2.1 的所有平台上运行。</span><span class="sxs-lookup"><span data-stu-id="a4c67-243">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="a4c67-244">这不包括 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a4c67-244">This does not include .NET Framework.</span></span>

<span data-ttu-id="a4c67-245">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-245">**Why**</span></span>

<span data-ttu-id="a4c67-246">这是 .NET 技术中战略决策的一部分，旨在将重点放在 .NET Core 和其他新式 .NET 平台，例如 Xamarin。</span><span class="sxs-lookup"><span data-stu-id="a4c67-246">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="a4c67-247">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-247">**Mitigations**</span></span>

<span data-ttu-id="a4c67-248">使用 EF Core 3.1。</span><span class="sxs-lookup"><span data-stu-id="a4c67-248">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="a4c67-249">Entity Framework Core 不再是 ASP.NET Core 共享框架的一部分</span><span class="sxs-lookup"><span data-stu-id="a4c67-249">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="a4c67-250">跟踪问题公告#325</span><span class="sxs-lookup"><span data-stu-id="a4c67-250">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="a4c67-251">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-251">**Old behavior**</span></span>

<span data-ttu-id="a4c67-252">在 ASP.NET Core 3.0 之前，当向 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 添加包引用时，它将包括 EF Core 和一些 EF Core 数据提供程序（如 SQL Server 提供程序）。</span><span class="sxs-lookup"><span data-stu-id="a4c67-252">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="a4c67-253">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-253">**New behavior**</span></span>

<span data-ttu-id="a4c67-254">从 3.0 开始，ASP.NET Core 共享框架不包括 EF Core 或任何 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-254">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="a4c67-255">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-255">**Why**</span></span>

<span data-ttu-id="a4c67-256">在此更改之前，获取 EF Core 需要不同的步骤，具体取决于应用程序是否是面向 ASP.NET Core 和 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="a4c67-256">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="a4c67-257">此外，升级 ASP.NET Core 会强制升级 EF Core 和 SQL Server 提供程序，这并不总是可取的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-257">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="a4c67-258">通过此更改，通过所有提供程序、支持的 .NET 实现和应用程序类型获取 EF Core 的体验都是一致的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-258">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="a4c67-259">开发人员现在还可以准确控制何时升级 EF Core 和 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-259">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="a4c67-260">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-260">**Mitigations**</span></span>

<span data-ttu-id="a4c67-261">若要在 ASP.NET Core 3.0 应用程序或任何其他受支持的应用程序中使用 EF Core，请显式添加对应用程序将使用的 EF Core 数据库提供程序的包引用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-261">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="a4c67-262">EF Core 命令行工具 dotnet ef 不再是 .NET Core SDK 的一部分</span><span class="sxs-lookup"><span data-stu-id="a4c67-262">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="a4c67-263">跟踪问题 #14016</span><span class="sxs-lookup"><span data-stu-id="a4c67-263">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="a4c67-264">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-264">**Old behavior**</span></span>

<span data-ttu-id="a4c67-265">.NET Core SDK 3.0 以前的版本包含 `dotnet ef` 工具，可以随时从任何项目的命令行使用，无需额外的步骤。</span><span class="sxs-lookup"><span data-stu-id="a4c67-265">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

<span data-ttu-id="a4c67-266">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-266">**New behavior**</span></span>

<span data-ttu-id="a4c67-267">从 3.0 版开始，.NET SDK 不再包含 `dotnet ef` 工具，因此，在使用它之前，必须将其明确安装为本地或全局工具。</span><span class="sxs-lookup"><span data-stu-id="a4c67-267">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

<span data-ttu-id="a4c67-268">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-268">**Why**</span></span>

<span data-ttu-id="a4c67-269">此更改允许我们在 NuGet 上将 `dotnet ef` 作为常规 .NET CLI 工具分发和更新，这与 EF Core 3.0 也始终作为 NuGet 包分发的事实一致。</span><span class="sxs-lookup"><span data-stu-id="a4c67-269">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="a4c67-270">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-270">**Mitigations**</span></span>

<span data-ttu-id="a4c67-271">为了能够管理迁移或构架 `DbContext`，请安装 `dotnet-ef` 作为全局工具：</span><span class="sxs-lookup"><span data-stu-id="a4c67-271">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="a4c67-272">使用[工具清单文件](/dotnet/core/tools/global-tools#install-a-local-tool)恢复声明为工具依赖项的项目依赖项时，还可以将其作为本地工具获取。</span><span class="sxs-lookup"><span data-stu-id="a4c67-272">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="a4c67-273">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重命名</span><span class="sxs-lookup"><span data-stu-id="a4c67-273">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="a4c67-274">跟踪问题 #10996</span><span class="sxs-lookup"><span data-stu-id="a4c67-274">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="a4c67-275">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-275">**Old behavior**</span></span>

<span data-ttu-id="a4c67-276">在 EF Core 3.0 之前，这些方法名称是重载的，它们使用普通字符串或应内插到 SQL 和参数中的字符串。</span><span class="sxs-lookup"><span data-stu-id="a4c67-276">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="a4c67-277">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-277">**New behavior**</span></span>

<span data-ttu-id="a4c67-278">自 EF Core 3.0 起，可使用 `FromSqlRaw`、`ExecuteSqlRaw` 和 `ExecuteSqlRawAsync` 创建一个参数化的查询，其中参数是从查询字符串中单独传递的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-278">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="a4c67-279">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-279">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="a4c67-280">使用 `FromSqlInterpolated`、`ExecuteSqlInterpolated` 和 `ExecuteSqlInterpolatedAsync` 创建一个参数化的查询，其中参数作为内插查询字符串的一部分进行传递。</span><span class="sxs-lookup"><span data-stu-id="a4c67-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="a4c67-281">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-281">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="a4c67-282">请注意，上述两个查询都将生成 SQL 参数相同的同一参数化的 SQL。</span><span class="sxs-lookup"><span data-stu-id="a4c67-282">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="a4c67-283">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-283">**Why**</span></span>

<span data-ttu-id="a4c67-284">此类方法重载使得在意图调用内插字符串方法时很容易意外调用原始字符串方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="a4c67-284">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="a4c67-285">这会导致查询中的本该参数化的结果没有参数化。</span><span class="sxs-lookup"><span data-stu-id="a4c67-285">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="a4c67-286">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-286">**Mitigations**</span></span>

<span data-ttu-id="a4c67-287">切换到使用新的方法名称。</span><span class="sxs-lookup"><span data-stu-id="a4c67-287">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="a4c67-288">FromSql 方法在与存储过程配合使用时，无法进行组合</span><span class="sxs-lookup"><span data-stu-id="a4c67-288">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="a4c67-289">跟踪问题 #15392</span><span class="sxs-lookup"><span data-stu-id="a4c67-289">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="a4c67-290">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-290">**Old behavior**</span></span>

<span data-ttu-id="a4c67-291">在 EF Core 3.0 之前，FromSql 方法已尝试检测是否可对传入的 SQL 进行组合。</span><span class="sxs-lookup"><span data-stu-id="a4c67-291">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="a4c67-292">当 SQL 像存储过程那样不可组合时，该方法进行客户端评估。</span><span class="sxs-lookup"><span data-stu-id="a4c67-292">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="a4c67-293">以下查询在服务器上运行存储过程并在客户端执行 FirstOrDefault。</span><span class="sxs-lookup"><span data-stu-id="a4c67-293">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="a4c67-294">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-294">**New behavior**</span></span>

<span data-ttu-id="a4c67-295">从 EF Core 3.0 开始，EF Core 将不再尝试分析 SQL。</span><span class="sxs-lookup"><span data-stu-id="a4c67-295">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="a4c67-296">因此，如果在 FromSqlRaw/FromSqlInterpolated 之后组合，则 EF Core 会通过引发子查询来组合 SQL。</span><span class="sxs-lookup"><span data-stu-id="a4c67-296">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="a4c67-297">因此，如果将存储过程用于组合，则出现无效 SQL 语法的异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-297">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="a4c67-298">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-298">**Why**</span></span>

<span data-ttu-id="a4c67-299">EF Core 3.0 不支持自动客户端评估，因为容易出错，如[此处](#linq-queries-are-no-longer-evaluated-on-the-client)所述。</span><span class="sxs-lookup"><span data-stu-id="a4c67-299">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="a4c67-300">**缓解操作**</span><span class="sxs-lookup"><span data-stu-id="a4c67-300">**Mitigation**</span></span>

<span data-ttu-id="a4c67-301">如果在 FromSqlRaw/FromSqlInterpolated 中使用存储过程，你了解无法对其进行组合，因此可以紧随 FromSql 方法调用添加 AsEnumerable/AsAsyncEnumerable，以避免在服务器端上进行任何组合。</span><span class="sxs-lookup"><span data-stu-id="a4c67-301">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="a4c67-302">只能在查询根上指定 FromSql 方法</span><span class="sxs-lookup"><span data-stu-id="a4c67-302">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="a4c67-303">跟踪问题 #15704</span><span class="sxs-lookup"><span data-stu-id="a4c67-303">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="a4c67-304">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-304">**Old behavior**</span></span>

<span data-ttu-id="a4c67-305">在 EF Core 3.0 之前，可以在查询中的任意位置指定 `FromSql` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-305">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="a4c67-306">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-306">**New behavior**</span></span>

<span data-ttu-id="a4c67-307">从 EF Core 3.0 开始，只能在查询根上（即，直接在 `DbSet<>` 上）指定新的 `FromSqlRaw` 和 `FromSqlInterpolated` 方法（替换 `FromSql`）。</span><span class="sxs-lookup"><span data-stu-id="a4c67-307">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="a4c67-308">尝试在其他任何位置指定这些方法将导致编译错误。</span><span class="sxs-lookup"><span data-stu-id="a4c67-308">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="a4c67-309">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-309">**Why**</span></span>

<span data-ttu-id="a4c67-310">在除 `DbSet` 之外的任意位置指定 `FromSql` 没有附加含义或附加值，并且可能在某些情况下存在多义性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-310">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="a4c67-311">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-311">**Mitigations**</span></span>

<span data-ttu-id="a4c67-312">应移动 `FromSql` 调用以使其直接位于它们所应用的 `DbSet` 上。</span><span class="sxs-lookup"><span data-stu-id="a4c67-312">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="a4c67-313">无跟踪查询不再执行标识解析</span><span class="sxs-lookup"><span data-stu-id="a4c67-313">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="a4c67-314">跟踪问题 #13518</span><span class="sxs-lookup"><span data-stu-id="a4c67-314">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="a4c67-315">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-315">**Old behavior**</span></span>

<span data-ttu-id="a4c67-316">在 EF Core 3.0 之前，将对具有给定类型和 ID 的实体的每个匹配项使用同一个实体实例。</span><span class="sxs-lookup"><span data-stu-id="a4c67-316">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="a4c67-317">这与跟踪查询的行为匹配。</span><span class="sxs-lookup"><span data-stu-id="a4c67-317">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="a4c67-318">例如下面的查询：</span><span class="sxs-lookup"><span data-stu-id="a4c67-318">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="a4c67-319">会为与给定类别关联的每个 `Product` 返回相同的 `Category` 实例。</span><span class="sxs-lookup"><span data-stu-id="a4c67-319">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="a4c67-320">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-320">**New behavior**</span></span>

<span data-ttu-id="a4c67-321">从 EF Core 3.0 开始，当在返回的图中的不同位置遇到具有给定类型和 ID 的实体时，将创建不同的实体实例。</span><span class="sxs-lookup"><span data-stu-id="a4c67-321">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="a4c67-322">例如，上面的查询现在将为每个 `Product` 返回新的 `Category` 实例，即使两个产品与同一类别关联。</span><span class="sxs-lookup"><span data-stu-id="a4c67-322">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="a4c67-323">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-323">**Why**</span></span>

<span data-ttu-id="a4c67-324">标识解析（即确定实体与先前遇到的实体具有相同的类型和 ID）会增加额外的性能和内存开销。</span><span class="sxs-lookup"><span data-stu-id="a4c67-324">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="a4c67-325">这通常会运行计数器，原因是最初使用无跟踪查询。</span><span class="sxs-lookup"><span data-stu-id="a4c67-325">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="a4c67-326">此外，尽管标识解析有时会很有用，但如果要对实体进行序列化并将其发送到客户端（这对于无跟踪查询很常见），则不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="a4c67-326">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="a4c67-327">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-327">**Mitigations**</span></span>

<span data-ttu-id="a4c67-328">如果需要标识解析，请使用跟踪查询。</span><span class="sxs-lookup"><span data-stu-id="a4c67-328">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="a4c67-329">~~在调试级别记录查询执行~~已还原</span><span class="sxs-lookup"><span data-stu-id="a4c67-329">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="a4c67-330">跟踪问题 #14523</span><span class="sxs-lookup"><span data-stu-id="a4c67-330">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="a4c67-331">我们之所以还原此更改是因为，EF Core 3.0 中的新配置允许应用程序指定任何事件的日志级别。</span><span class="sxs-lookup"><span data-stu-id="a4c67-331">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="a4c67-332">例如，若要将 SQL 日志记录切换为 `Debug`，请在 `OnConfiguring` 或 `AddDbContext` 中显式配置级别：</span><span class="sxs-lookup"><span data-stu-id="a4c67-332">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="a4c67-333">不再在实体实例上设置临时键值</span><span class="sxs-lookup"><span data-stu-id="a4c67-333">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="a4c67-334">跟踪问题 #12378</span><span class="sxs-lookup"><span data-stu-id="a4c67-334">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="a4c67-335">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-335">**Old behavior**</span></span>

<span data-ttu-id="a4c67-336">在 EF Core 3.0 之前，临时值已分配给所有键属性，这些属性稍后将具有数据库生成的实际值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-336">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="a4c67-337">通常这些临时值是较大负数。</span><span class="sxs-lookup"><span data-stu-id="a4c67-337">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="a4c67-338">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-338">**New behavior**</span></span>

<span data-ttu-id="a4c67-339">从 3.0 开始，EF Core 将临时键值存储为实体跟踪信息的一部分，并保持键属性本身不变。</span><span class="sxs-lookup"><span data-stu-id="a4c67-339">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="a4c67-340">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-340">**Why**</span></span>

<span data-ttu-id="a4c67-341">此更改是为了防止当之前由某个 `DbContext` 实例跟踪的实体移动到另一个 `DbContext` 实例时，临时键值错误地变成永久值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-341">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

<span data-ttu-id="a4c67-342">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-342">**Mitigations**</span></span>

<span data-ttu-id="a4c67-343">如果主键是存储生成的并且属于 `Added` 状态的实体，则将主键值分配到外键以在实体之间形成关联的应用可能会依赖于旧行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-343">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="a4c67-344">可通过以下方式避免：</span><span class="sxs-lookup"><span data-stu-id="a4c67-344">This can be avoided by:</span></span>
* <span data-ttu-id="a4c67-345">不使用存储生成的密钥。</span><span class="sxs-lookup"><span data-stu-id="a4c67-345">Not using store-generated keys.</span></span>
* <span data-ttu-id="a4c67-346">设置导航属性以形成关系，而不是设置外键值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-346">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="a4c67-347">从实体的跟踪信息中获取实际的临时键值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-347">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="a4c67-348">例如，即使 `blog.Id` 本身尚未设置，`context.Entry(blog).Property(e => e.Id).CurrentValue` 也将返回临时值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-348">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="a4c67-349">DetectChanges 遵循存储生成的键值</span><span class="sxs-lookup"><span data-stu-id="a4c67-349">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="a4c67-350">跟踪问题 #14616</span><span class="sxs-lookup"><span data-stu-id="a4c67-350">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="a4c67-351">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-351">**Old behavior**</span></span>

<span data-ttu-id="a4c67-352">在 EF Core 3.0 之前，`DetectChanges` 找到的未跟踪实体将在 `Added` 状态中被跟踪，并在调用 `SaveChanges` 时作为新行插入。</span><span class="sxs-lookup"><span data-stu-id="a4c67-352">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="a4c67-353">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-353">**New behavior**</span></span>

<span data-ttu-id="a4c67-354">从 EF Core 3.0 开始，如果实体使用生成的键值并设置了某个键值，则将在 `Modified` 状态下跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="a4c67-354">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="a4c67-355">这意味着假定存在实体的行，并且在调用 `SaveChanges` 时将更新该行。</span><span class="sxs-lookup"><span data-stu-id="a4c67-355">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="a4c67-356">如果未设置键值，或者实体类型未使用生成的键，则新实体仍将像先前版本一样被作为 `Added` 跟踪。</span><span class="sxs-lookup"><span data-stu-id="a4c67-356">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="a4c67-357">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-357">**Why**</span></span>

<span data-ttu-id="a4c67-358">进行此更改是为了在使用存储生成的键时更轻松、更一致地使用断开连接的实体图。</span><span class="sxs-lookup"><span data-stu-id="a4c67-358">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="a4c67-359">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-359">**Mitigations**</span></span>

<span data-ttu-id="a4c67-360">如果将实体类型配置为使用生成的键，但为新实例显式设置了键值，则此更改可能会中断应用程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-360">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="a4c67-361">解决方案是显式配置键属性，使其不使用生成的值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-361">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="a4c67-362">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="a4c67-362">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="a4c67-363">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="a4c67-363">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="a4c67-364">默认情况下，现在会立即发生级联删除</span><span class="sxs-lookup"><span data-stu-id="a4c67-364">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="a4c67-365">跟踪问题 #10114</span><span class="sxs-lookup"><span data-stu-id="a4c67-365">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="a4c67-366">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-366">**Old behavior**</span></span>

<span data-ttu-id="a4c67-367">在 3.0 之前，直到调用 SaveChanges 时，EF Core 才会应用级联操作（删除所需主体时或者在切断与所需主体的关系时删除依赖实体）。</span><span class="sxs-lookup"><span data-stu-id="a4c67-367">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="a4c67-368">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-368">**New behavior**</span></span>

<span data-ttu-id="a4c67-369">从 3.0 开始，一旦检测到触发条件，EF Core 就会应用级联操作。</span><span class="sxs-lookup"><span data-stu-id="a4c67-369">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="a4c67-370">例如，调用 `context.Remove()` 来删除主体实体将导致所有跟踪的相关必需依赖项也立即设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-370">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="a4c67-371">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-371">**Why**</span></span>

<span data-ttu-id="a4c67-372">此更改是为了改善数据绑定和审核方案的体验；在相关体验中，有必要了解在调用 `SaveChanges` 之前会删除哪些实体。</span><span class="sxs-lookup"><span data-stu-id="a4c67-372">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="a4c67-373">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-373">**Mitigations**</span></span>

<span data-ttu-id="a4c67-374">可以通过 `context.ChangeTracker` 上的设置还原以前的行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-374">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="a4c67-375">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-375">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="a4c67-376">单个查询中现在开始预先加载相关实体</span><span class="sxs-lookup"><span data-stu-id="a4c67-376">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="a4c67-377">跟踪问题 #18022</span><span class="sxs-lookup"><span data-stu-id="a4c67-377">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="a4c67-378">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-378">**Old behavior**</span></span>

<span data-ttu-id="a4c67-379">在 3.0 之前，通过 `Include` 运算符预先加载集合导航会导致在关系数据库上生成多个查询，每个相关实体类型对应一个查询。</span><span class="sxs-lookup"><span data-stu-id="a4c67-379">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="a4c67-380">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-380">**New behavior**</span></span>

<span data-ttu-id="a4c67-381">从 3.0 开始，EF Core 会在关系数据库上使用 JOIN 生成单个查询。</span><span class="sxs-lookup"><span data-stu-id="a4c67-381">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="a4c67-382">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-382">**Why**</span></span>

<span data-ttu-id="a4c67-383">以发出多个查询的方式实现单个 LINQ 查询会导致出现许多问题，包括由于需要执行多次数据库往返而引起的性能不佳问题，以及每个查询都可能会观察到不同的数据库状态的数据不一致问题。</span><span class="sxs-lookup"><span data-stu-id="a4c67-383">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="a4c67-384">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-384">**Mitigations**</span></span>

<span data-ttu-id="a4c67-385">尽管从技术上讲，这不是一项中断性变更，但当单个查询在集合导航中包含大量 `Include` 运算符时，这可能对应用程序性能产生相当大的影响。</span><span class="sxs-lookup"><span data-stu-id="a4c67-385">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="a4c67-386">[请参阅此评论](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085)，了解详细信息，以及如何以更有效的方式重写查询。</span><span class="sxs-lookup"><span data-stu-id="a4c67-386">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="a4c67-387">DeleteBehavior.Restrict 具有更简洁的语义</span><span class="sxs-lookup"><span data-stu-id="a4c67-387">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="a4c67-388">跟踪问题 #12661</span><span class="sxs-lookup"><span data-stu-id="a4c67-388">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="a4c67-389">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-389">**Old behavior**</span></span>

<span data-ttu-id="a4c67-390">3\.0 之前，`DeleteBehavior.Restrict` 使用 `Restrict` 语义在数据库中创建外键，但也以不明显的方式更改了内部修复。</span><span class="sxs-lookup"><span data-stu-id="a4c67-390">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="a4c67-391">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-391">**New behavior**</span></span>

<span data-ttu-id="a4c67-392">从 3.0 开始，`DeleteBehavior.Restrict` 确保使用 `Restrict` 语义创建外键--即无级联；在发生约束冲突时触发 - 但不会同时影响 EF 内部修复。</span><span class="sxs-lookup"><span data-stu-id="a4c67-392">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="a4c67-393">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-393">**Why**</span></span>

<span data-ttu-id="a4c67-394">进行此更改是为了改进以直观方式使用 `DeleteBehavior` 的体验，且不产生意外副作用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-394">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="a4c67-395">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-395">**Mitigations**</span></span>

<span data-ttu-id="a4c67-396">可使用 `DeleteBehavior.ClientNoAction` 还原以前的行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-396">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="a4c67-397">查询类型与实体类型合并</span><span class="sxs-lookup"><span data-stu-id="a4c67-397">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="a4c67-398">跟踪问题 #14194</span><span class="sxs-lookup"><span data-stu-id="a4c67-398">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="a4c67-399">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-399">**Old behavior**</span></span>

<span data-ttu-id="a4c67-400">在 EF Core 3.0 之前，[查询类型](xref:core/modeling/keyless-entity-types)是一种查询未以结构化方式定义主键的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-400">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="a4c67-401">也就是说，查询类型用于映射没有键的实体类型（更可能来自视图，但也可能来自表），而当有可用的键时则使用常规实体类型（更可能来自表，但也可能来自视图）。</span><span class="sxs-lookup"><span data-stu-id="a4c67-401">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="a4c67-402">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-402">**New behavior**</span></span>

<span data-ttu-id="a4c67-403">现在，查询类型只是一个没有主键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="a4c67-403">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="a4c67-404">无键实体类型与先前版本中的查询类型具有相同的功能。</span><span class="sxs-lookup"><span data-stu-id="a4c67-404">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="a4c67-405">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-405">**Why**</span></span>

<span data-ttu-id="a4c67-406">这样做是为了减少对查询类型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="a4c67-406">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="a4c67-407">具体来说，它们是无键实体类型，因此本质上是只读的，但是不应该仅仅因为实体类型需要是只读的就使用它们。</span><span class="sxs-lookup"><span data-stu-id="a4c67-407">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="a4c67-408">同样，它们通常映射到视图，但这只是因为视图通常不定义键。</span><span class="sxs-lookup"><span data-stu-id="a4c67-408">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="a4c67-409">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-409">**Mitigations**</span></span>

<span data-ttu-id="a4c67-410">API 的以下部分现已过时：</span><span class="sxs-lookup"><span data-stu-id="a4c67-410">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="a4c67-411">**`ModelBuilder.Query<>()`** - 需要调用 `ModelBuilder.Entity<>().HasNoKey()` 来将实体类型标记为没有键。</span><span class="sxs-lookup"><span data-stu-id="a4c67-411">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="a4c67-412">这仍然不会按约定配置，以避免在需要主键但是与约定不匹配时发生配置错误。</span><span class="sxs-lookup"><span data-stu-id="a4c67-412">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="a4c67-413">**`DbQuery<>`** - 应使用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-413">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="a4c67-414">**`DbContext.Query<>()`** - 应使用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-414">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="a4c67-415">**`IQueryTypeConfiguration<TQuery>`** - 应改用 `IEntityTypeConfiguration<TEntity>`\*\*。</span><span class="sxs-lookup"><span data-stu-id="a4c67-415">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>`\*\* should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="a4c67-416">由于查询所有属性设置为 `null` 的无键实体时存在 [3.x 中的问题](https://github.com/dotnet/efcore/issues/19537)，将返回 `null` 而不是实体，如果此问题适用于你的方案，则还要添加逻辑来处理结果中的 `null`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-416">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="a4c67-417">从属类型关系的配置 API 已更改</span><span class="sxs-lookup"><span data-stu-id="a4c67-417">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="a4c67-418">[跟踪问题 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[跟踪问题 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[跟踪问题 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="a4c67-418">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="a4c67-419">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-419">**Old behavior**</span></span>

<span data-ttu-id="a4c67-420">EF Core 3.0 之前，会在 `OwnsOne` 或 `OwnsMany` 调用之后直接执行所拥有关系的配置。</span><span class="sxs-lookup"><span data-stu-id="a4c67-420">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

<span data-ttu-id="a4c67-421">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-421">**New behavior**</span></span>

<span data-ttu-id="a4c67-422">从 EF Core 3.0 开始，Fluent API 会使用 `WithOwner()` 为所有者配置导航属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-422">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="a4c67-423">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-423">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="a4c67-424">与所有者之间关系相关的配置现会在 `WithOwner()` 之后关联起来，就像配置其他关系一样。</span><span class="sxs-lookup"><span data-stu-id="a4c67-424">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="a4c67-425">但从属类型本身的配置仍会在 `OwnsOne()/OwnsMany()` 之后关联。</span><span class="sxs-lookup"><span data-stu-id="a4c67-425">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="a4c67-426">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-426">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");

        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="a4c67-427">此外，使用固有类型目标调用 `Entity()``HasOne()` 或 `Set()` 现将引发异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-427">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="a4c67-428">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-428">**Why**</span></span>

<span data-ttu-id="a4c67-429">此更改是为了更清晰的区分从属类型本身的配置和与从属类型的_关系_的配置。</span><span class="sxs-lookup"><span data-stu-id="a4c67-429">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="a4c67-430">这反过来消除了诸如 `HasForeignKey` 之类的方法的模糊性和混淆。</span><span class="sxs-lookup"><span data-stu-id="a4c67-430">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="a4c67-431">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-431">**Mitigations**</span></span>

<span data-ttu-id="a4c67-432">更改从属类型关系的配置以使用新的 API 曲面，如上例所示。</span><span class="sxs-lookup"><span data-stu-id="a4c67-432">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="a4c67-433">与主体共享表的依赖实体现为可选项</span><span class="sxs-lookup"><span data-stu-id="a4c67-433">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="a4c67-434">跟踪问题 #9005</span><span class="sxs-lookup"><span data-stu-id="a4c67-434">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="a4c67-435">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-435">**Old behavior**</span></span>

<span data-ttu-id="a4c67-436">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="a4c67-436">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
<span data-ttu-id="a4c67-437">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表，则在添加新的 `Order` 时，始终需要 `OrderDetails` 实例。</span><span class="sxs-lookup"><span data-stu-id="a4c67-437">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="a4c67-438">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-438">**New behavior**</span></span>

<span data-ttu-id="a4c67-439">自 3.0 起，EF Core 允许添加 `Order` 而不添加 `OrderDetails`，并将主键之外的所有 `OrderDetails` 属性映射到不为 null 的列中。</span><span class="sxs-lookup"><span data-stu-id="a4c67-439">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="a4c67-440">查询时，如果其任意所需属性均没有值，或者它在主键之外没有任何必需属性且所有属性均为 `null`，则 EF Core 会将 `OrderDetails` 设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-440">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="a4c67-441">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-441">**Mitigations**</span></span>

<span data-ttu-id="a4c67-442">如果你的模型具有依赖于所有可选列的表共享，但指向该共享的导航不应为 `null`，则应修改应用程序，使其处理导航为 `null` 的情况。</span><span class="sxs-lookup"><span data-stu-id="a4c67-442">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="a4c67-443">如果此方法不可行，则应向实体类型添加一个必需属性，或者至少要有一个属性分配有非 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-443">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="a4c67-444">与并发标记列共享表的所有实体均必须将其映射到属性</span><span class="sxs-lookup"><span data-stu-id="a4c67-444">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="a4c67-445">跟踪问题 #14154</span><span class="sxs-lookup"><span data-stu-id="a4c67-445">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="a4c67-446">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-446">**Old behavior**</span></span>

<span data-ttu-id="a4c67-447">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="a4c67-447">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
<span data-ttu-id="a4c67-448">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表，则只更新 `OrderDetails` 时，将不更新客户端上的 `Version` 值且下次更新将失败。</span><span class="sxs-lookup"><span data-stu-id="a4c67-448">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="a4c67-449">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-449">**New behavior**</span></span>

<span data-ttu-id="a4c67-450">自 3.0 起，如果 新的 `Version` 值拥有 `OrderDetails`则 EF Core 会将该值传播给 `Order`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-450">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="a4c67-451">否则，会在模型验证期间引发异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-451">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="a4c67-452">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-452">**Why**</span></span>

<span data-ttu-id="a4c67-453">进行此更改的目的是避免在仅更新映射到同一张表的其中一个实体时使用过时的并发标记值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-453">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="a4c67-454">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-454">**Mitigations**</span></span>

<span data-ttu-id="a4c67-455">共享表的所有实体都必须包含一个映射到并发标记列的属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-455">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="a4c67-456">可在影子状态中创建一个：</span><span class="sxs-lookup"><span data-stu-id="a4c67-456">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="a4c67-457">如果没有所有者，则无法使用跟踪查询来查询从属实体</span><span class="sxs-lookup"><span data-stu-id="a4c67-457">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="a4c67-458">跟踪问题 #18876</span><span class="sxs-lookup"><span data-stu-id="a4c67-458">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="a4c67-459">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-459">**Old behavior**</span></span>

<span data-ttu-id="a4c67-460">在 EF Core 3.0 之前，可像任何其他导航一样查询从属实体。</span><span class="sxs-lookup"><span data-stu-id="a4c67-460">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="a4c67-461">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-461">**New behavior**</span></span>

<span data-ttu-id="a4c67-462">自版本 3.0 起，如果跟踪查询在没有所有者的情况下投射一个从属实体，EF Core 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-462">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="a4c67-463">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-463">**Why**</span></span>

<span data-ttu-id="a4c67-464">如果没有所有者，则无法操作从属实体，因此在绝大多数情况下，不该使用此方式查询它们。</span><span class="sxs-lookup"><span data-stu-id="a4c67-464">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="a4c67-465">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-465">**Mitigations**</span></span>

<span data-ttu-id="a4c67-466">如果应跟踪从属实体，以便之后以任何方式进行修改，则应在查询中包含所有者。</span><span class="sxs-lookup"><span data-stu-id="a4c67-466">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="a4c67-467">否则，请添加一个 `AsNoTracking()` 调用：</span><span class="sxs-lookup"><span data-stu-id="a4c67-467">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="a4c67-468">对于所有派生的类型而言，从未映射的类型继承的属性现在会映射到一个列中</span><span class="sxs-lookup"><span data-stu-id="a4c67-468">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="a4c67-469">跟踪问题 #13998</span><span class="sxs-lookup"><span data-stu-id="a4c67-469">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="a4c67-470">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-470">**Old behavior**</span></span>

<span data-ttu-id="a4c67-471">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="a4c67-471">Consider the following model:</span></span>
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="a4c67-472">在 EF Core 3.0 之前，`ShippingAddress` 属性会为 `BulkOrder` 和 `Order` 默认映射到单独的列中。</span><span class="sxs-lookup"><span data-stu-id="a4c67-472">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="a4c67-473">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-473">**New behavior**</span></span>

<span data-ttu-id="a4c67-474">自 3.0 起，EF Core只会为 `ShippingAddress` 创建一个列。</span><span class="sxs-lookup"><span data-stu-id="a4c67-474">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="a4c67-475">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-475">**Why**</span></span>

<span data-ttu-id="a4c67-476">旧行为不是预期行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-476">The old behavoir was unexpected.</span></span>

<span data-ttu-id="a4c67-477">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-477">**Mitigations**</span></span>

<span data-ttu-id="a4c67-478">属性仍可显式映射到所派生的类型上的单独的列中：</span><span class="sxs-lookup"><span data-stu-id="a4c67-478">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="a4c67-479">外键属性约定不再匹配与主体属性相同的名称</span><span class="sxs-lookup"><span data-stu-id="a4c67-479">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="a4c67-480">跟踪问题 #13274</span><span class="sxs-lookup"><span data-stu-id="a4c67-480">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="a4c67-481">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-481">**Old behavior**</span></span>

<span data-ttu-id="a4c67-482">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="a4c67-482">Consider the following model:</span></span>
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
<span data-ttu-id="a4c67-483">在 EF Core 3.0 之前，`CustomerId` 属性将按约定用于外键。</span><span class="sxs-lookup"><span data-stu-id="a4c67-483">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="a4c67-484">但是，如果 `Order` 是从属类型，那么这也会使 `CustomerId` 成为主键，这通常不是预期结果。</span><span class="sxs-lookup"><span data-stu-id="a4c67-484">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="a4c67-485">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-485">**New behavior**</span></span>

<span data-ttu-id="a4c67-486">自 3.0 起，如果外键的主体属性名称相同，EF Core 不会尝试通过转换来为外键使用属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-486">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="a4c67-487">与主体属性名称关联的主体类型名称和与主体属性名称模式关联的导航名称仍然相匹配。</span><span class="sxs-lookup"><span data-stu-id="a4c67-487">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="a4c67-488">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-488">For example:</span></span>

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

<span data-ttu-id="a4c67-489">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-489">**Why**</span></span>

<span data-ttu-id="a4c67-490">此更改是为了避免错误地在从属类型上定义主键属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-490">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="a4c67-491">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-491">**Mitigations**</span></span>

<span data-ttu-id="a4c67-492">如果该属性将成为外键，即为主键的一部分，则显式配置它。</span><span class="sxs-lookup"><span data-stu-id="a4c67-492">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="a4c67-493">现在，如果在 TransactionScope 完成前不再使用数据库连接，则该连接会关闭</span><span class="sxs-lookup"><span data-stu-id="a4c67-493">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="a4c67-494">跟踪问题 #14218</span><span class="sxs-lookup"><span data-stu-id="a4c67-494">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="a4c67-495">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-495">**Old behavior**</span></span>

<span data-ttu-id="a4c67-496">在 EF Core 3.0 之前，如果上下文打开了 `TransactionScope` 中的连接，则该连接在 `TransactionScope` 处于活动期间仍保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="a4c67-496">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

<span data-ttu-id="a4c67-497">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-497">**New behavior**</span></span>

<span data-ttu-id="a4c67-498">自 3.0 起，一旦不再使用连接，EF Core 就会将其关闭。</span><span class="sxs-lookup"><span data-stu-id="a4c67-498">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="a4c67-499">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-499">**Why**</span></span>

<span data-ttu-id="a4c67-500">此更改让你能够在同一 `TransactionScope` 中使用多个上下文。</span><span class="sxs-lookup"><span data-stu-id="a4c67-500">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="a4c67-501">新行为也与 EF6 一致。</span><span class="sxs-lookup"><span data-stu-id="a4c67-501">The new behavior also matches EF6.</span></span>

<span data-ttu-id="a4c67-502">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-502">**Mitigations**</span></span>

<span data-ttu-id="a4c67-503">如果连接需要保持打开状态，则显式调用 `OpenConnection()` 将确保 EF Core 不永久关闭此连接：</span><span class="sxs-lookup"><span data-stu-id="a4c67-503">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="a4c67-504">每个属性使用独立的内存中整数键生成</span><span class="sxs-lookup"><span data-stu-id="a4c67-504">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="a4c67-505">跟踪问题 #6872</span><span class="sxs-lookup"><span data-stu-id="a4c67-505">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="a4c67-506">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-506">**Old behavior**</span></span>

<span data-ttu-id="a4c67-507">在 EF Core 3.0 之前，一个共享值生成器用于所有内存中的整数键属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-507">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="a4c67-508">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-508">**New behavior**</span></span>

<span data-ttu-id="a4c67-509">从 EF Core 3.0 开始，每个整数键属性在使用内存数据库时都会获取其自己的值生成器。</span><span class="sxs-lookup"><span data-stu-id="a4c67-509">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="a4c67-510">此外，如果删除了数据库，则会为所有表重置键生成。</span><span class="sxs-lookup"><span data-stu-id="a4c67-510">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="a4c67-511">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-511">**Why**</span></span>

<span data-ttu-id="a4c67-512">此更改的目的是使内存中的键生成更接近于实际的数据库键生成，并改进在使用内存中的数据库时隔离测试的功能。</span><span class="sxs-lookup"><span data-stu-id="a4c67-512">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="a4c67-513">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-513">**Mitigations**</span></span>

<span data-ttu-id="a4c67-514">这可能会中断依赖于特定内存中键值的应用程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-514">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="a4c67-515">请考虑不依赖于特定键值，或者进行更新以匹配新行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-515">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="a4c67-516">默认情况下使用支持字段</span><span class="sxs-lookup"><span data-stu-id="a4c67-516">Backing fields are used by default</span></span>

[<span data-ttu-id="a4c67-517">跟踪问题 #12430</span><span class="sxs-lookup"><span data-stu-id="a4c67-517">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="a4c67-518">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-518">**Old behavior**</span></span>

<span data-ttu-id="a4c67-519">在 3.0 之前，即使已知属性的支持字段，EF Core 仍将默认使用属性 getter 和 setter 方法读取和写入属性值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-519">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="a4c67-520">例外情况是查询执行，如果已知，将直接设置支持字段。</span><span class="sxs-lookup"><span data-stu-id="a4c67-520">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="a4c67-521">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-521">**New behavior**</span></span>

<span data-ttu-id="a4c67-522">从 EF Core 3.0 开始，如果已知属性的支持字段，EF Core 将始终使用支持字段读取和写入该属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-522">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="a4c67-523">如果应用程序依赖于编码到 getter 或 setter 方法中的其他行为，则可能导致应用程序中断。</span><span class="sxs-lookup"><span data-stu-id="a4c67-523">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="a4c67-524">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-524">**Why**</span></span>

<span data-ttu-id="a4c67-525">此更改是为了防止 EF Core 在执行涉及实体的数据库操作时默认错误地触发业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="a4c67-525">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="a4c67-526">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-526">**Mitigations**</span></span>

<span data-ttu-id="a4c67-527">可以通过在 `ModelBuilder` 上配置属性访问模式来恢复 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-527">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="a4c67-528">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-528">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="a4c67-529">如果找到多个兼容的支持字段，则引发</span><span class="sxs-lookup"><span data-stu-id="a4c67-529">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="a4c67-530">跟踪问题 #12523</span><span class="sxs-lookup"><span data-stu-id="a4c67-530">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="a4c67-531">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-531">**Old behavior**</span></span>

<span data-ttu-id="a4c67-532">在 EF Core 3.0 之前，如果多个字段与查找属性的后备字段的规则匹配，则将基于某种优先顺序选择一个字段。</span><span class="sxs-lookup"><span data-stu-id="a4c67-532">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="a4c67-533">这可能导致在不明确的情况下使用错误字段。</span><span class="sxs-lookup"><span data-stu-id="a4c67-533">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="a4c67-534">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-534">**New behavior**</span></span>

<span data-ttu-id="a4c67-535">从 EF Core 3.0 开始，如果多个字段与同一属性匹配，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-535">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="a4c67-536">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-536">**Why**</span></span>

<span data-ttu-id="a4c67-537">此更改是为了避免在只有一个字段是正确的情况下无提示地使用另一个字段。</span><span class="sxs-lookup"><span data-stu-id="a4c67-537">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="a4c67-538">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-538">**Mitigations**</span></span>

<span data-ttu-id="a4c67-539">具有不明确的支持字段的属性必须具有显式指定的字段。</span><span class="sxs-lookup"><span data-stu-id="a4c67-539">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="a4c67-540">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="a4c67-540">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="a4c67-541">“仅字段”属性名应与字段名匹配</span><span class="sxs-lookup"><span data-stu-id="a4c67-541">Field-only property names should match the field name</span></span>

<span data-ttu-id="a4c67-542">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-542">**Old behavior**</span></span>

<span data-ttu-id="a4c67-543">在 EF Core 3.0 之前，可通过字符串值指定属性，如果在 .NET 类型上找不到具有该名称的属性，则 EF Core 将尝试使用约定规则将其与字段匹配。</span><span class="sxs-lookup"><span data-stu-id="a4c67-543">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="a4c67-544">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-544">**New behavior**</span></span>

<span data-ttu-id="a4c67-545">从 EF Core 3.0 开始，“仅字段”属性必须与字段名完全匹配。</span><span class="sxs-lookup"><span data-stu-id="a4c67-545">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="a4c67-546">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-546">**Why**</span></span>

<span data-ttu-id="a4c67-547">进行此更改是为了避免对两个名称相似的属性使用相同的字段，这也使得仅字段属性的匹配规则与映射到 CLR 属性的属性相同。</span><span class="sxs-lookup"><span data-stu-id="a4c67-547">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="a4c67-548">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-548">**Mitigations**</span></span>

<span data-ttu-id="a4c67-549">仅字段属性名必须与它们映射到的字段名相同。</span><span class="sxs-lookup"><span data-stu-id="a4c67-549">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="a4c67-550">在 3.0 版之后的 EF Core 未来版本中，我们计划重新启用显式配置与属性名称不同的字段名称（请参阅问题 [15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)）：</span><span class="sxs-lookup"><span data-stu-id="a4c67-550">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="a4c67-551">AddDbContext/AddDbContextPool 不再调用 AddLogging 和 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="a4c67-551">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="a4c67-552">跟踪问题 #14756</span><span class="sxs-lookup"><span data-stu-id="a4c67-552">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="a4c67-553">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-553">**Old behavior**</span></span>

<span data-ttu-id="a4c67-554">在 EF Core 3.0 之前，调用 `AddDbContext` 或 `AddDbContextPool` 的操作也会通过调用 [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 和 [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 向 DI 注册日志记录和内存缓存服务。</span><span class="sxs-lookup"><span data-stu-id="a4c67-554">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="a4c67-555">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-555">**New behavior**</span></span>

<span data-ttu-id="a4c67-556">从 EF Core 3.0 开始，`AddDbContext` 和 `AddDbContextPool` 将无法再在依赖注入 (DI) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="a4c67-556">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="a4c67-557">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-557">**Why**</span></span>

<span data-ttu-id="a4c67-558">EF Core 3.0 不要求这些服务位于应用程序的 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="a4c67-558">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="a4c67-559">但是，如果 `ILoggerFactory` 在应用程序的 DI 容器中注册，它仍然会由 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-559">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="a4c67-560">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-560">**Mitigations**</span></span>

<span data-ttu-id="a4c67-561">如果应用程序需要这些服务，则使用 [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 将它们显式注册到 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="a4c67-561">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="a4c67-562">AddEntityFramework\* 添加具有大小限制的 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="a4c67-562">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="a4c67-563">跟踪问题 #12905</span><span class="sxs-lookup"><span data-stu-id="a4c67-563">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="a4c67-564">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-564">**Old behavior**</span></span>

<span data-ttu-id="a4c67-565">在 EF Core 3.0 之前，调用 `AddEntityFramework*` 方法还将向 DI 注册内存缓存服务，且没有大小限制。</span><span class="sxs-lookup"><span data-stu-id="a4c67-565">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="a4c67-566">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-566">**New behavior**</span></span>

<span data-ttu-id="a4c67-567">自 EF Core 3.0 起，`AddEntityFramework*` 注册的 IMemoryCache 服务具有大小限制。</span><span class="sxs-lookup"><span data-stu-id="a4c67-567">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="a4c67-568">如果随后添加的任何其他服务依赖于 IMemoryCache，则它们很快就会达到默认限制，从而导致异常或性能下降。</span><span class="sxs-lookup"><span data-stu-id="a4c67-568">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="a4c67-569">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-569">**Why**</span></span>

<span data-ttu-id="a4c67-570">如果查询缓存逻辑中存在 bug 或者查询是动态生成的，则无限制地使用 IMemoryCache 可能会导致内存使用量不受控制。</span><span class="sxs-lookup"><span data-stu-id="a4c67-570">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="a4c67-571">设定默认限制可减少潜在的 DoS 攻击。</span><span class="sxs-lookup"><span data-stu-id="a4c67-571">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="a4c67-572">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-572">**Mitigations**</span></span>

<span data-ttu-id="a4c67-573">在大多数情况下，如果同时调用了 `AddDbContext` 或 `AddDbContextPool`，则无需调用 `AddEntityFramework*`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-573">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="a4c67-574">因此，最好的缓解措施是删除 `AddEntityFramework*` 调用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-574">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="a4c67-575">如果应用程序需要这些服务，则使用 [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 预先向 DI 容器显式注册 IMemoryCache 实现。</span><span class="sxs-lookup"><span data-stu-id="a4c67-575">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="a4c67-576">DbContext.Entry 现在执行本地 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="a4c67-576">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="a4c67-577">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="a4c67-577">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="a4c67-578">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-578">**Old behavior**</span></span>

<span data-ttu-id="a4c67-579">在 EF Core 3.0 之前，调用 `DbContext.Entry` 将导致检测到所有被跟踪实体的更改。</span><span class="sxs-lookup"><span data-stu-id="a4c67-579">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="a4c67-580">这确保了 `EntityEntry` 中暴露的状态是最新的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-580">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="a4c67-581">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-581">**New behavior**</span></span>

<span data-ttu-id="a4c67-582">从 EF Core 3.0 开始，调用 `DbContext.Entry` 现在只会尝试检测给定实体和与之相关的任何跟踪主体实体的更改。</span><span class="sxs-lookup"><span data-stu-id="a4c67-582">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="a4c67-583">这意味着可能无法通过调用此方法检测到其他位置的更改，这可能会影响应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="a4c67-583">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="a4c67-584">请注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 设置为 `false`，则即使是本地更改检测也将被禁用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-584">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="a4c67-585">导致更改检测的其他方法（例如 `ChangeTracker.Entries` 和 `SaveChanges`）仍然会导致所有被跟踪实体的完整 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-585">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="a4c67-586">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-586">**Why**</span></span>

<span data-ttu-id="a4c67-587">此更改是为了提高使用 `context.Entry` 的默认性能。</span><span class="sxs-lookup"><span data-stu-id="a4c67-587">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="a4c67-588">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-588">**Mitigations**</span></span>

<span data-ttu-id="a4c67-589">在调用 `Entry` 之前显式调用 `ChangeTracker.DetectChanges()` 以确保 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-589">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="a4c67-590">默认情况下，字符串和字节数组键不是客户端生成的</span><span class="sxs-lookup"><span data-stu-id="a4c67-590">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="a4c67-591">跟踪问题 #14617</span><span class="sxs-lookup"><span data-stu-id="a4c67-591">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="a4c67-592">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-592">**Old behavior**</span></span>

<span data-ttu-id="a4c67-593">在 EF Core 3.0 之前，可以使用 `string` 和 `byte[]` 键属性，而不需要显式地设置非 null 值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-593">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="a4c67-594">在这种情况下，键值将在客户端上生成为 GUID，并序列化为 `byte[]` 的字节。</span><span class="sxs-lookup"><span data-stu-id="a4c67-594">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="a4c67-595">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-595">**New behavior**</span></span>

<span data-ttu-id="a4c67-596">从 EF Core 3.0 开始，将引发异常，指示未设置任何键值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-596">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="a4c67-597">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-597">**Why**</span></span>

<span data-ttu-id="a4c67-598">之所以进行此更改是因为客户端生成的 `string`/`byte[]` 值通常没有用，并且默认行为使得很难以通用方式推断生成的键值。</span><span class="sxs-lookup"><span data-stu-id="a4c67-598">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="a4c67-599">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-599">**Mitigations**</span></span>

<span data-ttu-id="a4c67-600">如果没有设置其他非 null 值，则可以通过显式指定键属性应使用生成的值来获得 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="a4c67-600">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="a4c67-601">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="a4c67-601">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="a4c67-602">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="a4c67-602">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="a4c67-603">ILoggerFactory 现在是一个在一定范围内有效的服务</span><span class="sxs-lookup"><span data-stu-id="a4c67-603">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="a4c67-604">跟踪问题 #14698</span><span class="sxs-lookup"><span data-stu-id="a4c67-604">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="a4c67-605">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-605">**Old behavior**</span></span>

<span data-ttu-id="a4c67-606">在 EF Core 3.0 之前，`ILoggerFactory` 被注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="a4c67-606">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="a4c67-607">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-607">**New behavior**</span></span>

<span data-ttu-id="a4c67-608">从 EF Core 3.0 开始，`ILoggerFactory` 现已注册为作用域。</span><span class="sxs-lookup"><span data-stu-id="a4c67-608">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="a4c67-609">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-609">**Why**</span></span>

<span data-ttu-id="a4c67-610">此更改是为了允许记录器与 `DbContext` 实例关联，从而启用其他功能并删除一些反常行为，例如内部服务提供商爆炸式增长的情况。</span><span class="sxs-lookup"><span data-stu-id="a4c67-610">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="a4c67-611">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-611">**Mitigations**</span></span>

<span data-ttu-id="a4c67-612">除非在 EF Core 内部服务提供商上注册和使用自定义服务，否则此更改不应影响应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="a4c67-612">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="a4c67-613">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="a4c67-613">This isn't common.</span></span>
<span data-ttu-id="a4c67-614">在这些情况下，大多数事情仍然有效，但是需要更改依赖于 `ILoggerFactory` 的任何单一实例服务以便以不同的方式获取 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-614">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="a4c67-615">如果遇到此类情况，请在 [EF Core GitHub 问题跟踪程序](https://github.com/aspnet/EntityFrameworkCore/issues)上提交一个问题，让我们知道你是如何使用 `ILoggerFactory` 的，以便我们更好地理解今后如何避免这种情况再次发生。</span><span class="sxs-lookup"><span data-stu-id="a4c67-615">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="a4c67-616">延迟加载代理不再假定导航属性已完全加载</span><span class="sxs-lookup"><span data-stu-id="a4c67-616">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="a4c67-617">跟踪问题 #12780</span><span class="sxs-lookup"><span data-stu-id="a4c67-617">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="a4c67-618">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-618">**Old behavior**</span></span>

<span data-ttu-id="a4c67-619">在 EF Core 3.0 之前，一旦 `DbContext` 被处置，就无法知道从该上下文获得的实体上的给定导航属性是否已完全加载。</span><span class="sxs-lookup"><span data-stu-id="a4c67-619">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="a4c67-620">相反，如果导航有一个非 null 值，代理将假定加载一个引用导航，如果导航非空，则假定加载集合导航。</span><span class="sxs-lookup"><span data-stu-id="a4c67-620">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="a4c67-621">在这些情况下，尝试延迟加载将是无效的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-621">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="a4c67-622">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-622">**New behavior**</span></span>

<span data-ttu-id="a4c67-623">从 EF Core 3.0 开始，代理会跟踪是否加载了导航属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-623">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="a4c67-624">这意味着如果尝试访问在释放了上下文之后加载的导航属性，其结果始终是无操作，即使加载的导航为空或为 null。</span><span class="sxs-lookup"><span data-stu-id="a4c67-624">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="a4c67-625">相反，即使导航属性是非空集合，尝试访问未加载的导航属性也会引发异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-625">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="a4c67-626">如果出现这种情况，则表示应用程序代码在无效时间尝试使用延迟加载，应将应用程序更改为不执行此操作。</span><span class="sxs-lookup"><span data-stu-id="a4c67-626">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="a4c67-627">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-627">**Why**</span></span>

<span data-ttu-id="a4c67-628">此更改是为了在尝试对已释放的 `DbContext` 实例进行延迟加载时使行为保持一致和正确。</span><span class="sxs-lookup"><span data-stu-id="a4c67-628">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="a4c67-629">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-629">**Mitigations**</span></span>

<span data-ttu-id="a4c67-630">更新应用程序代码，以避免尝试对已释放的上下文进行延迟加载，或者将其配置为无操作，如异常消息中所述。</span><span class="sxs-lookup"><span data-stu-id="a4c67-630">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="a4c67-631">默认情况下，现在过度创建内部服务提供程序是一个错误</span><span class="sxs-lookup"><span data-stu-id="a4c67-631">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="a4c67-632">跟踪问题 #10236</span><span class="sxs-lookup"><span data-stu-id="a4c67-632">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="a4c67-633">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-633">**Old behavior**</span></span>

<span data-ttu-id="a4c67-634">在 EF Core 3.0 之前，对于创建了大量内部服务提供程序的应用程序，将会记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="a4c67-634">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="a4c67-635">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-635">**New behavior**</span></span>

<span data-ttu-id="a4c67-636">从 EF Core 3.0 开始，现在会考虑此警告，并引发错误和异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-636">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

<span data-ttu-id="a4c67-637">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-637">**Why**</span></span>

<span data-ttu-id="a4c67-638">此更改是为了通过更明显地暴露这个病态案例来驱动生成更好的应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="a4c67-638">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="a4c67-639">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-639">**Mitigations**</span></span>

<span data-ttu-id="a4c67-640">遇到此错误时，最合适的操作是了解根本原因并停止创建如此多的内部服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-640">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="a4c67-641">但是，可以通过 `DbContextOptionsBuilder` 上的配置将错误转换回警告（或忽略）。</span><span class="sxs-lookup"><span data-stu-id="a4c67-641">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="a4c67-642">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-642">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="a4c67-643">使用单个字符串调用 HasOne/HasMany 的新行为</span><span class="sxs-lookup"><span data-stu-id="a4c67-643">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="a4c67-644">跟踪问题 #9171</span><span class="sxs-lookup"><span data-stu-id="a4c67-644">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="a4c67-645">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-645">**Old behavior**</span></span>

<span data-ttu-id="a4c67-646">在 EF Core 3.0 之前，对通过单个字符串调用 `HasOne` 或 `HasMany` 的代码的解释令人困惑。</span><span class="sxs-lookup"><span data-stu-id="a4c67-646">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="a4c67-647">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-647">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="a4c67-648">该代码看起来是使用 `Entrance` 导航属性（可能是私有属性）将 `Samurai` 与某些其他实体类型关联起来的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-648">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="a4c67-649">实际上，此代码试图创建与某个名为 `Entrance` 的实体类型的关系，该实体类型没有导航属性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-649">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="a4c67-650">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-650">**New behavior**</span></span>

<span data-ttu-id="a4c67-651">从 EF Core 3.0 开始，上面的代码现执行它以前应执行的操作。</span><span class="sxs-lookup"><span data-stu-id="a4c67-651">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="a4c67-652">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-652">**Why**</span></span>

<span data-ttu-id="a4c67-653">这一旧行为令人非常困惑，尤其是在读取配置代码和查找错误时。</span><span class="sxs-lookup"><span data-stu-id="a4c67-653">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="a4c67-654">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-654">**Mitigations**</span></span>

<span data-ttu-id="a4c67-655">这只会中断使用类型名称字符串显式配置关系而无需显式指定导航属性的应用程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-655">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="a4c67-656">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="a4c67-656">This is not common.</span></span>
<span data-ttu-id="a4c67-657">以前的行为可以通过显式传递导航属性名称的 `null` 获得。</span><span class="sxs-lookup"><span data-stu-id="a4c67-657">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="a4c67-658">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-658">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="a4c67-659">多个异步方法的返回类型已从 Task 更改为 ValueTask</span><span class="sxs-lookup"><span data-stu-id="a4c67-659">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="a4c67-660">跟踪问题 #15184</span><span class="sxs-lookup"><span data-stu-id="a4c67-660">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="a4c67-661">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-661">**Old behavior**</span></span>

<span data-ttu-id="a4c67-662">以下异步方法之前返回的是 `Task<T>`：</span><span class="sxs-lookup"><span data-stu-id="a4c67-662">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="a4c67-663">`ValueGenerator.NextValueAsync()`（及派生类）</span><span class="sxs-lookup"><span data-stu-id="a4c67-663">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="a4c67-664">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-664">**New behavior**</span></span>

<span data-ttu-id="a4c67-665">上述方法现返回一个 `ValueTask<T>`，其中 `T` 与前述相同。</span><span class="sxs-lookup"><span data-stu-id="a4c67-665">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="a4c67-666">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-666">**Why**</span></span>

<span data-ttu-id="a4c67-667">此更改会减少在调用这些方法时发生的堆分配数量，从而提高整体性能。</span><span class="sxs-lookup"><span data-stu-id="a4c67-667">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="a4c67-668">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-668">**Mitigations**</span></span>

<span data-ttu-id="a4c67-669">仅需重新编译只等待上述 API 的应用程序 -无需更改源。</span><span class="sxs-lookup"><span data-stu-id="a4c67-669">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="a4c67-670">更复杂的用法（例如，将返回的 `Task` 传递到 `Task.WhenAny()`）通常需要通过对返回的 `ValueTask<T>` 调用 `AsTask()` 来将其转换为 `Task<T>`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-670">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="a4c67-671">请注意，这会抵消此更改所带来的分配数减少优势。</span><span class="sxs-lookup"><span data-stu-id="a4c67-671">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="a4c67-672">关系式：TypeMapping 注释现在只是 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="a4c67-672">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="a4c67-673">跟踪问题 #9913</span><span class="sxs-lookup"><span data-stu-id="a4c67-673">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="a4c67-674">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-674">**Old behavior**</span></span>

<span data-ttu-id="a4c67-675">类型映射注释的注释名称是“Relational：TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="a4c67-675">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="a4c67-676">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-676">**New behavior**</span></span>

<span data-ttu-id="a4c67-677">类型映射注释的注释名称现在是“TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="a4c67-677">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="a4c67-678">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-678">**Why**</span></span>

<span data-ttu-id="a4c67-679">类型映射现在不仅用于关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="a4c67-679">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="a4c67-680">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-680">**Mitigations**</span></span>

<span data-ttu-id="a4c67-681">这只会中断直接作为注释访问类型映射的应用程序，这不常见。</span><span class="sxs-lookup"><span data-stu-id="a4c67-681">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="a4c67-682">最合适的修复操作是使用 API 曲面来访问类型映射，而不是直接使用注释。</span><span class="sxs-lookup"><span data-stu-id="a4c67-682">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="a4c67-683">派生类型上的 ToTable 会引发异常</span><span class="sxs-lookup"><span data-stu-id="a4c67-683">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="a4c67-684">跟踪问题 #11811</span><span class="sxs-lookup"><span data-stu-id="a4c67-684">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="a4c67-685">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-685">**Old behavior**</span></span>

<span data-ttu-id="a4c67-686">在 EF Core 3.0 之前，将忽略调用派生类型的 `ToTable()`，因为只有继承映射策略是 TPH，这是无效的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-686">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

<span data-ttu-id="a4c67-687">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-687">**New behavior**</span></span>

<span data-ttu-id="a4c67-688">从 EF Core 3.0 开始，同时为在以后的版本中添加 TPT 和 TPC 支持做准备，调用派生类型的 `ToTable()` 现在将引发异常，以避免将来发生意外的映射更改。</span><span class="sxs-lookup"><span data-stu-id="a4c67-688">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="a4c67-689">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-689">**Why**</span></span>

<span data-ttu-id="a4c67-690">目前，将派生类型映射到不同的表是无效的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-690">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="a4c67-691">这种改变避免了在将来当它变为有效时被中断。</span><span class="sxs-lookup"><span data-stu-id="a4c67-691">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="a4c67-692">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-692">**Mitigations**</span></span>

<span data-ttu-id="a4c67-693">删除将派生类型映射到其他表的任何尝试。</span><span class="sxs-lookup"><span data-stu-id="a4c67-693">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="a4c67-694">用 HasIndex 替换 ForSqlServerHasIndex</span><span class="sxs-lookup"><span data-stu-id="a4c67-694">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="a4c67-695">跟踪问题 #12366</span><span class="sxs-lookup"><span data-stu-id="a4c67-695">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="a4c67-696">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-696">**Old behavior**</span></span>

<span data-ttu-id="a4c67-697">在 EF Core 3.0 之前，`ForSqlServerHasIndex().ForSqlServerInclude()` 提供了一种配置与 `INCLUDE` 一起使用的列的方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-697">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="a4c67-698">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-698">**New behavior**</span></span>

<span data-ttu-id="a4c67-699">从 EF Core 3.0 开始，现在支持在关系级别上对索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-699">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="a4c67-700">请使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-700">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="a4c67-701">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-701">**Why**</span></span>

<span data-ttu-id="a4c67-702">此更改是为了将用于索引的 API 与 `Include` 合并到一个位置，以供所有数据库提供程序使用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-702">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="a4c67-703">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-703">**Mitigations**</span></span>

<span data-ttu-id="a4c67-704">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="a4c67-704">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="a4c67-705">元数据 API 更改</span><span class="sxs-lookup"><span data-stu-id="a4c67-705">Metadata API changes</span></span>

[<span data-ttu-id="a4c67-706">跟踪问题 #214</span><span class="sxs-lookup"><span data-stu-id="a4c67-706">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="a4c67-707">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-707">**New behavior**</span></span>

<span data-ttu-id="a4c67-708">以下属性已转换为扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a4c67-708">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="a4c67-709">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-709">**Why**</span></span>

<span data-ttu-id="a4c67-710">此更改简化了上述接口的实现。</span><span class="sxs-lookup"><span data-stu-id="a4c67-710">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="a4c67-711">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-711">**Mitigations**</span></span>

<span data-ttu-id="a4c67-712">使用新的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-712">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="a4c67-713">特定于提供程序的元数据 API 更改</span><span class="sxs-lookup"><span data-stu-id="a4c67-713">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="a4c67-714">跟踪问题 #214</span><span class="sxs-lookup"><span data-stu-id="a4c67-714">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="a4c67-715">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-715">**New behavior**</span></span>

<span data-ttu-id="a4c67-716">将展开特定于提供程序的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a4c67-716">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="a4c67-717">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-717">**Why**</span></span>

<span data-ttu-id="a4c67-718">此更改简化了上述扩展方法的实现。</span><span class="sxs-lookup"><span data-stu-id="a4c67-718">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="a4c67-719">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-719">**Mitigations**</span></span>

<span data-ttu-id="a4c67-720">使用新的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-720">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="a4c67-721">EF Core 不再发送 pragma 来执行 SQLite FK</span><span class="sxs-lookup"><span data-stu-id="a4c67-721">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="a4c67-722">跟踪问题 #12151</span><span class="sxs-lookup"><span data-stu-id="a4c67-722">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="a4c67-723">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-723">**Old behavior**</span></span>

<span data-ttu-id="a4c67-724">在 EF Core 3.0 之前，当打开与 SQLite 的连接时，EF Core 会发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-724">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="a4c67-725">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-725">**New behavior**</span></span>

<span data-ttu-id="a4c67-726">从 EF Core 3.0 开始，当打开到 SQLite 的连接时，EF Core 不再发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-726">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="a4c67-727">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-727">**Why**</span></span>

<span data-ttu-id="a4c67-728">之所以进行此更改，是因为 EF Core 默认使用 `SQLitePCLRaw.bundle_e_sqlite3`，这意味着 FK 强制执行操作在默认情况下是打开的，并且不需要在每次打开连接时显式启用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-728">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="a4c67-729">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-729">**Mitigations**</span></span>

<span data-ttu-id="a4c67-730">默认情况下，SQLitePCLRaw.bundle_e_sqlite3 中启用了默认用于 EF Core 的外键。</span><span class="sxs-lookup"><span data-stu-id="a4c67-730">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="a4c67-731">对于其他情况，可以通过在连接字符串中指定 `Foreign Keys=True` 来启用外键。</span><span class="sxs-lookup"><span data-stu-id="a4c67-731">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="a4c67-732">Microsoft.EntityFrameworkCore.Sqlite 现在依赖于 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="a4c67-732">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="a4c67-733">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-733">**Old behavior**</span></span>

<span data-ttu-id="a4c67-734">在 EF Core 3.0 之前，EF Core 使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-734">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="a4c67-735">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-735">**New behavior**</span></span>

<span data-ttu-id="a4c67-736">从 EF Core 3.0 开始，EF Core 使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-736">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="a4c67-737">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-737">**Why**</span></span>

<span data-ttu-id="a4c67-738">此更改是为了使 iOS 上使用的 SQLite 版本与其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="a4c67-738">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="a4c67-739">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-739">**Mitigations**</span></span>

<span data-ttu-id="a4c67-740">若要在 iOS 上使用本机 SQLite 版本，请配置 `Microsoft.Data.Sqlite` 以使用其他 `SQLitePCLRaw` 捆绑包。</span><span class="sxs-lookup"><span data-stu-id="a4c67-740">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="a4c67-741">GUID 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="a4c67-741">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="a4c67-742">跟踪问题 #15078</span><span class="sxs-lookup"><span data-stu-id="a4c67-742">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="a4c67-743">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-743">**Old behavior**</span></span>

<span data-ttu-id="a4c67-744">GUID 值之前以 BLOB 值形式存储在 SQLite 上。</span><span class="sxs-lookup"><span data-stu-id="a4c67-744">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="a4c67-745">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-745">**New behavior**</span></span>

<span data-ttu-id="a4c67-746">Guid 值现在以文本形式存储。</span><span class="sxs-lookup"><span data-stu-id="a4c67-746">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="a4c67-747">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-747">**Why**</span></span>

<span data-ttu-id="a4c67-748">GUID 的二进制格式不会进行标准化。</span><span class="sxs-lookup"><span data-stu-id="a4c67-748">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="a4c67-749">以文本形式存储值使数据库与其他技术更兼容。</span><span class="sxs-lookup"><span data-stu-id="a4c67-749">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="a4c67-750">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-750">**Mitigations**</span></span>

<span data-ttu-id="a4c67-751">现在通过执行如下的 SQL，可以将现有数据库转成新的格式。</span><span class="sxs-lookup"><span data-stu-id="a4c67-751">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="a4c67-752">在 EF Core 中，还可以通过为这些属性配置值转换器，继续使用以前的行为模式。</span><span class="sxs-lookup"><span data-stu-id="a4c67-752">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="a4c67-753">Microsoft.Data.Sqlite 仍然能够从“BLOB”和“文本”列读取 GUID 值；但是，由于参数和常量的默认格式已更改，可能需要针对涉及 GUID 的大多数情况采取措施。</span><span class="sxs-lookup"><span data-stu-id="a4c67-753">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="a4c67-754">Char 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="a4c67-754">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="a4c67-755">跟踪问题 #15020</span><span class="sxs-lookup"><span data-stu-id="a4c67-755">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="a4c67-756">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-756">**Old behavior**</span></span>

<span data-ttu-id="a4c67-757">Char 值之前以整数值形式存储在 SQLite 上。</span><span class="sxs-lookup"><span data-stu-id="a4c67-757">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="a4c67-758">例如，A 的 char 值存储为整数值 65。</span><span class="sxs-lookup"><span data-stu-id="a4c67-758">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="a4c67-759">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-759">**New behavior**</span></span>

<span data-ttu-id="a4c67-760">Char 值现在以文本形式存储。</span><span class="sxs-lookup"><span data-stu-id="a4c67-760">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="a4c67-761">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-761">**Why**</span></span>

<span data-ttu-id="a4c67-762">以文本形式存储值显得更加自然，并且使数据库与其他技术更兼容。</span><span class="sxs-lookup"><span data-stu-id="a4c67-762">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="a4c67-763">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-763">**Mitigations**</span></span>

<span data-ttu-id="a4c67-764">现在通过执行如下的 SQL，可以将现有数据库转成新的格式。</span><span class="sxs-lookup"><span data-stu-id="a4c67-764">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="a4c67-765">在 EF Core 中，还可以通过为这些属性配置值转换器，继续使用以前的行为模式。</span><span class="sxs-lookup"><span data-stu-id="a4c67-765">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="a4c67-766">Microsoft.Data.Sqlite 也仍然能够读取整数列和文本列的字符值，因此某些情况可能不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="a4c67-766">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="a4c67-767">现在使用固定区域性的日历生成迁移 ID</span><span class="sxs-lookup"><span data-stu-id="a4c67-767">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="a4c67-768">跟踪问题 #12978</span><span class="sxs-lookup"><span data-stu-id="a4c67-768">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="a4c67-769">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-769">**Old behavior**</span></span>

<span data-ttu-id="a4c67-770">以前使用当前区域性的日历无意生成迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="a4c67-770">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="a4c67-771">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-771">**New behavior**</span></span>

<span data-ttu-id="a4c67-772">现在始终使用固定区域性的日历（公历）生成迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="a4c67-772">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="a4c67-773">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-773">**Why**</span></span>

<span data-ttu-id="a4c67-774">更新数据库或解决合并冲突时，迁移的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="a4c67-774">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="a4c67-775">使用固定日历可以避免因团队成员采用不同系统日历而产生的顺序问题。</span><span class="sxs-lookup"><span data-stu-id="a4c67-775">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="a4c67-776">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-776">**Mitigations**</span></span>

<span data-ttu-id="a4c67-777">如果有人使用年份时间大于公历日历的非公历日历（如泰国佛历），则会受到影响。</span><span class="sxs-lookup"><span data-stu-id="a4c67-777">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="a4c67-778">现有迁移 ID 需要进行更新，以便新迁移排在现有迁移之后。</span><span class="sxs-lookup"><span data-stu-id="a4c67-778">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="a4c67-779">在迁移的设计者文件的 Migration 属性中可以找到迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="a4c67-779">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="a4c67-780">迁移历史记录表还需要更新。</span><span class="sxs-lookup"><span data-stu-id="a4c67-780">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="a4c67-781">UseRowNumberForPaging 已删除</span><span class="sxs-lookup"><span data-stu-id="a4c67-781">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="a4c67-782">跟踪问题 #16400</span><span class="sxs-lookup"><span data-stu-id="a4c67-782">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="a4c67-783">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-783">**Old behavior**</span></span>

<span data-ttu-id="a4c67-784">在 EF Core 3.0 之前，`UseRowNumberForPaging` 可用于生成与 SQL Server 2008 兼容的分页 SQL。</span><span class="sxs-lookup"><span data-stu-id="a4c67-784">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="a4c67-785">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-785">**New behavior**</span></span>

<span data-ttu-id="a4c67-786">从 EF Core 3.0 开始，EF 将仅生成仅与更高的 SQL Server 版本兼容的分页 SQL。</span><span class="sxs-lookup"><span data-stu-id="a4c67-786">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

<span data-ttu-id="a4c67-787">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-787">**Why**</span></span>

<span data-ttu-id="a4c67-788">我们正在进行此更改，因为 [SQL Server 2008 不再是受支持的产品](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/)，并且更新此功能以使用在 EF Core 3.0 中做出的查询更改是一项重要工作。</span><span class="sxs-lookup"><span data-stu-id="a4c67-788">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="a4c67-789">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-789">**Mitigations**</span></span>

<span data-ttu-id="a4c67-790">建议更新到更高的 SQL Server 版本，或者使用更高的兼容性级别，以便支持生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="a4c67-790">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="a4c67-791">这就是说，如果无法执行此操作，请[在跟踪问题中做出详细注释](https://github.com/aspnet/EntityFrameworkCore/issues/16400)。</span><span class="sxs-lookup"><span data-stu-id="a4c67-791">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="a4c67-792">我们可能会根据反馈重新考虑这个决定。</span><span class="sxs-lookup"><span data-stu-id="a4c67-792">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="a4c67-793">已从 IDbContextOptionsExtension 中删除扩展信息/元数据</span><span class="sxs-lookup"><span data-stu-id="a4c67-793">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="a4c67-794">跟踪问题 #16119</span><span class="sxs-lookup"><span data-stu-id="a4c67-794">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="a4c67-795">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-795">**Old behavior**</span></span>

<span data-ttu-id="a4c67-796">`IDbContextOptionsExtension` 包含用于提供扩展元数据的方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-796">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="a4c67-797">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-797">**New behavior**</span></span>

<span data-ttu-id="a4c67-798">这些方法已迁移到从新 `IDbContextOptionsExtension.Info` 属性返回的新 `DbContextOptionsExtensionInfo` 抽象基类。</span><span class="sxs-lookup"><span data-stu-id="a4c67-798">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="a4c67-799">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-799">**Why**</span></span>

<span data-ttu-id="a4c67-800">在从版本 2.0 迁移到版本 3.0 的过程中，我们需要多次添加或更改这些方法。</span><span class="sxs-lookup"><span data-stu-id="a4c67-800">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="a4c67-801">将它们拆分成新抽象基类可以更轻松地进行此类更改，而不会破坏现有扩展。</span><span class="sxs-lookup"><span data-stu-id="a4c67-801">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="a4c67-802">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-802">**Mitigations**</span></span>

<span data-ttu-id="a4c67-803">将扩展更新为采用新模式。</span><span class="sxs-lookup"><span data-stu-id="a4c67-803">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="a4c67-804">例如，许多用于 EF Core 源代码中不同种类扩展的 `IDbContextOptionsExtension` 实现。</span><span class="sxs-lookup"><span data-stu-id="a4c67-804">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="a4c67-805">已重命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="a4c67-805">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="a4c67-806">跟踪问题 #10985</span><span class="sxs-lookup"><span data-stu-id="a4c67-806">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="a4c67-807">**更改**</span><span class="sxs-lookup"><span data-stu-id="a4c67-807">**Change**</span></span>

<span data-ttu-id="a4c67-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` 已重命名为 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="a4c67-809">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-809">**Why**</span></span>

<span data-ttu-id="a4c67-810">将此警告事件的命名方式与所有其他警告事件保持一致。</span><span class="sxs-lookup"><span data-stu-id="a4c67-810">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="a4c67-811">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-811">**Mitigations**</span></span>

<span data-ttu-id="a4c67-812">使用新名称。</span><span class="sxs-lookup"><span data-stu-id="a4c67-812">Use the new name.</span></span> <span data-ttu-id="a4c67-813">（注意：事件 ID 号码未更改。）</span><span class="sxs-lookup"><span data-stu-id="a4c67-813">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="a4c67-814">阐明 API 的外键约束名称</span><span class="sxs-lookup"><span data-stu-id="a4c67-814">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="a4c67-815">跟踪问题 #10730</span><span class="sxs-lookup"><span data-stu-id="a4c67-815">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="a4c67-816">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-816">**Old behavior**</span></span>

<span data-ttu-id="a4c67-817">在 EF Core 3.0 之前，外键约束名称被简单地称为“名称”。</span><span class="sxs-lookup"><span data-stu-id="a4c67-817">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="a4c67-818">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-818">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="a4c67-819">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-819">**New behavior**</span></span>

<span data-ttu-id="a4c67-820">从 EF Core 3.0 开始，外键约束名称现在被称为“约束名称”。</span><span class="sxs-lookup"><span data-stu-id="a4c67-820">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="a4c67-821">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-821">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="a4c67-822">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-822">**Why**</span></span>

<span data-ttu-id="a4c67-823">这样不仅可以让此领域的命名方式保持一致，还阐明了这是外键约束的名称，不是定义外键所依据的列或属性名称。</span><span class="sxs-lookup"><span data-stu-id="a4c67-823">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="a4c67-824">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-824">**Mitigations**</span></span>

<span data-ttu-id="a4c67-825">使用新名称。</span><span class="sxs-lookup"><span data-stu-id="a4c67-825">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="a4c67-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync 已公开</span><span class="sxs-lookup"><span data-stu-id="a4c67-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="a4c67-827">跟踪问题 #15997</span><span class="sxs-lookup"><span data-stu-id="a4c67-827">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="a4c67-828">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-828">**Old behavior**</span></span>

<span data-ttu-id="a4c67-829">在 EF Core 3.0 推出前，这些方法受保护。</span><span class="sxs-lookup"><span data-stu-id="a4c67-829">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="a4c67-830">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-830">**New behavior**</span></span>

<span data-ttu-id="a4c67-831">自 EF Core 3.0 起，这些方法是公共的。</span><span class="sxs-lookup"><span data-stu-id="a4c67-831">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="a4c67-832">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-832">**Why**</span></span>

<span data-ttu-id="a4c67-833">EF 使用这些方法来确定数据库是否已创建但为空。</span><span class="sxs-lookup"><span data-stu-id="a4c67-833">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="a4c67-834">这也适用于在 EF 外部确定是否要应用迁移。</span><span class="sxs-lookup"><span data-stu-id="a4c67-834">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="a4c67-835">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-835">**Mitigations**</span></span>

<span data-ttu-id="a4c67-836">更改任何重写的可访问性。</span><span class="sxs-lookup"><span data-stu-id="a4c67-836">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="a4c67-837">Microsoft.EntityFrameworkCore.Design 现在是 DevelopmentDependency 包</span><span class="sxs-lookup"><span data-stu-id="a4c67-837">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="a4c67-838">跟踪问题 #11506</span><span class="sxs-lookup"><span data-stu-id="a4c67-838">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="a4c67-839">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-839">**Old behavior**</span></span>

<span data-ttu-id="a4c67-840">在 EF Core 3.0 推出前，Microsoft.EntityFrameworkCore.Design 是常规 NuGet 包，它的程序集可以由依赖它的项目引用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-840">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="a4c67-841">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-841">**New behavior**</span></span>

<span data-ttu-id="a4c67-842">自 EF Core 3.0 起，它是 DevelopmentDependency 包。</span><span class="sxs-lookup"><span data-stu-id="a4c67-842">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="a4c67-843">这意味着，依赖项不会过渡流动到其他项目中，并且你也无法再默认引用它的程序集。</span><span class="sxs-lookup"><span data-stu-id="a4c67-843">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="a4c67-844">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-844">**Why**</span></span>

<span data-ttu-id="a4c67-845">此包仅用于设计时。</span><span class="sxs-lookup"><span data-stu-id="a4c67-845">This package is only intended to be used at design time.</span></span> <span data-ttu-id="a4c67-846">已部署的应用程序不得引用它。</span><span class="sxs-lookup"><span data-stu-id="a4c67-846">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="a4c67-847">让它成为 DevelopmentDependency 包强化了此建议。</span><span class="sxs-lookup"><span data-stu-id="a4c67-847">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="a4c67-848">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-848">**Mitigations**</span></span>

<span data-ttu-id="a4c67-849">如果需要引用此包来重写 EF Core 的设计时行为，则可更新项目中的 PackageReference 项元数据。</span><span class="sxs-lookup"><span data-stu-id="a4c67-849">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="a4c67-850">如果正在通过 Microsoft.EntityFrameworkCore.Tools 过渡引用此包，必须向此包添加显式 PackageReference，以更改它的元数据。</span><span class="sxs-lookup"><span data-stu-id="a4c67-850">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="a4c67-851">必须在需要此包中的类型的任何项目下添加此类显式引用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-851">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="a4c67-852">SQLitePCL.raw 已更新为版本 2.0.0</span><span class="sxs-lookup"><span data-stu-id="a4c67-852">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="a4c67-853">跟踪问题 #14824</span><span class="sxs-lookup"><span data-stu-id="a4c67-853">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="a4c67-854">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-854">**Old behavior**</span></span>

<span data-ttu-id="a4c67-855">Microsoft.EntityFrameworkCore.Sqlite 以前依赖 SQLitePCL.raw 版本 1.1.12。</span><span class="sxs-lookup"><span data-stu-id="a4c67-855">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="a4c67-856">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-856">**New behavior**</span></span>

<span data-ttu-id="a4c67-857">我们已将包更新为依赖版本 2.0.0。</span><span class="sxs-lookup"><span data-stu-id="a4c67-857">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="a4c67-858">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-858">**Why**</span></span>

<span data-ttu-id="a4c67-859">SQLitePCL.raw 版本 2.0.0 定目标到 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="a4c67-859">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="a4c67-860">它以前定目标到 .NET Standard 1.1，这就要求必须将可传递的包用作大型收尾，才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="a4c67-860">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="a4c67-861">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-861">**Mitigations**</span></span>

<span data-ttu-id="a4c67-862">SQLitePCL.raw 版本 2.0.0 包括一些重大变化。</span><span class="sxs-lookup"><span data-stu-id="a4c67-862">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="a4c67-863">有关详细信息，请参阅[发行说明](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md)。</span><span class="sxs-lookup"><span data-stu-id="a4c67-863">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="a4c67-864">NetTopologySuite 已更新为版本 2.0.0</span><span class="sxs-lookup"><span data-stu-id="a4c67-864">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="a4c67-865">跟踪问题 #14825</span><span class="sxs-lookup"><span data-stu-id="a4c67-865">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="a4c67-866">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-866">**Old behavior**</span></span>

<span data-ttu-id="a4c67-867">空间包以前依赖于 NetTopologySuite 的 1.15.1 版。</span><span class="sxs-lookup"><span data-stu-id="a4c67-867">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="a4c67-868">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-868">**New behavior**</span></span>

<span data-ttu-id="a4c67-869">我们已将包更新为依赖版本 2.0.0。</span><span class="sxs-lookup"><span data-stu-id="a4c67-869">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="a4c67-870">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-870">**Why**</span></span>

<span data-ttu-id="a4c67-871">NetTopologySuite 2.0.0 版旨在解决 EF Core 用户遇到的几个可用性问题。</span><span class="sxs-lookup"><span data-stu-id="a4c67-871">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="a4c67-872">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-872">**Mitigations**</span></span>

<span data-ttu-id="a4c67-873">NetTopologySuite 2.0.0 版包括一些重大更改。</span><span class="sxs-lookup"><span data-stu-id="a4c67-873">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="a4c67-874">有关详细信息，请参阅[发行说明](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001)。</span><span class="sxs-lookup"><span data-stu-id="a4c67-874">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="a4c67-875">使用 Microsoft.Data.SqlClient 而不是 System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="a4c67-875">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="a4c67-876">跟踪问题 #15636</span><span class="sxs-lookup"><span data-stu-id="a4c67-876">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="a4c67-877">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-877">**Old behavior**</span></span>

<span data-ttu-id="a4c67-878">Microsoft.EntityFrameworkCore.SqlServer 以前依赖 System.Data.SqlClient。</span><span class="sxs-lookup"><span data-stu-id="a4c67-878">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="a4c67-879">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-879">**New behavior**</span></span>

<span data-ttu-id="a4c67-880">我们已将包更新为依赖 Microsoft.Data.SqlClient。</span><span class="sxs-lookup"><span data-stu-id="a4c67-880">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="a4c67-881">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-881">**Why**</span></span>

<span data-ttu-id="a4c67-882">Microsoft.Data.SqlClient 是今后用于 SQL Server 的旗舰版数据访问驱动程序。而 System.Data.SqlClient 不再是开发的重点。</span><span class="sxs-lookup"><span data-stu-id="a4c67-882">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="a4c67-883">一些重要功能（例如 Always Encrypted）仅可在 Microsoft.Data.SqlClient 上使用。</span><span class="sxs-lookup"><span data-stu-id="a4c67-883">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="a4c67-884">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-884">**Mitigations**</span></span>

<span data-ttu-id="a4c67-885">如果你的代码直接依赖于 System.Data.SqlClient，则必须将其更改为 Microsoft.Data.SqlClient；由于这两个包与 API 的兼容性都非常高，因此这只是简单的包和命名空间更改。</span><span class="sxs-lookup"><span data-stu-id="a4c67-885">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="a4c67-886">必须配置多个不明确的自引用关系</span><span class="sxs-lookup"><span data-stu-id="a4c67-886">Multiple ambiguous self-referencing relationships must be configured</span></span>

[<span data-ttu-id="a4c67-887">跟踪问题 #13573</span><span class="sxs-lookup"><span data-stu-id="a4c67-887">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="a4c67-888">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-888">**Old behavior**</span></span>

<span data-ttu-id="a4c67-889">具有多个自引用单向导航属性和匹配的 FK 的实体类型被错误配置为单个关系。</span><span class="sxs-lookup"><span data-stu-id="a4c67-889">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="a4c67-890">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-890">For example:</span></span>

```csharp
public class User
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

<span data-ttu-id="a4c67-891">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-891">**New behavior**</span></span>

<span data-ttu-id="a4c67-892">现已在模型构建中检测到此场景，引发了一个指示模型不明确的异常。</span><span class="sxs-lookup"><span data-stu-id="a4c67-892">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="a4c67-893">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-893">**Why**</span></span>

<span data-ttu-id="a4c67-894">生成的模型是不明确的，在这种情况下可能会出现错误。</span><span class="sxs-lookup"><span data-stu-id="a4c67-894">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="a4c67-895">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-895">**Mitigations**</span></span>

<span data-ttu-id="a4c67-896">使用关系的完全配置。</span><span class="sxs-lookup"><span data-stu-id="a4c67-896">Use full configuration of the relationship.</span></span> <span data-ttu-id="a4c67-897">例如：</span><span class="sxs-lookup"><span data-stu-id="a4c67-897">For example:</span></span>

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();

 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="a4c67-898">DbFunction.Schema 为 null 或者空字符串将其配置为位于模型的默认架构中</span><span class="sxs-lookup"><span data-stu-id="a4c67-898">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="a4c67-899">跟踪问题 #12757</span><span class="sxs-lookup"><span data-stu-id="a4c67-899">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="a4c67-900">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-900">**Old behavior**</span></span>

<span data-ttu-id="a4c67-901">配置了实为空字符串的架构的 DbFunction 被视为不带架构的内置函数。</span><span class="sxs-lookup"><span data-stu-id="a4c67-901">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="a4c67-902">例如，下述代码会将 `DatePart` CLR 函数映射到 SqlServer 上的 `DATEPART` 内置函数。</span><span class="sxs-lookup"><span data-stu-id="a4c67-902">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="a4c67-903">**新行为**</span><span class="sxs-lookup"><span data-stu-id="a4c67-903">**New behavior**</span></span>

<span data-ttu-id="a4c67-904">所有 DbFunction 映射都被视为映射到用户定义的函数。</span><span class="sxs-lookup"><span data-stu-id="a4c67-904">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="a4c67-905">因此，空字符串值会将函数置于模型的默认架构中。</span><span class="sxs-lookup"><span data-stu-id="a4c67-905">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="a4c67-906">这可能是通过 Fluent API `modelBuilder.HasDefaultSchema()` 显式配置的架构，否则为 `dbo`。</span><span class="sxs-lookup"><span data-stu-id="a4c67-906">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="a4c67-907">**为什么**</span><span class="sxs-lookup"><span data-stu-id="a4c67-907">**Why**</span></span>

<span data-ttu-id="a4c67-908">如果之前的架构为空，可以此将函数视为内置项，但此逻辑仅适用于内置函数不属于任何架构的 SqlServer。</span><span class="sxs-lookup"><span data-stu-id="a4c67-908">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="a4c67-909">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="a4c67-909">**Mitigations**</span></span>

<span data-ttu-id="a4c67-910">手动配置 DbFunction 的转换，以将其映射到内置函数中。</span><span class="sxs-lookup"><span data-stu-id="a4c67-910">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
