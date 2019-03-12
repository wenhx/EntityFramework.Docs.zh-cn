---
title: EF Core 3.0 中的中断性变更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: a7e1a03bf1131cd53123f5cc39b07bed94619b44
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463359"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="e3604-102">EF Core 3.0 中包含的中断性变更（目前处于预览状态）</span><span class="sxs-lookup"><span data-stu-id="e3604-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e3604-103">请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。</span><span class="sxs-lookup"><span data-stu-id="e3604-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="e3604-104">以下 API 和行为更改有可能在将 EF Core 2.2.x 升级到 3.0.0 时中断为其开发的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="e3604-105">我们将仅影响数据库提供程序的更改记录在[提供程序更改](../../providers/provider-log.md)下。</span><span class="sxs-lookup"><span data-stu-id="e3604-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="e3604-106">其中没有记录从一个 3.0 预览版引入到另一个 3.0 预览版的新功能的中断。</span><span class="sxs-lookup"><span data-stu-id="e3604-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="e3604-107">不再在客户端上计算 LINQ 查询</span><span class="sxs-lookup"><span data-stu-id="e3604-107">LINQ queries are no longer evaluated on the client</span></span>

[<span data-ttu-id="e3604-108">跟踪问题 #12795</span><span class="sxs-lookup"><span data-stu-id="e3604-108">Tracking Issue #12795</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

> [!IMPORTANT]
> <span data-ttu-id="e3604-109">我们会预先宣布此中断。</span><span class="sxs-lookup"><span data-stu-id="e3604-109">We are pre-announcing this break.</span></span>
<span data-ttu-id="e3604-110">它尚未在任何 3.0 预览版中发布。</span><span class="sxs-lookup"><span data-stu-id="e3604-110">It has not yet shipped in any 3.0 preview.</span></span>

<span data-ttu-id="e3604-111">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-111">**Old behavior**</span></span>

<span data-ttu-id="e3604-112">在 3.0 之前，当 EF Core 无法将查询中的表达式转换为 SQL 或参数时，它会在客户端上自动计算表达式的值。</span><span class="sxs-lookup"><span data-stu-id="e3604-112">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="e3604-113">默认情况下，客户端对潜在的昂贵表达式的计算仅触发警告。</span><span class="sxs-lookup"><span data-stu-id="e3604-113">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="e3604-114">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-114">**New behavior**</span></span>

<span data-ttu-id="e3604-115">从 3.0 开始，EF Core 仅允许在客户端上计算顶级投影中的表达式（查询中的最后一个 `Select()` 调用）。</span><span class="sxs-lookup"><span data-stu-id="e3604-115">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="e3604-116">当查询的任何其他部分中的表达式无法转换为 SQL 或参数时，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="e3604-116">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="e3604-117">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-117">**Why**</span></span>

<span data-ttu-id="e3604-118">自动的客户端查询计算允许执行许多查询，即使它们的重要组成部分无法转换。</span><span class="sxs-lookup"><span data-stu-id="e3604-118">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="e3604-119">此行为可能导致意外且具有潜在破坏性的行为，这些行为可能仅在生产中变得明显。</span><span class="sxs-lookup"><span data-stu-id="e3604-119">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="e3604-120">例如，`Where()` 调用中无法转换的条件可能导致表中的所有行从数据库服务器传输且筛选器应用于客户端。</span><span class="sxs-lookup"><span data-stu-id="e3604-120">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="e3604-121">如果在开发中表中只包含几行，则不容易检测到这种情况，但是当应用程序转入生产环节时，由于表中可能包含数百万行，这种情况会非常严重。</span><span class="sxs-lookup"><span data-stu-id="e3604-121">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="e3604-122">在开发过程中，客户端求值警告也很容易被忽视。</span><span class="sxs-lookup"><span data-stu-id="e3604-122">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="e3604-123">除此之外，自动客户端计算可能会导致问题，其中改进特定表达式的查询转换会导致版本之间发生意外中断性变更。</span><span class="sxs-lookup"><span data-stu-id="e3604-123">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="e3604-124">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-124">**Mitigations**</span></span>

<span data-ttu-id="e3604-125">如果无法完全转换查询，则以可转换的形式重写查询，或使用 `AsEnumerable()`、`ToList()` 或类似信息将数据显式返回客户端，然后可以进一步使用 LINQ 到对象处理。</span><span class="sxs-lookup"><span data-stu-id="e3604-125">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="e3604-126">Entity Framework Core 不再是 ASP.NET Core 共享框架的一部分</span><span class="sxs-lookup"><span data-stu-id="e3604-126">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="e3604-127">跟踪问题公告 #325</span><span class="sxs-lookup"><span data-stu-id="e3604-127">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="e3604-128">此更改是在 ASP.NET Core 3.0-preview 1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-128">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="e3604-129">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-129">**Old behavior**</span></span>

<span data-ttu-id="e3604-130">在 ASP.NET Core 3.0 之前，当向 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 添加包引用时，它将包括 EF Core 和一些 EF Core 数据提供程序（如 SQL Server 提供程序）。</span><span class="sxs-lookup"><span data-stu-id="e3604-130">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="e3604-131">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-131">**New behavior**</span></span>

<span data-ttu-id="e3604-132">从 3.0 开始，ASP.NET Core 共享框架不包括 EF Core 或任何 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-132">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="e3604-133">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-133">**Why**</span></span>

<span data-ttu-id="e3604-134">在此更改之前，获取 EF Core 需要不同的步骤，具体取决于应用程序是否是面向 ASP.NET Core 和 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="e3604-134">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="e3604-135">此外，升级 ASP.NET Core 会强制升级 EF Core 和 SQL Server 提供程序，这并不总是可取的。</span><span class="sxs-lookup"><span data-stu-id="e3604-135">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="e3604-136">通过此更改，通过所有提供程序、支持的 .NET 实现和应用程序类型获取 EF Core 的体验都是一致的。</span><span class="sxs-lookup"><span data-stu-id="e3604-136">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="e3604-137">开发人员现在还可以准确控制何时升级 EF Core 和 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-137">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="e3604-138">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-138">**Mitigations**</span></span>

<span data-ttu-id="e3604-139">若要在 ASP.NET Core 3.0 应用程序或任何其他受支持的应用程序中使用 EF Core，请显式添加对应用程序将使用的 EF Core 数据库提供程序的包引用。</span><span class="sxs-lookup"><span data-stu-id="e3604-139">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="e3604-140">在调试级别记录查询执行</span><span class="sxs-lookup"><span data-stu-id="e3604-140">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="e3604-141">跟踪问题 #14523</span><span class="sxs-lookup"><span data-stu-id="e3604-141">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="e3604-142">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-142">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-143">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-143">**Old behavior**</span></span>

<span data-ttu-id="e3604-144">EF Core 3.0 之前，是在 `Info` 级别记录查询和其他命令的执行。</span><span class="sxs-lookup"><span data-stu-id="e3604-144">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="e3604-145">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-145">**New behavior**</span></span>

<span data-ttu-id="e3604-146">从 EF Core 3.0 开始，将在 `Debug` 级别记录命令/SQL 的执行。</span><span class="sxs-lookup"><span data-stu-id="e3604-146">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="e3604-147">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-147">**Why**</span></span>

<span data-ttu-id="e3604-148">此更改是为了降低 `Info` 日志级别的噪音。</span><span class="sxs-lookup"><span data-stu-id="e3604-148">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="e3604-149">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-149">**Mitigations**</span></span>

<span data-ttu-id="e3604-150">此日志记录事件由 `RelationalEventId.CommandExecuting` 定义，事件 ID 为 20100。</span><span class="sxs-lookup"><span data-stu-id="e3604-150">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="e3604-151">若要再次在 `Info` 级别记录 SQL，请启用 `Debug` 级别的日志记录并筛选到仅此一个事件。</span><span class="sxs-lookup"><span data-stu-id="e3604-151">To log SQL at the `Info` level again, switch on logging at the `Debug` level and filter to just this event.</span></span>


## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="e3604-152">不再在实体实例上设置临时键值</span><span class="sxs-lookup"><span data-stu-id="e3604-152">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="e3604-153">跟踪问题 #12378</span><span class="sxs-lookup"><span data-stu-id="e3604-153">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="e3604-154">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-154">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="e3604-155">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-155">**Old behavior**</span></span>

<span data-ttu-id="e3604-156">在 EF Core 3.0 之前，临时值已分配给所有键属性，这些属性稍后将具有数据库生成的实际值。</span><span class="sxs-lookup"><span data-stu-id="e3604-156">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="e3604-157">通常这些临时值是较大负数。</span><span class="sxs-lookup"><span data-stu-id="e3604-157">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="e3604-158">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-158">**New behavior**</span></span>

<span data-ttu-id="e3604-159">从 3.0 开始，EF Core 将临时键值存储为实体跟踪信息的一部分，并保持键属性本身不变。</span><span class="sxs-lookup"><span data-stu-id="e3604-159">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="e3604-160">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-160">**Why**</span></span>

<span data-ttu-id="e3604-161">此更改是为了防止当之前由某个 `DbContext` 实例跟踪的实体移动到另一个 `DbContext` 实例时，临时键值错误地变成永久值。</span><span class="sxs-lookup"><span data-stu-id="e3604-161">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="e3604-162">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-162">**Mitigations**</span></span>

<span data-ttu-id="e3604-163">如果主键是存储生成的并且属于 `Added` 状态的实体，则将主键值分配到外键以在实体之间形成关联的应用可能会依赖于旧行为。</span><span class="sxs-lookup"><span data-stu-id="e3604-163">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="e3604-164">可通过以下方式避免：</span><span class="sxs-lookup"><span data-stu-id="e3604-164">This can be avoided by:</span></span>
* <span data-ttu-id="e3604-165">不使用存储生成的密钥。</span><span class="sxs-lookup"><span data-stu-id="e3604-165">Not using store-generated keys.</span></span>
* <span data-ttu-id="e3604-166">设置导航属性以形成关系，而不是设置外键值。</span><span class="sxs-lookup"><span data-stu-id="e3604-166">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="e3604-167">从实体的跟踪信息中获取实际的临时键值。</span><span class="sxs-lookup"><span data-stu-id="e3604-167">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="e3604-168">例如，即使 `blog.Id` 本身尚未设置，`context.Entry(blog).Property(e => e.Id).CurrentValue` 也将返回临时值。</span><span class="sxs-lookup"><span data-stu-id="e3604-168">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="e3604-169">DetectChanges 遵循存储生成的键值</span><span class="sxs-lookup"><span data-stu-id="e3604-169">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="e3604-170">跟踪问题 #14616</span><span class="sxs-lookup"><span data-stu-id="e3604-170">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="e3604-171">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-171">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-172">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-172">**Old behavior**</span></span>

<span data-ttu-id="e3604-173">在 EF Core 3.0 之前，`DetectChanges` 找到的未跟踪实体将在 `Added` 状态中被跟踪，并在调用 `SaveChanges` 时作为新行插入。</span><span class="sxs-lookup"><span data-stu-id="e3604-173">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="e3604-174">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-174">**New behavior**</span></span>

<span data-ttu-id="e3604-175">从 EF Core 3.0 开始，如果实体使用生成的键值并设置了某个键值，则将在 `Modified` 状态下跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="e3604-175">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="e3604-176">这意味着假定存在实体的行，并且在调用 `SaveChanges` 时将更新该行。</span><span class="sxs-lookup"><span data-stu-id="e3604-176">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="e3604-177">如果未设置键值，或者实体类型未使用生成的键，则新实体仍将像先前版本一样被作为 `Added` 跟踪。</span><span class="sxs-lookup"><span data-stu-id="e3604-177">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="e3604-178">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-178">**Why**</span></span>

<span data-ttu-id="e3604-179">进行此更改是为了在使用存储生成的键时更轻松、更一致地使用断开连接的实体图。</span><span class="sxs-lookup"><span data-stu-id="e3604-179">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="e3604-180">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-180">**Mitigations**</span></span>

<span data-ttu-id="e3604-181">如果将实体类型配置为使用生成的键，但为新实例显式设置了键值，则此更改可能会中断应用程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-181">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="e3604-182">解决方案是显式配置键属性，使其不使用生成的值。</span><span class="sxs-lookup"><span data-stu-id="e3604-182">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="e3604-183">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="e3604-183">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="e3604-184">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="e3604-184">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="e3604-185">默认情况下，现在会立即发生级联删除</span><span class="sxs-lookup"><span data-stu-id="e3604-185">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="e3604-186">跟踪问题 #10114</span><span class="sxs-lookup"><span data-stu-id="e3604-186">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="e3604-187">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-188">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-188">**Old behavior**</span></span>

<span data-ttu-id="e3604-189">在 3.0 之前，直到调用 SaveChanges 时，EF Core 才会应用级联操作（删除所需主体时或者在切断与所需主体的关系时删除依赖实体）。</span><span class="sxs-lookup"><span data-stu-id="e3604-189">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="e3604-190">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-190">**New behavior**</span></span>

<span data-ttu-id="e3604-191">从 3.0 开始，一旦检测到触发条件，EF Core 就会应用级联操作。</span><span class="sxs-lookup"><span data-stu-id="e3604-191">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="e3604-192">例如，调用 `context.Remove()` 来删除主体实体将导致所有跟踪的相关必需依赖项也立即设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="e3604-192">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="e3604-193">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-193">**Why**</span></span>

<span data-ttu-id="e3604-194">此更改是为了改善数据绑定和审核方案的体验，在相关体验中，需要了解在调用 `SaveChanges` _之前_会删除哪些实体。</span><span class="sxs-lookup"><span data-stu-id="e3604-194">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="e3604-195">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-195">**Mitigations**</span></span>

<span data-ttu-id="e3604-196">可以通过 `context.ChangedTracker` 上的设置还原以前的行为。</span><span class="sxs-lookup"><span data-stu-id="e3604-196">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="e3604-197">例如:</span><span class="sxs-lookup"><span data-stu-id="e3604-197">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="e3604-198">查询类型与实体类型合并</span><span class="sxs-lookup"><span data-stu-id="e3604-198">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="e3604-199">跟踪问题 #14194</span><span class="sxs-lookup"><span data-stu-id="e3604-199">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="e3604-200">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-200">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-201">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-201">**Old behavior**</span></span>

<span data-ttu-id="e3604-202">在 EF Core 3.0 之前，[查询类型](xref:core/modeling/query-types)是一种查询未以结构化方式定义主键的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="e3604-202">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="e3604-203">也就是说，查询类型用于映射没有键的实体类型（更可能来自视图，但也可能来自表），而当有可用的键时则使用常规实体类型（更可能来自表，但也可能来自视图）。</span><span class="sxs-lookup"><span data-stu-id="e3604-203">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="e3604-204">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-204">**New behavior**</span></span>

<span data-ttu-id="e3604-205">现在，查询类型只是一个没有主键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="e3604-205">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="e3604-206">无键实体类型与先前版本中的查询类型具有相同的功能。</span><span class="sxs-lookup"><span data-stu-id="e3604-206">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="e3604-207">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-207">**Why**</span></span>

<span data-ttu-id="e3604-208">这样做是为了减少对查询类型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="e3604-208">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="e3604-209">具体来说，它们是无键实体类型，因此本质上是只读的，但是不应该仅仅因为实体类型需要是只读的就使用它们。</span><span class="sxs-lookup"><span data-stu-id="e3604-209">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="e3604-210">同样，它们通常映射到视图，但这只是因为视图通常不定义键。</span><span class="sxs-lookup"><span data-stu-id="e3604-210">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="e3604-211">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-211">**Mitigations**</span></span>

<span data-ttu-id="e3604-212">API 的以下部分现已过时：</span><span class="sxs-lookup"><span data-stu-id="e3604-212">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="e3604-213">**`ModelBuilder.Query<>()`** - 需要调用 `ModelBuilder.Entity<>().HasNoKey()` 来将实体类型标记为没有键。</span><span class="sxs-lookup"><span data-stu-id="e3604-213">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="e3604-214">这仍然不会按约定配置，以避免在需要主键但是与约定不匹配时发生配置错误。</span><span class="sxs-lookup"><span data-stu-id="e3604-214">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="e3604-215">**`DbQuery<>`** - 应使用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="e3604-215">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="e3604-216">**`DbContext.Query<>()`** - 应使用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="e3604-216">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="e3604-217">从属类型关系的配置 API 已更改</span><span class="sxs-lookup"><span data-stu-id="e3604-217">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="e3604-218">[跟踪问题 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[跟踪问题 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[跟踪问题 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="e3604-218">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="e3604-219">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-219">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-220">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-220">**Old behavior**</span></span>

<span data-ttu-id="e3604-221">EF Core 3.0 之前，会在 `OwnsOne` 或 `OwnsMany` 调用之后直接执行所拥有关系的配置。</span><span class="sxs-lookup"><span data-stu-id="e3604-221">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="e3604-222">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-222">**New behavior**</span></span>

<span data-ttu-id="e3604-223">从 EF Core 3.0 开始，Fluent API 会使用 `WithOwner()` 为所有者配置导航属性。</span><span class="sxs-lookup"><span data-stu-id="e3604-223">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="e3604-224">例如:</span><span class="sxs-lookup"><span data-stu-id="e3604-224">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="e3604-225">与所有者之间关系相关的配置现会在 `WithOwner()` 之后关联起来，就像配置其他关系一样。</span><span class="sxs-lookup"><span data-stu-id="e3604-225">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="e3604-226">但从属类型本身的配置仍会在 `OwnsOne()/OwnsMany()` 之后关联。</span><span class="sxs-lookup"><span data-stu-id="e3604-226">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="e3604-227">例如:</span><span class="sxs-lookup"><span data-stu-id="e3604-227">For example:</span></span>

```C#
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

<span data-ttu-id="e3604-228">此外，使用固有类型目标调用 `Entity()``HasOne()` 或 `Set()` 现将引发异常。</span><span class="sxs-lookup"><span data-stu-id="e3604-228">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="e3604-229">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-229">**Why**</span></span>

<span data-ttu-id="e3604-230">此更改是为了更清晰的区分从属类型本身的配置和与从属类型的_关系_的配置。</span><span class="sxs-lookup"><span data-stu-id="e3604-230">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="e3604-231">这反过来消除了诸如 `HasForeignKey` 之类的方法的模糊性和混淆。</span><span class="sxs-lookup"><span data-stu-id="e3604-231">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="e3604-232">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-232">**Mitigations**</span></span>

<span data-ttu-id="e3604-233">更改从属类型关系的配置以使用新的 API 曲面，如上例所示。</span><span class="sxs-lookup"><span data-stu-id="e3604-233">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="e3604-234">外键属性约定不再匹配与主体属性相同的名称</span><span class="sxs-lookup"><span data-stu-id="e3604-234">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="e3604-235">跟踪问题 #13274</span><span class="sxs-lookup"><span data-stu-id="e3604-235">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="e3604-236">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-236">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-237">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-237">**Old behavior**</span></span>

<span data-ttu-id="e3604-238">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="e3604-238">Consider the following model:</span></span>
```C#
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
<span data-ttu-id="e3604-239">在 EF Core 3.0 之前，`CustomerId` 属性将按约定用于外键。</span><span class="sxs-lookup"><span data-stu-id="e3604-239">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="e3604-240">但是，如果 `Order` 是从属类型，那么这也会使 `CustomerId` 成为主键，这通常不是预期结果。</span><span class="sxs-lookup"><span data-stu-id="e3604-240">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="e3604-241">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-241">**New behavior**</span></span>

<span data-ttu-id="e3604-242">从 3.0 开始，如果外键具有与主体属性相同的名称，则 EF Core 不会尝试按约定使用外键属性。</span><span class="sxs-lookup"><span data-stu-id="e3604-242">Starting with 3.0, EF Core won't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="e3604-243">与主体属性名称关联的主体类型名称和与主体属性名称模式关联的导航名称仍然相匹配。</span><span class="sxs-lookup"><span data-stu-id="e3604-243">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="e3604-244">例如:</span><span class="sxs-lookup"><span data-stu-id="e3604-244">For example:</span></span>

```C#
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

```C#
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

<span data-ttu-id="e3604-245">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-245">**Why**</span></span>

<span data-ttu-id="e3604-246">此更改是为了避免错误地在从属类型上定义主键属性。</span><span class="sxs-lookup"><span data-stu-id="e3604-246">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="e3604-247">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-247">**Mitigations**</span></span>

<span data-ttu-id="e3604-248">如果该属性将成为外键，即为主键的一部分，则显式配置它。</span><span class="sxs-lookup"><span data-stu-id="e3604-248">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="e3604-249">每个属性使用独立的内存中整数键生成</span><span class="sxs-lookup"><span data-stu-id="e3604-249">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="e3604-250">跟踪问题 #6872</span><span class="sxs-lookup"><span data-stu-id="e3604-250">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="e3604-251">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="e3604-251">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="e3604-252">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-252">**Old behavior**</span></span>

<span data-ttu-id="e3604-253">在 EF Core 3.0 之前，一个共享值生成器用于所有内存中的整数键属性。</span><span class="sxs-lookup"><span data-stu-id="e3604-253">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="e3604-254">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-254">**New behavior**</span></span>

<span data-ttu-id="e3604-255">从 EF Core 3.0 开始，每个整数键属性在使用内存数据库时都会获取其自己的值生成器。</span><span class="sxs-lookup"><span data-stu-id="e3604-255">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="e3604-256">此外，如果删除了数据库，则会为所有表重置键生成。</span><span class="sxs-lookup"><span data-stu-id="e3604-256">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="e3604-257">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-257">**Why**</span></span>

<span data-ttu-id="e3604-258">此更改的目的是使内存中的键生成更接近于实际的数据库键生成，并改进在使用内存中的数据库时隔离测试的功能。</span><span class="sxs-lookup"><span data-stu-id="e3604-258">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="e3604-259">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-259">**Mitigations**</span></span>

<span data-ttu-id="e3604-260">这可能会中断依赖于特定内存中键值的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-260">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="e3604-261">请考虑不依赖于特定键值，或者进行更新以匹配新行为。</span><span class="sxs-lookup"><span data-stu-id="e3604-261">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="e3604-262">默认情况下使用支持字段</span><span class="sxs-lookup"><span data-stu-id="e3604-262">Backing fields are used by default</span></span>

[<span data-ttu-id="e3604-263">跟踪问题 #12430</span><span class="sxs-lookup"><span data-stu-id="e3604-263">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="e3604-264">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-264">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="e3604-265">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-265">**Old behavior**</span></span>

<span data-ttu-id="e3604-266">在 3.0 之前，即使已知属性的支持字段，EF Core 仍将默认使用属性 getter 和 setter 方法读取和写入属性值。</span><span class="sxs-lookup"><span data-stu-id="e3604-266">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="e3604-267">例外情况是查询执行，如果已知，将直接设置支持字段。</span><span class="sxs-lookup"><span data-stu-id="e3604-267">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="e3604-268">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-268">**New behavior**</span></span>

<span data-ttu-id="e3604-269">从 EF Core 3.0 开始，如果已知属性的支持字段，则始终使用支持字段读取和写入该属性。</span><span class="sxs-lookup"><span data-stu-id="e3604-269">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="e3604-270">如果应用程序依赖于编码到 getter 或 setter 方法中的其他行为，则可能导致应用程序中断。</span><span class="sxs-lookup"><span data-stu-id="e3604-270">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="e3604-271">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-271">**Why**</span></span>

<span data-ttu-id="e3604-272">此更改是为了防止 EF Core 在执行涉及实体的数据库操作时默认错误地触发业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="e3604-272">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="e3604-273">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-273">**Mitigations**</span></span>

<span data-ttu-id="e3604-274">可以通过在 modelBuilder fluent API 中配置属性访问模式来恢复 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="e3604-274">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="e3604-275">例如:</span><span class="sxs-lookup"><span data-stu-id="e3604-275">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="e3604-276">如果找到多个兼容的支持字段，则引发</span><span class="sxs-lookup"><span data-stu-id="e3604-276">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="e3604-277">跟踪问题 #12523</span><span class="sxs-lookup"><span data-stu-id="e3604-277">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="e3604-278">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="e3604-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="e3604-279">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-279">**Old behavior**</span></span>

<span data-ttu-id="e3604-280">在 EF Core 3.0 之前，如果多个字段与查找属性的后备字段的规则匹配，则将基于某种优先顺序选择一个字段。</span><span class="sxs-lookup"><span data-stu-id="e3604-280">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="e3604-281">这可能导致在不明确的情况下使用错误字段。</span><span class="sxs-lookup"><span data-stu-id="e3604-281">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="e3604-282">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-282">**New behavior**</span></span>

<span data-ttu-id="e3604-283">从 EF Core 3.0 开始，如果多个字段与同一属性匹配，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="e3604-283">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="e3604-284">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-284">**Why**</span></span>

<span data-ttu-id="e3604-285">此更改是为了避免在只有一个字段是正确的情况下无提示地使用另一个字段。</span><span class="sxs-lookup"><span data-stu-id="e3604-285">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="e3604-286">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-286">**Mitigations**</span></span>

<span data-ttu-id="e3604-287">具有不明确的支持字段的属性必须具有显式指定的字段。</span><span class="sxs-lookup"><span data-stu-id="e3604-287">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="e3604-288">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="e3604-288">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="e3604-289">DbContext.Entry 现在执行本地 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="e3604-289">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="e3604-290">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="e3604-290">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="e3604-291">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-291">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-292">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-292">**Old behavior**</span></span>

<span data-ttu-id="e3604-293">在 EF Core 3.0 之前，调用 `DbContext.Entry` 将导致检测到所有被跟踪实体的更改。</span><span class="sxs-lookup"><span data-stu-id="e3604-293">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="e3604-294">这确保了 `EntityEntry` 中暴露的状态是最新的。</span><span class="sxs-lookup"><span data-stu-id="e3604-294">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="e3604-295">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-295">**New behavior**</span></span>

<span data-ttu-id="e3604-296">从 EF Core 3.0 开始，调用 `DbContext.Entry` 现在只会尝试检测给定实体和与之相关的任何跟踪主体实体的更改。</span><span class="sxs-lookup"><span data-stu-id="e3604-296">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="e3604-297">这意味着可能无法通过调用此方法检测到其他位置的更改，这可能会影响应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="e3604-297">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="e3604-298">请注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 设置为 `false`，则即使是本地更改检测也将被禁用。</span><span class="sxs-lookup"><span data-stu-id="e3604-298">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="e3604-299">导致更改检测的其他方法（例如 `ChangeTracker.Entries` 和 `SaveChanges`）仍然会导致所有被跟踪实体的完整 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="e3604-299">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="e3604-300">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-300">**Why**</span></span>

<span data-ttu-id="e3604-301">此更改是为了提高使用 `context.Entry` 的默认性能。</span><span class="sxs-lookup"><span data-stu-id="e3604-301">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="e3604-302">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-302">**Mitigations**</span></span>

<span data-ttu-id="e3604-303">在调用 `Entry` 之前显式调用 `ChgangeTracker.DetectChanges()` 以确保 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="e3604-303">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="e3604-304">默认情况下，字符串和字节数组键不是客户端生成的</span><span class="sxs-lookup"><span data-stu-id="e3604-304">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="e3604-305">跟踪问题 #14617</span><span class="sxs-lookup"><span data-stu-id="e3604-305">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="e3604-306">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="e3604-306">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="e3604-307">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-307">**Old behavior**</span></span>

<span data-ttu-id="e3604-308">在 EF Core 3.0 之前，可以使用 `string` 和 `byte[]` 键属性，而不需要显式地设置非 null 值。</span><span class="sxs-lookup"><span data-stu-id="e3604-308">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="e3604-309">在这种情况下，键值将在客户端上生成为 GUID，并序列化为 `byte[]` 的字节。</span><span class="sxs-lookup"><span data-stu-id="e3604-309">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="e3604-310">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-310">**New behavior**</span></span>

<span data-ttu-id="e3604-311">从 EF Core 3.0 开始，将引发异常，指示未设置任何键值。</span><span class="sxs-lookup"><span data-stu-id="e3604-311">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="e3604-312">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-312">**Why**</span></span>

<span data-ttu-id="e3604-313">之所以进行此更改是因为客户端生成的 `string`/`byte[]` 值通常没有用，并且默认行为使得很难以通用方式推断生成的键值。</span><span class="sxs-lookup"><span data-stu-id="e3604-313">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="e3604-314">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-314">**Mitigations**</span></span>

<span data-ttu-id="e3604-315">如果没有设置其他非 null 值，则可以通过显式指定键属性应使用生成的值来获得 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="e3604-315">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="e3604-316">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="e3604-316">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="e3604-317">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="e3604-317">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="e3604-318">ILoggerFactory 现在是一个在一定范围内有效的服务</span><span class="sxs-lookup"><span data-stu-id="e3604-318">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="e3604-319">跟踪问题 #14698</span><span class="sxs-lookup"><span data-stu-id="e3604-319">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="e3604-320">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-320">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-321">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-321">**Old behavior**</span></span>

<span data-ttu-id="e3604-322">在 EF Core 3.0 之前，`ILoggerFactory` 被注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="e3604-322">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="e3604-323">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-323">**New behavior**</span></span>

<span data-ttu-id="e3604-324">从 EF Core 3.0 开始，`ILoggerFactory` 现已注册为作用域。</span><span class="sxs-lookup"><span data-stu-id="e3604-324">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="e3604-325">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-325">**Why**</span></span>

<span data-ttu-id="e3604-326">此更改是为了允许记录器与 `DbContext` 实例关联，从而启用其他功能并删除一些反常行为，例如内部服务提供商爆炸式增长的情况。</span><span class="sxs-lookup"><span data-stu-id="e3604-326">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="e3604-327">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-327">**Mitigations**</span></span>

<span data-ttu-id="e3604-328">除非在 EF Core 内部服务提供商上注册和使用自定义服务，否则此更改不应影响应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="e3604-328">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="e3604-329">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="e3604-329">This isn't common.</span></span>
<span data-ttu-id="e3604-330">在这些情况下，大多数事情仍然有效，但是需要更改依赖于 `ILoggerFactory` 的任何单一实例服务以便以不同的方式获取 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="e3604-330">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="e3604-331">如果遇到此类情况，请在 [EF Core GitHub 问题跟踪程序](https://github.com/aspnet/EntityFrameworkCore/issues)上提交一个问题，让我们知道你是如何使用 `ILoggerFactory` 的，以便我们更好地理解今后如何避免这种情况再次发生。</span><span class="sxs-lookup"><span data-stu-id="e3604-331">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="e3604-332">IDbContextOptionsExtensionWithDebugInfo 合并为 IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="e3604-332">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="e3604-333">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="e3604-333">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="e3604-334">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-334">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-335">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-335">**Old behavior**</span></span>

<span data-ttu-id="e3604-336">`IDbContextOptionsExtensionWithDebugInfo` 是从 `IDbContextOptionsExtension` 扩展的附加可选接口，以避免在 2.x 发布周期期间对接口进行重大更改。</span><span class="sxs-lookup"><span data-stu-id="e3604-336">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="e3604-337">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-337">**New behavior**</span></span>

<span data-ttu-id="e3604-338">接口现在合并到 `IDbContextOptionsExtension` 中。</span><span class="sxs-lookup"><span data-stu-id="e3604-338">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="e3604-339">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-339">**Why**</span></span>

<span data-ttu-id="e3604-340">之所以进行此更改，是因为所有接口在概念上是一个接口。</span><span class="sxs-lookup"><span data-stu-id="e3604-340">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="e3604-341">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-341">**Mitigations**</span></span>

<span data-ttu-id="e3604-342">任何 `IDbContextOptionsExtension` 实现都需要更新以支持新成员。</span><span class="sxs-lookup"><span data-stu-id="e3604-342">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="e3604-343">延迟加载代理不再假定导航属性已完全加载</span><span class="sxs-lookup"><span data-stu-id="e3604-343">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="e3604-344">跟踪问题 #12780</span><span class="sxs-lookup"><span data-stu-id="e3604-344">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="e3604-345">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="e3604-345">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="e3604-346">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-346">**Old behavior**</span></span>

<span data-ttu-id="e3604-347">在 EF Core 3.0 之前，一旦 `DbContext` 被处置，就无法知道从该上下文获得的实体上的给定导航属性是否已完全加载。</span><span class="sxs-lookup"><span data-stu-id="e3604-347">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="e3604-348">相反，如果导航有一个非 null 值，代理将假定加载一个引用导航，如果导航非空，则假定加载集合导航。</span><span class="sxs-lookup"><span data-stu-id="e3604-348">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="e3604-349">在这些情况下，尝试延迟加载将是无效的。</span><span class="sxs-lookup"><span data-stu-id="e3604-349">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="e3604-350">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-350">**New behavior**</span></span>

<span data-ttu-id="e3604-351">从 EF Core 3.0 开始，代理会跟踪是否加载了导航属性。</span><span class="sxs-lookup"><span data-stu-id="e3604-351">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="e3604-352">这意味着如果尝试访问在释放了上下文之后加载的导航属性，其结果始终是无操作，即使加载的导航为空或为 null。</span><span class="sxs-lookup"><span data-stu-id="e3604-352">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="e3604-353">相反，即使导航属性是非空集合，尝试访问未加载的导航属性也会引发异常。</span><span class="sxs-lookup"><span data-stu-id="e3604-353">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="e3604-354">如果出现这种情况，则表示应用程序代码在无效时间尝试使用延迟加载，应将应用程序更改为不执行此操作。</span><span class="sxs-lookup"><span data-stu-id="e3604-354">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="e3604-355">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-355">**Why**</span></span>

<span data-ttu-id="e3604-356">此更改是为了在尝试对已释放的 `DbContext` 实例进行延迟加载时使行为保持一致和正确。</span><span class="sxs-lookup"><span data-stu-id="e3604-356">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="e3604-357">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-357">**Mitigations**</span></span>

<span data-ttu-id="e3604-358">更新应用程序代码，以避免尝试对已释放的上下文进行延迟加载，或者将其配置为无操作，如异常消息中所述。</span><span class="sxs-lookup"><span data-stu-id="e3604-358">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="e3604-359">默认情况下，现在过度创建内部服务提供程序是一个错误</span><span class="sxs-lookup"><span data-stu-id="e3604-359">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="e3604-360">跟踪问题 #10236</span><span class="sxs-lookup"><span data-stu-id="e3604-360">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="e3604-361">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-361">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-362">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-362">**Old behavior**</span></span>

<span data-ttu-id="e3604-363">在 EF Core 3.0 之前，对于创建了大量内部服务提供程序的应用程序，将会记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="e3604-363">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="e3604-364">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-364">**New behavior**</span></span>

<span data-ttu-id="e3604-365">从 EF Core 3.0 开始，现在会考虑此警告，并引发错误和异常。</span><span class="sxs-lookup"><span data-stu-id="e3604-365">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="e3604-366">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-366">**Why**</span></span>

<span data-ttu-id="e3604-367">此更改是为了通过更明显地暴露这个病态案例来驱动生成更好的应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="e3604-367">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="e3604-368">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-368">**Mitigations**</span></span>

<span data-ttu-id="e3604-369">遇到此错误时，最合适的操作是了解根本原因并停止创建如此多的内部服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-369">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="e3604-370">但是，可以通过 `DbContextOptionsBuilder` 上的配置将错误转换回警告（或忽略）。</span><span class="sxs-lookup"><span data-stu-id="e3604-370">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="e3604-371">例如:</span><span class="sxs-lookup"><span data-stu-id="e3604-371">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="e3604-372">关系式：TypeMapping 注释现在只是 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="e3604-372">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="e3604-373">跟踪问题 #9913</span><span class="sxs-lookup"><span data-stu-id="e3604-373">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="e3604-374">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-374">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="e3604-375">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-375">**Old behavior**</span></span>

<span data-ttu-id="e3604-376">类型映射注释的注释名称是“Relational：TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="e3604-376">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="e3604-377">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-377">**New behavior**</span></span>

<span data-ttu-id="e3604-378">类型映射注释的注释名称现在是“TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="e3604-378">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="e3604-379">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-379">**Why**</span></span>

<span data-ttu-id="e3604-380">类型映射现在不仅用于关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="e3604-380">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="e3604-381">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-381">**Mitigations**</span></span>

<span data-ttu-id="e3604-382">这只会中断直接作为注释访问类型映射的应用程序，这不常见。</span><span class="sxs-lookup"><span data-stu-id="e3604-382">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="e3604-383">最合适的修复操作是使用 API 曲面来访问类型映射，而不是直接使用注释。</span><span class="sxs-lookup"><span data-stu-id="e3604-383">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="e3604-384">派生类型上的 ToTable 会引发异常</span><span class="sxs-lookup"><span data-stu-id="e3604-384">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="e3604-385">跟踪问题 #11811</span><span class="sxs-lookup"><span data-stu-id="e3604-385">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="e3604-386">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-386">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-387">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-387">**Old behavior**</span></span>

<span data-ttu-id="e3604-388">在 EF Core 3.0 之前，将忽略调用派生类型的 `ToTable()`，因为只有继承映射策略是 TPH，这是无效的。</span><span class="sxs-lookup"><span data-stu-id="e3604-388">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="e3604-389">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-389">**New behavior**</span></span>

<span data-ttu-id="e3604-390">从 EF Core 3.0 开始，同时为在以后的版本中添加 TPT 和 TPC 支持做准备，调用派生类型的 `ToTable()` 现在将引发异常，以避免将来发生意外的映射更改。</span><span class="sxs-lookup"><span data-stu-id="e3604-390">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="e3604-391">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-391">**Why**</span></span>

<span data-ttu-id="e3604-392">目前，将派生类型映射到不同的表是无效的。</span><span class="sxs-lookup"><span data-stu-id="e3604-392">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="e3604-393">这种改变避免了在将来当它变为有效时被中断。</span><span class="sxs-lookup"><span data-stu-id="e3604-393">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="e3604-394">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-394">**Mitigations**</span></span>

<span data-ttu-id="e3604-395">删除将派生类型映射到其他表的任何尝试。</span><span class="sxs-lookup"><span data-stu-id="e3604-395">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="e3604-396">用 HasIndex 替换 ForSqlServerHasIndex</span><span class="sxs-lookup"><span data-stu-id="e3604-396">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="e3604-397">跟踪问题 #12366</span><span class="sxs-lookup"><span data-stu-id="e3604-397">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="e3604-398">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-398">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-399">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-399">**Old behavior**</span></span>

<span data-ttu-id="e3604-400">在 EF Core 3.0 之前，`ForSqlServerHasIndex().ForSqlServerInclude()` 提供了一种配置与 `INCLUDE` 一起使用的列的方法。</span><span class="sxs-lookup"><span data-stu-id="e3604-400">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="e3604-401">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-401">**New behavior**</span></span>

<span data-ttu-id="e3604-402">从 EF Core 3.0 开始，现在支持在关系级别上对索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="e3604-402">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="e3604-403">请使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="e3604-403">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="e3604-404">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-404">**Why**</span></span>

<span data-ttu-id="e3604-405">此更改是为了将用于索引的 API 与 `Includes` 合并到一个位置，以供所有数据库提供程序使用。</span><span class="sxs-lookup"><span data-stu-id="e3604-405">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

<span data-ttu-id="e3604-406">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-406">**Mitigations**</span></span>

<span data-ttu-id="e3604-407">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="e3604-407">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="e3604-408">EF Core 不再发送 pragma 来执行 SQLite FK</span><span class="sxs-lookup"><span data-stu-id="e3604-408">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="e3604-409">跟踪问题 #12151</span><span class="sxs-lookup"><span data-stu-id="e3604-409">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="e3604-410">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="e3604-410">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="e3604-411">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-411">**Old behavior**</span></span>

<span data-ttu-id="e3604-412">在 EF Core 3.0 之前，当打开与 SQLite 的连接时，EF Core 会发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="e3604-412">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="e3604-413">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-413">**New behavior**</span></span>

<span data-ttu-id="e3604-414">从 EF Core 3.0 开始，当打开到 SQLite 的连接时，EF Core 不再发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="e3604-414">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="e3604-415">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-415">**Why**</span></span>

<span data-ttu-id="e3604-416">之所以进行此更改，是因为 EF Core 默认使用 `SQLitePCLRaw.bundle_e_sqlite3`，这意味着 FK 强制执行操作在默认情况下是打开的，并且不需要在每次打开连接时显式启用。</span><span class="sxs-lookup"><span data-stu-id="e3604-416">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="e3604-417">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-417">**Mitigations**</span></span>

<span data-ttu-id="e3604-418">默认情况下，SQLitePCLRaw.bundle_e_sqlite3 中启用了默认用于 EF Core 的外键。</span><span class="sxs-lookup"><span data-stu-id="e3604-418">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="e3604-419">对于其他情况，可以通过在连接字符串中指定 `Foreign Keys=True` 来启用外键。</span><span class="sxs-lookup"><span data-stu-id="e3604-419">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="e3604-420">Microsoft.EntityFrameworkCore.Sqlite 现在依赖于 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="e3604-420">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="e3604-421">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-421">**Old behavior**</span></span>

<span data-ttu-id="e3604-422">在 EF Core 3.0 之前，EF Core 使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="e3604-422">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="e3604-423">**新行为**</span><span class="sxs-lookup"><span data-stu-id="e3604-423">**New behavior**</span></span>

<span data-ttu-id="e3604-424">从 EF Core 3.0 开始，EF Core 使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="e3604-424">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="e3604-425">**为什么**</span><span class="sxs-lookup"><span data-stu-id="e3604-425">**Why**</span></span>

<span data-ttu-id="e3604-426">此更改是为了使 iOS 上使用的 SQLite 版本与其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="e3604-426">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="e3604-427">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="e3604-427">**Mitigations**</span></span>

<span data-ttu-id="e3604-428">若要在 iOS 上使用本机 SQLite 版本，请配置 `Microsoft.Data.Sqlite` 以使用其他 `SQLitePCLRaw` 捆绑包。</span><span class="sxs-lookup"><span data-stu-id="e3604-428">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>
