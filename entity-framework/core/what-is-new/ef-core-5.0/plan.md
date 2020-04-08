---
title: 针对 Entity Framework Core 5.0 的计划
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/ef-core-5.0/plan.md
ms.openlocfilehash: 8b4ca32524869019c04d5a4d4d55967f68181cd7
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136224"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="8e1c4-102">针对 Entity Framework Core 5.0 的计划</span><span class="sxs-lookup"><span data-stu-id="8e1c4-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="8e1c4-103">如[计划过程](../release-planning.md)中所述，我们已来自利益干系人的输入收集到针对 EF Core 5.0 版的暂定计划中。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-103">As described in the [planning process](../release-planning.md), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="8e1c4-104">此计划仍是半成品。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="8e1c4-105">这里不进行任何承诺。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-105">Nothing here is a commitment.</span></span> <span data-ttu-id="8e1c4-106">此计划是一个起点，会随着我们了解更多信息而发展。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="8e1c4-107">当前未针对 5.0 进行计划的某些内容可能会被纳入。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="8e1c4-108">当前已针对 5.0 进行计划的某些内容可能会被淘汰。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-108">Some things currently planned for 5.0 may get punted out.</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="8e1c4-109">版本号和发布日期。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-109">Version number and release date.</span></span>

<span data-ttu-id="8e1c4-110">EF Core 5.0 当前计划[与 .NET 5.0 同时](https://devblogs.microsoft.com/dotnet/introducing-net-5/)发布。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-110">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="8e1c4-111">已选择版本“5.0”，以便与 .NET 5.0 保持一致。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-111">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="8e1c4-112">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="8e1c4-112">Supported platforms</span></span>

<span data-ttu-id="8e1c4-113">EF Core 5.0 计划可在任何 .NET 5.0 平台上运行（基于[这些平台与 .NET Core 的融合](https://devblogs.microsoft.com/dotnet/introducing-net-5/)）。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-113">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="8e1c4-114">就 .NET Standard 和使用的实际 TFM 而言，这意味着仍然是 TBD。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-114">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="8e1c4-115">EF Core 5.0 不会在 .NET Framework 上运行。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-115">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="8e1c4-116">重大更改</span><span class="sxs-lookup"><span data-stu-id="8e1c4-116">Breaking changes</span></span>

<span data-ttu-id="8e1c4-117">EF Core 5.0 将包含一些重大更改，但与 EF Core 3.0 的情况相比，这些更改的严重性要小得多。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-117">EF Core 5.0 will contain some breaking changes, but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="8e1c4-118">我们的目标是允许大多数应用程序进行更新而不会中断。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-118">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="8e1c4-119">预计会对数据库提供程序进行一些重大更改，尤其是在 TPT 支持方面。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-119">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="8e1c4-120">但是，我们预计为 5.0 更新提供程序的工作会少于为 3.0 更新所需的工作。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-120">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="8e1c4-121">主题</span><span class="sxs-lookup"><span data-stu-id="8e1c4-121">Themes</span></span>

<span data-ttu-id="8e1c4-122">我们提取了几个主要领域或主题，它们将构成对 EF Core 5.0 进行大量投入的基础。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-122">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="8e1c4-123">多对多导航属性（即“跳过导航”）</span><span class="sxs-lookup"><span data-stu-id="8e1c4-123">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="8e1c4-124">开发人员负责人：@smitpatel 和 @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="8e1c4-124">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="8e1c4-125">通过 [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-125">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="8e1c4-126">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-126">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-127">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-127">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-128">多对多是 GitHub 积压工作 (backlog) 中[请求最多的功能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)（大约 407 张投票）。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-128">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~407 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="8e1c4-129">对所有多对多关系的支持通过 [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-129">Support for many-to-many relationships in their entirety is tracked as [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508).</span></span> <span data-ttu-id="8e1c4-130">这可以划分为三个主要区域：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-130">This can be broken down into three major areas:</span></span>

* <span data-ttu-id="8e1c4-131">跳过导航属性。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-131">Skip navigation properties.</span></span> <span data-ttu-id="8e1c4-132">这些属性使模型可以用于查询等，而无需引用基础联接表实体。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-132">These allow the model to be used for queries, etc. without reference to the underlying join table entity.</span></span> <span data-ttu-id="8e1c4-133">([#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003))</span><span class="sxs-lookup"><span data-stu-id="8e1c4-133">([#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003))</span></span>
* <span data-ttu-id="8e1c4-134">属性包实体类型。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-134">Property-bag entity types.</span></span> <span data-ttu-id="8e1c4-135">这些类型使标准 CLR 类型（例如 `Dictionary`）可以用于实体实例，使得每种实体类型都不需要显式 CLR 类型。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-135">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="8e1c4-136">（5.0 版的延伸目标：[#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)。）</span><span class="sxs-lookup"><span data-stu-id="8e1c4-136">(Stretch for 5.0: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).)</span></span>
* <span data-ttu-id="8e1c4-137">Sugar 可用于轻松配置多对多关系。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-137">Sugar for easy configuration of many-to-many relationships.</span></span> <span data-ttu-id="8e1c4-138">（5.0 版的延伸目标。）</span><span class="sxs-lookup"><span data-stu-id="8e1c4-138">(Stretch for 5.0.)</span></span>

<span data-ttu-id="8e1c4-139">我们认为，对于需要多对多支持的人员而言，最重要的阻止因素是无法在业务逻辑（如查询）中使用“自然”关系，而无需引用联接表。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-139">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span> <span data-ttu-id="8e1c4-140">联接表实体类型可能仍然存在，但不应妨碍业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-140">The join table entity type may still exist, but it should not get in the way of business logic.</span></span> <span data-ttu-id="8e1c4-141">这就是我们选择为 5.0 处理跳过导航属性的原因。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-141">This is why we have chosen to tackle skip navigation properties for 5.0.</span></span>

<span data-ttu-id="8e1c4-142">此时，多对多的其他部分正在作为 EF Core 5.0 的延伸目标而进行从事。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-142">At this time the other parts of many-to-many are being pursued as a stretch goal for EF Core 5.0.</span></span> <span data-ttu-id="8e1c4-143">这意味着它们当前不在针对 5.0 的计划中，但是如果一切顺利，我们希望将它们纳入。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-143">This means they are not currently in the plan for 5.0, but if things go well we hope to pull them in.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="8e1c4-144">每个类型一张表 (TPT) 继承映射</span><span class="sxs-lookup"><span data-stu-id="8e1c4-144">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="8e1c4-145">开发人员负责人：@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="8e1c4-145">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="8e1c4-146">通过 [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-146">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="8e1c4-147">T 恤大小：XL</span><span class="sxs-lookup"><span data-stu-id="8e1c4-147">T-shirt size: XL</span></span>

<span data-ttu-id="8e1c4-148">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-148">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-149">我们要实现 TPT 是因为它是经常请求的功能（大约 254 张投票；第三名），并且它需要一些低级更改，我们认为这些更改适合于总体 .NET 5 计划的基础性质。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-149">We're doing TPT because it is both a highly requested feature (~254 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="8e1c4-150">我们预计这会形成数据库提供程序的重大更改，但与 3.0 所需的更改相比，这些更改的严重性要小得多。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-150">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="8e1c4-151">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="8e1c4-151">Filtered Include</span></span>

<span data-ttu-id="8e1c4-152">开发人员负责人：@maumar</span><span class="sxs-lookup"><span data-stu-id="8e1c4-152">Lead developer: @maumar</span></span>

<span data-ttu-id="8e1c4-153">通过 [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-153">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="8e1c4-154">T 恤大小：M</span><span class="sxs-lookup"><span data-stu-id="8e1c4-154">T-shirt size: M</span></span>

<span data-ttu-id="8e1c4-155">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-155">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-156">经过筛选的包含是经常请求的功能（大约 317 张投票；第二名），其工作量不大，我们认为这会使当前需要模型级筛选器或更复杂查询的许多方案不受阻碍或更加容易。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-156">Filtered Include is a highly-requested feature (~317 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="8e1c4-157">合理化 ToTable、ToQuery、ToView、FromSql 等。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-157">Rationalize ToTable, ToQuery, ToView, FromSql, etc.</span></span>

<span data-ttu-id="8e1c4-158">开发人员负责人：@maumar 和 @smitpatel</span><span class="sxs-lookup"><span data-stu-id="8e1c4-158">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="8e1c4-159">通过 [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-159">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="8e1c4-160">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-160">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-161">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-161">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-162">在以前的版本中，我们在支持原始 SQL、无键类型和相关领域方面取得了进展。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-162">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="8e1c4-163">但是，在所有内容作为一个整体协同工作的方式上存在差距和不一致。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-163">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="8e1c4-164">5\.0 的目标是修复这些问题，并为定义、迁移和使用不同类型的实体及其关联查询和数据库项目创造良好体验。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-164">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="8e1c4-165">这也可能涉及到已编译查询 API 的更新。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-165">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="8e1c4-166">请注意，此项可能会导致某些应用程序级重大更改，因为我们当前拥有的某些功能过于宽松，以至于它可能很快导致人们陷入困境。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-166">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="8e1c4-167">我们可能最终会阻止其中一些功能，并改为提供有关应执行的操作的指导。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-167">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="8e1c4-168">常规查询增强功能</span><span class="sxs-lookup"><span data-stu-id="8e1c4-168">General query enhancements</span></span>

<span data-ttu-id="8e1c4-169">开发人员负责人：@smitpatel 和 @maumar</span><span class="sxs-lookup"><span data-stu-id="8e1c4-169">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="8e1c4-170">通过 [5.0 里程碑中使用 `area-query` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-170">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="8e1c4-171">T 恤大小：XL</span><span class="sxs-lookup"><span data-stu-id="8e1c4-171">T-shirt size: XL</span></span>

<span data-ttu-id="8e1c4-172">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-172">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-173">查询转换代码已针对 EF Core 3.0 进行了广泛重写。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-173">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="8e1c4-174">因此，查询代码一般处于更可靠的状态。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-174">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="8e1c4-175">对于 5.0，在支持 TPT 和 skip 导航属性所需的更改范围之外，我们未计划进行重大查询更改。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-175">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="8e1c4-176">但是，仍然需要大量工作来修复 3.0 全面修改中遗留的一些技术债务。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-176">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="8e1c4-177">我们还计划修复许多 bug 并实现少量的增强功能，以进一步改进总体查询体验。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-177">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="8e1c4-178">迁移和部署体验</span><span class="sxs-lookup"><span data-stu-id="8e1c4-178">Migrations and deployment experience</span></span>

<span data-ttu-id="8e1c4-179">开发人员负责人：@bricelam</span><span class="sxs-lookup"><span data-stu-id="8e1c4-179">Lead developers: @bricelam</span></span>

<span data-ttu-id="8e1c4-180">通过 [#19587](https://github.com/dotnet/efcore/issues/19587) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-180">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="8e1c4-181">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-181">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-182">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-182">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-183">当前，许多开发人员在应用程序启动时迁移其数据库。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-183">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="8e1c4-184">这十分简单，但不建议这样做，因为：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-184">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="8e1c4-185">多个线程/进程/服务器可能会并发尝试迁移数据库</span><span class="sxs-lookup"><span data-stu-id="8e1c4-185">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="8e1c4-186">发生这种情况时，应用程序可能会尝试访问不一致的状态</span><span class="sxs-lookup"><span data-stu-id="8e1c4-186">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="8e1c4-187">通常，不应为应用程序执行授予修改架构的数据库权限</span><span class="sxs-lookup"><span data-stu-id="8e1c4-187">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="8e1c4-188">如果出现问题，则难以还原为干净状态</span><span class="sxs-lookup"><span data-stu-id="8e1c4-188">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="8e1c4-189">我们希望在这里提供更好的体验，从而可以轻松地在部署时迁移数据库。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-189">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="8e1c4-190">这应该：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-190">This should:</span></span>

* <span data-ttu-id="8e1c4-191">可在 Linux、Mac 和 Windows 上正常工作</span><span class="sxs-lookup"><span data-stu-id="8e1c4-191">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="8e1c4-192">在命令行上具有良好体验</span><span class="sxs-lookup"><span data-stu-id="8e1c4-192">Be a good experience on the command line</span></span>
* <span data-ttu-id="8e1c4-193">支持采用容器的方案</span><span class="sxs-lookup"><span data-stu-id="8e1c4-193">Support scenarios with containers</span></span>
* <span data-ttu-id="8e1c4-194">适用于常用的实际部署工具/流</span><span class="sxs-lookup"><span data-stu-id="8e1c4-194">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="8e1c4-195">至少集成到 Visual Studio 中</span><span class="sxs-lookup"><span data-stu-id="8e1c4-195">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="8e1c4-196">结果可能是在 EF Core 中进行许多小改进（例如，SQLite 上更好的迁移），并与其他团队一起进行指导和长期协作，以改进不仅限于 EF 的端到端体验。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-196">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="8e1c4-197">EF Core 平台体验</span><span class="sxs-lookup"><span data-stu-id="8e1c4-197">EF Core platforms experience</span></span> 

<span data-ttu-id="8e1c4-198">开发人员负责人：@roji 和 @bricelam</span><span class="sxs-lookup"><span data-stu-id="8e1c4-198">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="8e1c4-199">通过 [#19588](https://github.com/dotnet/efcore/issues/19588) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-199">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="8e1c4-200">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-200">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-201">状态：尚未开始</span><span class="sxs-lookup"><span data-stu-id="8e1c4-201">Status: Not started</span></span>

<span data-ttu-id="8e1c4-202">我们提供了有关在类似传统 MVC 的 Web 应用程序中使用 EF Core 的良好指导。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-202">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="8e1c4-203">适用于其他平台和应用程序模型的指导缺失或过期。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-203">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="8e1c4-204">对于 EF Core 5.0，我们计划调查、改进和记录在以下方面使用 EF Core 的体验：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-204">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="8e1c4-205">Blazor</span><span class="sxs-lookup"><span data-stu-id="8e1c4-205">Blazor</span></span>
* <span data-ttu-id="8e1c4-206">Xamarin，包括使用 AOT/链接器情景</span><span class="sxs-lookup"><span data-stu-id="8e1c4-206">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="8e1c4-207">WinForms/WPF/WinUI，可能还有其他 U.I.</span><span class="sxs-lookup"><span data-stu-id="8e1c4-207">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="8e1c4-208">框架</span><span class="sxs-lookup"><span data-stu-id="8e1c4-208">frameworks</span></span>

<span data-ttu-id="8e1c4-209">这可能是在 EF Core 中进行许多小改进，并与其他团队一起进行指导和长期协作，以改进不仅限于 EF 的端到端体验。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-209">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="8e1c4-210">我们计划研究的特定领域有：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-210">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="8e1c4-211">部署，包括使用 EF 工具（例如用于迁移）的体验</span><span class="sxs-lookup"><span data-stu-id="8e1c4-211">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="8e1c4-212">应用程序模型（包括 Xamarin 和 Blazor），可能还有其他模型</span><span class="sxs-lookup"><span data-stu-id="8e1c4-212">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="8e1c4-213">SQLite 体验，包括空间体验和表重新生成</span><span class="sxs-lookup"><span data-stu-id="8e1c4-213">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="8e1c4-214">AOT 和链接体验</span><span class="sxs-lookup"><span data-stu-id="8e1c4-214">AOT and linking experiences</span></span>
* <span data-ttu-id="8e1c4-215">诊断集成，包括性能计数器</span><span class="sxs-lookup"><span data-stu-id="8e1c4-215">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="8e1c4-216">性能</span><span class="sxs-lookup"><span data-stu-id="8e1c4-216">Performance</span></span>

<span data-ttu-id="8e1c4-217">开发人员负责人：@roji</span><span class="sxs-lookup"><span data-stu-id="8e1c4-217">Lead developer: @roji</span></span>

<span data-ttu-id="8e1c4-218">通过 [5.0 里程碑中使用 `area-perf` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-218">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="8e1c4-219">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-219">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-220">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-220">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-221">对于 EF Core，我们计划改进性能基准套件，并对运行时进行定向性能改进。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-221">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="8e1c4-222">此外，我们计划完成在 3.0 发布周期内进行原型设计的新 ADO.NET 批处理 API。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-222">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="8e1c4-223">同样在 ADO.NET 层，我们计划对 Npgsql 提供程序进行其他性能改进。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-223">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="8e1c4-224">作为此工作的一部分，我们还计划根据需要添加 ADO.NET/EF Core 性能计数器和其他诊断。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-224">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="8e1c4-225">体系结构/参与者文档</span><span class="sxs-lookup"><span data-stu-id="8e1c4-225">Architectural/contributor documentation</span></span>

<span data-ttu-id="8e1c4-226">文档管理人员主管：@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="8e1c4-226">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="8e1c4-227">通过 [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-227">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="8e1c4-228">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-228">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-229">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-229">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-230">这里的思路是使人们更容易了解 EF Core 的内部情况。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-230">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="8e1c4-231">这可能对于使用 EF Core 的任何人都十分有用，但主要动机是使外部人员更容易：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-231">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="8e1c4-232">为 EF Core 代码做出贡献</span><span class="sxs-lookup"><span data-stu-id="8e1c4-232">Contribute to the EF Core code</span></span>
* <span data-ttu-id="8e1c4-233">创建数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="8e1c4-233">Create database providers</span></span>
* <span data-ttu-id="8e1c4-234">构建其他扩展</span><span class="sxs-lookup"><span data-stu-id="8e1c4-234">Build other extensions</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="8e1c4-235">Microsoft.Data.Sqlite 文档</span><span class="sxs-lookup"><span data-stu-id="8e1c4-235">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="8e1c4-236">文档管理人员主管：@bricelam</span><span class="sxs-lookup"><span data-stu-id="8e1c4-236">Lead documenter: @bricelam</span></span>

<span data-ttu-id="8e1c4-237">通过 [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675) 进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-237">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="8e1c4-238">T 恤大小：M</span><span class="sxs-lookup"><span data-stu-id="8e1c4-238">T-shirt size: M</span></span>

<span data-ttu-id="8e1c4-239">状态：已完成。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-239">Status: Completed.</span></span> <span data-ttu-id="8e1c4-240">新文档在 [Microsoft 文档站点上提供](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-240">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="8e1c4-241">EF 团队还拥有 Microsoft.Data.Sqlite ADO.NET 提供程序。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-241">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="8e1c4-242">我们计划在 5.0 版本中完整记录此提供程序。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-242">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="8e1c4-243">常规文档</span><span class="sxs-lookup"><span data-stu-id="8e1c4-243">General documentation</span></span>

<span data-ttu-id="8e1c4-244">文档管理人员主管：@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="8e1c4-244">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="8e1c4-245">通过 [5.0 里程碑的文档存储库中的问题](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-245">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="8e1c4-246">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-246">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-247">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-247">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-248">我们已在针对 3.0 和 3.1 版本更新文档。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-248">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="8e1c4-249">我们还在致力于：</span><span class="sxs-lookup"><span data-stu-id="8e1c4-249">We are also working on:</span></span>
  * <span data-ttu-id="8e1c4-250">全面修改入门文档，使它们更易于理解/更易于遵循</span><span class="sxs-lookup"><span data-stu-id="8e1c4-250">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
  * <span data-ttu-id="8e1c4-251">重新组织文档，使内容更易于查找并添加交叉引用</span><span class="sxs-lookup"><span data-stu-id="8e1c4-251">Reorganization of docs to make things easier to find and to add cross-references</span></span>
  * <span data-ttu-id="8e1c4-252">向现有文档添加更多详细信息和说明</span><span class="sxs-lookup"><span data-stu-id="8e1c4-252">Adding more details and clarifications to existing docs</span></span>
  * <span data-ttu-id="8e1c4-253">更新示例并添加更多示例</span><span class="sxs-lookup"><span data-stu-id="8e1c4-253">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="8e1c4-254">修复 bug</span><span class="sxs-lookup"><span data-stu-id="8e1c4-254">Fixing bugs</span></span>

<span data-ttu-id="8e1c4-255">通过 [5.0 里程碑中使用 `type-bug` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-255">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="8e1c4-256">开发人员：@roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="8e1c4-256">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="8e1c4-257">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-257">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-258">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-258">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-259">撰写本文时，我们已将 135 个 bug 会审为在 5.0 版本中进行修复（已修复了 62 个），但与上面的常规查询增强功能部分存在很大的重叠  。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-259">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="8e1c4-260">在 3.0 版本期间，传入率（最终成为里程碑工作的问题）大约为每月 23 个问题。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-260">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="8e1c4-261">并非所有这些问题都需要在 5.0 中进行修复。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-261">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="8e1c4-262">作为大致估计，我们计划修复在 5.0 时间范围内修复其他 150 个问题。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-262">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="8e1c4-263">小型增强功能</span><span class="sxs-lookup"><span data-stu-id="8e1c4-263">Small enhancements</span></span>

<span data-ttu-id="8e1c4-264">通过 [5.0 里程碑中使用 `type-enhancement` 标记的问题](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-264">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="8e1c4-265">开发人员：@roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="8e1c4-265">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="8e1c4-266">T 恤大小：L</span><span class="sxs-lookup"><span data-stu-id="8e1c4-266">T-shirt size: L</span></span>

<span data-ttu-id="8e1c4-267">状态：正在进行</span><span class="sxs-lookup"><span data-stu-id="8e1c4-267">Status: In-progress</span></span>

<span data-ttu-id="8e1c4-268">除了上面概述的较大功能之外，我们还计划对 5.0 进行许多较小的改进，以修复“小问题”。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-268">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="8e1c4-269">请注意，上面概述的更一般主题也涵盖了其中许多增强功能。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-269">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="8e1c4-270">非计划</span><span class="sxs-lookup"><span data-stu-id="8e1c4-270">Below-the-line</span></span>

<span data-ttu-id="8e1c4-271">通过[使用 `consider-for-next-release` 标记的问题](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)进行跟踪</span><span class="sxs-lookup"><span data-stu-id="8e1c4-271">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="8e1c4-272">这些是当前未针对 5.0 版本  计划的 bug 修复和增强功能，但我们将根据以上工作的进度将它们视为延伸目标。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-272">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="8e1c4-273">此外，我们始终会在计划时考虑[投票最多的问题](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-273">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="8e1c4-274">从版本中去除其中任何问题总是很痛苦的，但是我们确实需要针对所拥有的资源制定切合实际的计划。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-274">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="feedback"></a><span data-ttu-id="8e1c4-275">反馈</span><span class="sxs-lookup"><span data-stu-id="8e1c4-275">Feedback</span></span>

<span data-ttu-id="8e1c4-276">你对计划的反馈非常重要。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-276">Your feedback on planning is important.</span></span> <span data-ttu-id="8e1c4-277">指示问题重要性的最佳方式是在 GitHub 上为该问题投票（竖起大拇指）。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-277">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="8e1c4-278">然后，此数据将进入下一个版本的[计划过程](../release-planning.md)。</span><span class="sxs-lookup"><span data-stu-id="8e1c4-278">This data will then feed into the [planning process](../release-planning.md) for the next release.</span></span>
