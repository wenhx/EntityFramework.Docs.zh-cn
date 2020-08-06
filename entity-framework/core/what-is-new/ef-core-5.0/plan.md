---
title: 针对 Entity Framework Core 5.0 的计划
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 4abb6f500dce320dd0c32f8f3bf5c529b59fb28b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526883"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="b54cd-102">针对 Entity Framework Core 5.0 的计划</span><span class="sxs-lookup"><span data-stu-id="b54cd-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="b54cd-103">如[计划过程](xref:core/what-is-new/release_planning)中所述，我们已来自利益干系人的输入收集到针对 EF Core 5.0 版的暂定计划中。</span><span class="sxs-lookup"><span data-stu-id="b54cd-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b54cd-104">此计划仍是半成品。</span><span class="sxs-lookup"><span data-stu-id="b54cd-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="b54cd-105">这里不进行任何承诺。</span><span class="sxs-lookup"><span data-stu-id="b54cd-105">Nothing here is a commitment.</span></span> <span data-ttu-id="b54cd-106">此计划是一个起点，会随着我们了解更多信息而发展。</span><span class="sxs-lookup"><span data-stu-id="b54cd-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="b54cd-107">当前未针对 5.0 进行计划的某些内容可能会被纳入。</span><span class="sxs-lookup"><span data-stu-id="b54cd-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="b54cd-108">当前已针对 5.0 进行计划的某些内容可能会被淘汰。</span><span class="sxs-lookup"><span data-stu-id="b54cd-108">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="b54cd-109">常规信息</span><span class="sxs-lookup"><span data-stu-id="b54cd-109">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="b54cd-110">版本号和发布日期</span><span class="sxs-lookup"><span data-stu-id="b54cd-110">Version number and release date</span></span>

<span data-ttu-id="b54cd-111">EF Core 5.0 当前计划[与 .NET 5.0 同时](https://devblogs.microsoft.com/dotnet/introducing-net-5/)发布。</span><span class="sxs-lookup"><span data-stu-id="b54cd-111">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="b54cd-112">已选择版本“5.0”，以便与 .NET 5.0 保持一致。</span><span class="sxs-lookup"><span data-stu-id="b54cd-112">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="b54cd-113">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="b54cd-113">Supported platforms</span></span>

<span data-ttu-id="b54cd-114">EF Core 5.0 计划可在任何 .NET 5.0 平台上运行（基于[这些平台与 .NET Core 的融合](https://devblogs.microsoft.com/dotnet/introducing-net-5/)）。</span><span class="sxs-lookup"><span data-stu-id="b54cd-114">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="b54cd-115">就 .NET Standard 和使用的实际 TFM 而言，这意味着仍然是 TBD。</span><span class="sxs-lookup"><span data-stu-id="b54cd-115">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="b54cd-116">EF Core 5.0 不会在 .NET Framework 上运行。</span><span class="sxs-lookup"><span data-stu-id="b54cd-116">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="b54cd-117">重大更改</span><span class="sxs-lookup"><span data-stu-id="b54cd-117">Breaking changes</span></span>

<span data-ttu-id="b54cd-118">EF Core 5.0 将包含一些[中断性变更](xref:core/what-is-new/ef-core-5.0/breaking-changes)，但与 EF Core 3.0 的情况相比，这些更改的严重性要小得多。</span><span class="sxs-lookup"><span data-stu-id="b54cd-118">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="b54cd-119">我们的目标是允许大多数应用程序进行更新而不会中断。</span><span class="sxs-lookup"><span data-stu-id="b54cd-119">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="b54cd-120">预计会对数据库提供程序进行一些重大更改，尤其是在 TPT 支持方面。</span><span class="sxs-lookup"><span data-stu-id="b54cd-120">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="b54cd-121">但是，我们预计为 5.0 更新提供程序的工作会少于为 3.0 更新所需的工作。</span><span class="sxs-lookup"><span data-stu-id="b54cd-121">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="b54cd-122">主题</span><span class="sxs-lookup"><span data-stu-id="b54cd-122">Themes</span></span>

<span data-ttu-id="b54cd-123">我们提取了几个主要领域或主题，它们将构成对 EF Core 5.0 进行大量投入的基础。</span><span class="sxs-lookup"><span data-stu-id="b54cd-123">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="b54cd-124">以完全透明的方式按约定进行多对多映射</span><span class="sxs-lookup"><span data-stu-id="b54cd-124">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="b54cd-125">主要开发人员：@smitpatel、@AndriySvyryd 和 @lajones</span><span class="sxs-lookup"><span data-stu-id="b54cd-125">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="b54cd-126">通过 [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-126">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="b54cd-127">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-127">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-128">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-128">Status: In-progress</span></span>

<span data-ttu-id="b54cd-129">多对多是 GitHub 积压工作 (backlog) 中[请求最多的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)（大约 506 张投票）。</span><span class="sxs-lookup"><span data-stu-id="b54cd-129">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="b54cd-130">多对多关系的支持分为三个主要方面：</span><span class="sxs-lookup"><span data-stu-id="b54cd-130">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="b54cd-131">跳过导航属性 - 将在下个主题中讲解它们。</span><span class="sxs-lookup"><span data-stu-id="b54cd-131">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="b54cd-132">属性包实体类型。</span><span class="sxs-lookup"><span data-stu-id="b54cd-132">Property-bag entity types.</span></span> <span data-ttu-id="b54cd-133">这些类型使标准 CLR 类型（例如 `Dictionary`）可以用于实体实例，使得每种实体类型都不需要显式 CLR 类型。</span><span class="sxs-lookup"><span data-stu-id="b54cd-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="b54cd-134">通过 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b54cd-134">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="b54cd-135">Sugar 可用于轻松配置多对多关系。</span><span class="sxs-lookup"><span data-stu-id="b54cd-135">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="b54cd-136">除了支持跳过导航，我们现还将多对多关系的这些其他方面引入到 EF Core 5.0 来提供完整的体验。</span><span class="sxs-lookup"><span data-stu-id="b54cd-136">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="b54cd-137">多对多导航属性（即“跳过导航”）</span><span class="sxs-lookup"><span data-stu-id="b54cd-137">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="b54cd-138">开发人员负责人：@smitpatel 和 @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="b54cd-138">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="b54cd-139">通过 [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-139">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="b54cd-140">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-140">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-141">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-141">Status: In-progress</span></span>

<span data-ttu-id="b54cd-142">如第一个主题中所述，多对多支持具有多个方面。</span><span class="sxs-lookup"><span data-stu-id="b54cd-142">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="b54cd-143">该主题专门跟踪对跳过导航功能的使用。</span><span class="sxs-lookup"><span data-stu-id="b54cd-143">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="b54cd-144">我们认为，对于需要多对多支持的人员而言，最重要的阻止因素是无法在业务逻辑（如查询）中使用“自然”关系，而无需引用联接表。</span><span class="sxs-lookup"><span data-stu-id="b54cd-144">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="b54cd-145">联接表实体类型可能仍然存在，但不应妨碍业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="b54cd-145">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="b54cd-146">每个类型一张表 (TPT) 继承映射</span><span class="sxs-lookup"><span data-stu-id="b54cd-146">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="b54cd-147">开发人员负责人：@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="b54cd-147">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="b54cd-148">通过 [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-148">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="b54cd-149">T 恤大小：XL</span><span class="sxs-lookup"><span data-stu-id="b54cd-149">T-shirt size: XL</span></span>

<span data-ttu-id="b54cd-150">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-150">Status: In-progress</span></span>

<span data-ttu-id="b54cd-151">我们要实现 TPT 是因为它是经常请求的功能（大约 289 张投票；第三名），并且它需要一些低级更改，我们认为这些更改适合于总体 .NET 5 计划的基础性质。</span><span class="sxs-lookup"><span data-stu-id="b54cd-151">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="b54cd-152">我们预计这会形成数据库提供程序的重大更改，但与 3.0 所需的更改相比，这些更改的严重性要小得多。</span><span class="sxs-lookup"><span data-stu-id="b54cd-152">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="b54cd-153">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="b54cd-153">Filtered Include</span></span>

<span data-ttu-id="b54cd-154">开发人员负责人：@maumar</span><span class="sxs-lookup"><span data-stu-id="b54cd-154">Lead developer: @maumar</span></span>

<span data-ttu-id="b54cd-155">通过 [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-155">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="b54cd-156">T 恤大小：M</span><span class="sxs-lookup"><span data-stu-id="b54cd-156">T-shirt size: M</span></span>

<span data-ttu-id="b54cd-157">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-157">Status: In-progress</span></span>

<span data-ttu-id="b54cd-158">经过筛选的包含是经常请求的功能（大约 376 张投票；第二名），其工作量不大，我们认为这会使当前需要模型级筛选器或更复杂查询的许多方案不受阻碍或更加容易。</span><span class="sxs-lookup"><span data-stu-id="b54cd-158">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="b54cd-159">拆分 Include</span><span class="sxs-lookup"><span data-stu-id="b54cd-159">Split Include</span></span>

<span data-ttu-id="b54cd-160">开发人员负责人：@smitpatel</span><span class="sxs-lookup"><span data-stu-id="b54cd-160">Lead developer: @smitpatel</span></span>

<span data-ttu-id="b54cd-161">通过 [#20892](https://github.com/dotnet/efcore/issues/20892) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-161">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="b54cd-162">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-162">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-163">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-163">Status: In-progress</span></span>

<span data-ttu-id="b54cd-164">EF Core 3.0 更改了默认行为，它为给定的 LINQ 查询创建一个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="b54cd-164">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="b54cd-165">对于使用 Include 来处理多个集合的查询来说，这导致性能大幅下降。</span><span class="sxs-lookup"><span data-stu-id="b54cd-165">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="b54cd-166">在 EF Core 5.0 中，我们将保留新的默认行为。</span><span class="sxs-lookup"><span data-stu-id="b54cd-166">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="b54cd-167">不过，EF Core 5.0 现在允许为集合 Includes 生成多个查询，因为使用单个查询会导致性能不佳。</span><span class="sxs-lookup"><span data-stu-id="b54cd-167">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="b54cd-168">合理化 ToTable、ToQuery、ToView、FromSql 等</span><span class="sxs-lookup"><span data-stu-id="b54cd-168">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="b54cd-169">开发人员负责人：@maumar 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="b54cd-169">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="b54cd-170">通过 [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-170">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="b54cd-171">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-171">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-172">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-172">Status: In-progress</span></span>

<span data-ttu-id="b54cd-173">在以前的版本中，我们在支持原始 SQL、无键类型和相关领域方面取得了进展。</span><span class="sxs-lookup"><span data-stu-id="b54cd-173">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="b54cd-174">但是，在所有内容作为一个整体协同工作的方式上存在差距和不一致。</span><span class="sxs-lookup"><span data-stu-id="b54cd-174">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="b54cd-175">5\.0 的目标是修复这些问题，并为定义、迁移和使用不同类型的实体及其关联查询和数据库项目创造良好体验。</span><span class="sxs-lookup"><span data-stu-id="b54cd-175">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="b54cd-176">这也可能涉及到已编译查询 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="b54cd-176">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="b54cd-177">请注意，此项可能会导致某些应用程序级重大更改，因为我们当前拥有的某些功能过于宽松，以至于它可能很快导致人们陷入困境。</span><span class="sxs-lookup"><span data-stu-id="b54cd-177">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="b54cd-178">我们可能最终会阻止其中一些功能，并改为提供有关应执行的操作的指导。</span><span class="sxs-lookup"><span data-stu-id="b54cd-178">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="b54cd-179">常规查询增强功能</span><span class="sxs-lookup"><span data-stu-id="b54cd-179">General query enhancements</span></span>

<span data-ttu-id="b54cd-180">开发人员负责人：@smitpatel 和 @maumar</span><span class="sxs-lookup"><span data-stu-id="b54cd-180">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="b54cd-181">通过 [5.0 里程碑中使用 `area-query` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-181">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="b54cd-182">T 恤大小：XL</span><span class="sxs-lookup"><span data-stu-id="b54cd-182">T-shirt size: XL</span></span>

<span data-ttu-id="b54cd-183">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-183">Status: In-progress</span></span>

<span data-ttu-id="b54cd-184">查询转换代码已针对 EF Core 3.0 进行了广泛重写。</span><span class="sxs-lookup"><span data-stu-id="b54cd-184">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="b54cd-185">因此，查询代码一般处于更可靠的状态。</span><span class="sxs-lookup"><span data-stu-id="b54cd-185">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="b54cd-186">对于 5.0，在支持 TPT 和 skip 导航属性所需的更改范围之外，我们未计划进行重大查询更改。</span><span class="sxs-lookup"><span data-stu-id="b54cd-186">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="b54cd-187">但是，仍然需要大量工作来修复 3.0 全面修改中遗留的一些技术债务。</span><span class="sxs-lookup"><span data-stu-id="b54cd-187">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="b54cd-188">我们还计划修复许多 bug 并实现少量的增强功能，以进一步改进总体查询体验。</span><span class="sxs-lookup"><span data-stu-id="b54cd-188">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="b54cd-189">迁移和部署体验</span><span class="sxs-lookup"><span data-stu-id="b54cd-189">Migrations and deployment experience</span></span>

<span data-ttu-id="b54cd-190">开发人员负责人：@bricelam</span><span class="sxs-lookup"><span data-stu-id="b54cd-190">Lead developers: @bricelam</span></span>

<span data-ttu-id="b54cd-191">通过 [#19587](https://github.com/dotnet/efcore/issues/19587) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-191">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="b54cd-192">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-192">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-193">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-193">Status: In-progress</span></span>

<span data-ttu-id="b54cd-194">当前，许多开发人员在应用程序启动时迁移其数据库。</span><span class="sxs-lookup"><span data-stu-id="b54cd-194">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="b54cd-195">这十分简单，但不建议这样做，因为：</span><span class="sxs-lookup"><span data-stu-id="b54cd-195">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="b54cd-196">多个线程/进程/服务器可能会并发尝试迁移数据库</span><span class="sxs-lookup"><span data-stu-id="b54cd-196">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="b54cd-197">发生这种情况时，应用程序可能会尝试访问不一致的状态</span><span class="sxs-lookup"><span data-stu-id="b54cd-197">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="b54cd-198">通常，不应为应用程序执行授予修改架构的数据库权限</span><span class="sxs-lookup"><span data-stu-id="b54cd-198">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="b54cd-199">如果出现问题，则难以还原为干净状态</span><span class="sxs-lookup"><span data-stu-id="b54cd-199">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="b54cd-200">我们希望在这里提供更好的体验，从而可以轻松地在部署时迁移数据库。</span><span class="sxs-lookup"><span data-stu-id="b54cd-200">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="b54cd-201">这应该：</span><span class="sxs-lookup"><span data-stu-id="b54cd-201">This should:</span></span>

* <span data-ttu-id="b54cd-202">可在 Linux、Mac 和 Windows 上正常工作</span><span class="sxs-lookup"><span data-stu-id="b54cd-202">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="b54cd-203">在命令行上具有良好体验</span><span class="sxs-lookup"><span data-stu-id="b54cd-203">Be a good experience on the command line</span></span>
* <span data-ttu-id="b54cd-204">支持采用容器的方案</span><span class="sxs-lookup"><span data-stu-id="b54cd-204">Support scenarios with containers</span></span>
* <span data-ttu-id="b54cd-205">适用于常用的实际部署工具/流</span><span class="sxs-lookup"><span data-stu-id="b54cd-205">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="b54cd-206">至少集成到 Visual Studio 中</span><span class="sxs-lookup"><span data-stu-id="b54cd-206">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="b54cd-207">结果可能是在 EF Core 中进行许多小改进（例如，SQLite 上更好的迁移），并与其他团队一起进行指导和长期协作，以改进不仅限于 EF 的端到端体验。</span><span class="sxs-lookup"><span data-stu-id="b54cd-207">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="b54cd-208">EF Core 平台体验</span><span class="sxs-lookup"><span data-stu-id="b54cd-208">EF Core platforms experience</span></span>

<span data-ttu-id="b54cd-209">开发人员负责人：@roji 和 @bricelam</span><span class="sxs-lookup"><span data-stu-id="b54cd-209">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="b54cd-210">通过 [#19588](https://github.com/dotnet/efcore/issues/19588) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-210">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="b54cd-211">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-211">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-212">状态：尚未开始</span><span class="sxs-lookup"><span data-stu-id="b54cd-212">Status: Not started</span></span>

<span data-ttu-id="b54cd-213">我们提供了有关在类似传统 MVC 的 Web 应用程序中使用 EF Core 的良好指导。</span><span class="sxs-lookup"><span data-stu-id="b54cd-213">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="b54cd-214">适用于其他平台和应用程序模型的指导缺失或过期。</span><span class="sxs-lookup"><span data-stu-id="b54cd-214">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="b54cd-215">对于 EF Core 5.0，我们计划调查、改进和记录在以下方面使用 EF Core 的体验：</span><span class="sxs-lookup"><span data-stu-id="b54cd-215">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="b54cd-216">Blazor</span><span class="sxs-lookup"><span data-stu-id="b54cd-216">Blazor</span></span>
* <span data-ttu-id="b54cd-217">Xamarin，包括使用 AOT/链接器情景</span><span class="sxs-lookup"><span data-stu-id="b54cd-217">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="b54cd-218">WinForms/WPF/WinUI，可能还有其他 U.I.</span><span class="sxs-lookup"><span data-stu-id="b54cd-218">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="b54cd-219">框架</span><span class="sxs-lookup"><span data-stu-id="b54cd-219">frameworks</span></span>

<span data-ttu-id="b54cd-220">这可能是在 EF Core 中进行许多小改进，并与其他团队一起进行指导和长期协作，以改进不仅限于 EF 的端到端体验。</span><span class="sxs-lookup"><span data-stu-id="b54cd-220">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="b54cd-221">我们计划研究的特定领域有：</span><span class="sxs-lookup"><span data-stu-id="b54cd-221">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="b54cd-222">部署，包括使用 EF 工具（例如用于迁移）的体验</span><span class="sxs-lookup"><span data-stu-id="b54cd-222">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="b54cd-223">应用程序模型（包括 Xamarin 和 Blazor），可能还有其他模型</span><span class="sxs-lookup"><span data-stu-id="b54cd-223">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="b54cd-224">SQLite 体验，包括空间体验和表重新生成</span><span class="sxs-lookup"><span data-stu-id="b54cd-224">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="b54cd-225">AOT 和链接体验</span><span class="sxs-lookup"><span data-stu-id="b54cd-225">AOT and linking experiences</span></span>
* <span data-ttu-id="b54cd-226">诊断集成，包括性能计数器</span><span class="sxs-lookup"><span data-stu-id="b54cd-226">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="b54cd-227">性能</span><span class="sxs-lookup"><span data-stu-id="b54cd-227">Performance</span></span>

<span data-ttu-id="b54cd-228">开发人员负责人：@roji</span><span class="sxs-lookup"><span data-stu-id="b54cd-228">Lead developer: @roji</span></span>

<span data-ttu-id="b54cd-229">通过 [5.0 里程碑中使用 `area-perf` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-229">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="b54cd-230">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-230">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-231">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-231">Status: In-progress</span></span>

<span data-ttu-id="b54cd-232">对于 EF Core，我们计划改进性能基准套件，并对运行时进行定向性能改进。</span><span class="sxs-lookup"><span data-stu-id="b54cd-232">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="b54cd-233">此外，我们计划完成在 3.0 发布周期内进行原型设计的新 ADO.NET 批处理 API。</span><span class="sxs-lookup"><span data-stu-id="b54cd-233">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="b54cd-234">同样在 ADO.NET 层，我们计划对 Npgsql 提供程序进行其他性能改进。</span><span class="sxs-lookup"><span data-stu-id="b54cd-234">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="b54cd-235">作为此工作的一部分，我们还计划根据需要添加 ADO.NET/EF Core 性能计数器和其他诊断。</span><span class="sxs-lookup"><span data-stu-id="b54cd-235">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="b54cd-236">体系结构/参与者文档</span><span class="sxs-lookup"><span data-stu-id="b54cd-236">Architectural/contributor documentation</span></span>

<span data-ttu-id="b54cd-237">文档管理人员主管：@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="b54cd-237">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="b54cd-238">通过 [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-238">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="b54cd-239">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-239">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-240">状态：剪切</span><span class="sxs-lookup"><span data-stu-id="b54cd-240">Status: Cut</span></span>

<span data-ttu-id="b54cd-241">这里的思路是使人们更容易了解 EF Core 的内部情况。</span><span class="sxs-lookup"><span data-stu-id="b54cd-241">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="b54cd-242">这可能对于使用 EF Core 的任何人都十分有用，但主要动机是使外部人员更容易：</span><span class="sxs-lookup"><span data-stu-id="b54cd-242">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="b54cd-243">为 EF Core 代码做出贡献</span><span class="sxs-lookup"><span data-stu-id="b54cd-243">Contribute to the EF Core code</span></span>
* <span data-ttu-id="b54cd-244">创建数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="b54cd-244">Create database providers</span></span>
* <span data-ttu-id="b54cd-245">构建其他扩展</span><span class="sxs-lookup"><span data-stu-id="b54cd-245">Build other extensions</span></span>

<span data-ttu-id="b54cd-246">更新：遗憾的是，该计划过于宏大。</span><span class="sxs-lookup"><span data-stu-id="b54cd-246">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="b54cd-247">我们仍相信这很重要，但遗憾的是，EF Core 5.0 未包含它。</span><span class="sxs-lookup"><span data-stu-id="b54cd-247">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="b54cd-248">Microsoft.Data.Sqlite 文档</span><span class="sxs-lookup"><span data-stu-id="b54cd-248">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="b54cd-249">文档管理人员主管：@bricelam</span><span class="sxs-lookup"><span data-stu-id="b54cd-249">Lead documenter: @bricelam</span></span>

<span data-ttu-id="b54cd-250">通过 [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-250">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="b54cd-251">T 恤大小：M</span><span class="sxs-lookup"><span data-stu-id="b54cd-251">T-shirt size: M</span></span>

<span data-ttu-id="b54cd-252">状态：已完成。</span><span class="sxs-lookup"><span data-stu-id="b54cd-252">Status: Completed.</span></span> <span data-ttu-id="b54cd-253">新文档在 [Microsoft 文档站点上提供](/dotnet/standard/data/sqlite/?tabs=netcore-cli)。</span><span class="sxs-lookup"><span data-stu-id="b54cd-253">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="b54cd-254">EF 团队还拥有 Microsoft.Data.Sqlite ADO.NET 提供程序。</span><span class="sxs-lookup"><span data-stu-id="b54cd-254">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="b54cd-255">我们计划在 5.0 版本中完整记录此提供程序。</span><span class="sxs-lookup"><span data-stu-id="b54cd-255">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="b54cd-256">常规文档</span><span class="sxs-lookup"><span data-stu-id="b54cd-256">General documentation</span></span>

<span data-ttu-id="b54cd-257">文档管理人员主管：@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="b54cd-257">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="b54cd-258">通过 [5.0 里程碑的文档存储库中的问题](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-258">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="b54cd-259">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-259">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-260">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-260">Status: In-progress</span></span>

<span data-ttu-id="b54cd-261">我们已在针对 3.0 和 3.1 版本更新文档。</span><span class="sxs-lookup"><span data-stu-id="b54cd-261">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="b54cd-262">我们还在致力于：</span><span class="sxs-lookup"><span data-stu-id="b54cd-262">We are also working on:</span></span>

* <span data-ttu-id="b54cd-263">全面修改入门文档，使它们更易于理解/更易于遵循</span><span class="sxs-lookup"><span data-stu-id="b54cd-263">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="b54cd-264">重新组织文档，使内容更易于查找并添加交叉引用</span><span class="sxs-lookup"><span data-stu-id="b54cd-264">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="b54cd-265">向现有文档添加更多详细信息和说明</span><span class="sxs-lookup"><span data-stu-id="b54cd-265">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="b54cd-266">更新示例并添加更多示例</span><span class="sxs-lookup"><span data-stu-id="b54cd-266">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="b54cd-267">修复 bug</span><span class="sxs-lookup"><span data-stu-id="b54cd-267">Fixing bugs</span></span>

<span data-ttu-id="b54cd-268">通过 [5.0 里程碑中使用 `type-bug` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-268">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="b54cd-269">开发人员：@roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="b54cd-269">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="b54cd-270">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-270">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-271">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-271">Status: In-progress</span></span>

<span data-ttu-id="b54cd-272">撰写本文时，我们已将 135 个 bug 会审为在 5.0 版本中进行修复（已修复了 62 个），但与上面的常规查询增强功能部分存在很大的重叠。</span><span class="sxs-lookup"><span data-stu-id="b54cd-272">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="b54cd-273">在 3.0 版本期间，传入率（最终成为里程碑工作的问题）大约为每月 23 个问题。</span><span class="sxs-lookup"><span data-stu-id="b54cd-273">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="b54cd-274">并非所有这些问题都需要在 5.0 中进行修复。</span><span class="sxs-lookup"><span data-stu-id="b54cd-274">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="b54cd-275">作为大致估计，我们计划修复在 5.0 时间范围内修复其他 150 个问题。</span><span class="sxs-lookup"><span data-stu-id="b54cd-275">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="b54cd-276">小型增强功能</span><span class="sxs-lookup"><span data-stu-id="b54cd-276">Small enhancements</span></span>

<span data-ttu-id="b54cd-277">通过 [5.0 里程碑中使用 `type-enhancement` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-277">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="b54cd-278">开发人员：@roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="b54cd-278">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="b54cd-279">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="b54cd-279">T-shirt size: L</span></span>

<span data-ttu-id="b54cd-280">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="b54cd-280">Status: In-progress</span></span>

<span data-ttu-id="b54cd-281">除了上面概述的较大功能之外，我们还计划对 5.0 进行许多较小的改进，以修复“小问题”。</span><span class="sxs-lookup"><span data-stu-id="b54cd-281">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="b54cd-282">请注意，上面概述的更一般主题也涵盖了其中许多增强功能。</span><span class="sxs-lookup"><span data-stu-id="b54cd-282">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="b54cd-283">非计划</span><span class="sxs-lookup"><span data-stu-id="b54cd-283">Below-the-line</span></span>

<span data-ttu-id="b54cd-284">通过[使用 `consider-for-next-release` 标记的问题](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="b54cd-284">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="b54cd-285">这些是当前未针对 5.0 版本计划的 bug 修复和增强功能，但我们将根据以上工作的进度将它们视为延伸目标。</span><span class="sxs-lookup"><span data-stu-id="b54cd-285">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="b54cd-286">此外，我们始终会在计划时考虑[投票最多的问题](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="b54cd-286">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="b54cd-287">从版本中去除其中任何问题总是很痛苦的，但是我们确实需要针对所拥有的资源制定切合实际的计划。</span><span class="sxs-lookup"><span data-stu-id="b54cd-287">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="b54cd-288">建议</span><span class="sxs-lookup"><span data-stu-id="b54cd-288">Suggestions</span></span>

<span data-ttu-id="b54cd-289">你对计划的反馈非常重要。</span><span class="sxs-lookup"><span data-stu-id="b54cd-289">Your feedback on planning is important.</span></span> <span data-ttu-id="b54cd-290">指示问题重要性的最佳方式是在 GitHub 上为该问题投票（竖起大拇指）。</span><span class="sxs-lookup"><span data-stu-id="b54cd-290">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="b54cd-291">然后，此数据将进入下一个版本的[计划过程](xref:core/what-is-new/release_planning)。</span><span class="sxs-lookup"><span data-stu-id="b54cd-291">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
