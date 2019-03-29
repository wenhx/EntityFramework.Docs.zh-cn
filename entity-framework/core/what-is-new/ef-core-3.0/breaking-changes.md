---
title: EF Core 3.0 中的中断性变更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 7ed55d4cae36f6b25059a5b218db4b0d5e2fb266
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419739"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="f3806-102">EF Core 3.0 中包含的中断性变更（目前处于预览状态）</span><span class="sxs-lookup"><span data-stu-id="f3806-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f3806-103">请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。</span><span class="sxs-lookup"><span data-stu-id="f3806-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="f3806-104">以下 API 和行为更改有可能在将 EF Core 2.2.x 升级到 3.0.0 时中断为其开发的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="f3806-105">我们将仅影响数据库提供程序的更改记录在[提供程序更改](../../providers/provider-log.md)下。</span><span class="sxs-lookup"><span data-stu-id="f3806-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="f3806-106">其中没有记录从一个 3.0 预览版引入到另一个 3.0 预览版的新功能的中断。</span><span class="sxs-lookup"><span data-stu-id="f3806-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="f3806-107">不再在客户端上计算 LINQ 查询</span><span class="sxs-lookup"><span data-stu-id="f3806-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="f3806-108">[跟踪问题 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另请参阅问题 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="f3806-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="f3806-109">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-110">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-110">**Old behavior**</span></span>

<span data-ttu-id="f3806-111">在 3.0 之前，当 EF Core 无法将查询中的表达式转换为 SQL 或参数时，它会在客户端上自动计算表达式的值。</span><span class="sxs-lookup"><span data-stu-id="f3806-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="f3806-112">默认情况下，客户端对潜在的昂贵表达式的计算仅触发警告。</span><span class="sxs-lookup"><span data-stu-id="f3806-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="f3806-113">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-113">**New behavior**</span></span>

<span data-ttu-id="f3806-114">从 3.0 开始，EF Core 仅允许在客户端上计算顶级投影中的表达式（查询中的最后一个 `Select()` 调用）。</span><span class="sxs-lookup"><span data-stu-id="f3806-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="f3806-115">当查询的任何其他部分中的表达式无法转换为 SQL 或参数时，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="f3806-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="f3806-116">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-116">**Why**</span></span>

<span data-ttu-id="f3806-117">自动的客户端查询计算允许执行许多查询，即使它们的重要组成部分无法转换。</span><span class="sxs-lookup"><span data-stu-id="f3806-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="f3806-118">此行为可能导致意外且具有潜在破坏性的行为，这些行为可能仅在生产中变得明显。</span><span class="sxs-lookup"><span data-stu-id="f3806-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="f3806-119">例如，`Where()` 调用中无法转换的条件可能导致表中的所有行从数据库服务器传输且筛选器应用于客户端。</span><span class="sxs-lookup"><span data-stu-id="f3806-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="f3806-120">如果在开发中表中只包含几行，则不容易检测到这种情况，但是当应用程序转入生产环节时，由于表中可能包含数百万行，这种情况会非常严重。</span><span class="sxs-lookup"><span data-stu-id="f3806-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="f3806-121">在开发过程中，客户端求值警告也很容易被忽视。</span><span class="sxs-lookup"><span data-stu-id="f3806-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="f3806-122">除此之外，自动客户端计算可能会导致问题，其中改进特定表达式的查询转换会导致版本之间发生意外中断性变更。</span><span class="sxs-lookup"><span data-stu-id="f3806-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="f3806-123">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-123">**Mitigations**</span></span>

<span data-ttu-id="f3806-124">如果无法完全转换查询，则以可转换的形式重写查询，或使用 `AsEnumerable()`、`ToList()` 或类似信息将数据显式返回客户端，然后可以进一步使用 LINQ 到对象处理。</span><span class="sxs-lookup"><span data-stu-id="f3806-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="f3806-125">Entity Framework Core 不再是 ASP.NET Core 共享框架的一部分</span><span class="sxs-lookup"><span data-stu-id="f3806-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="f3806-126">跟踪问题公告 #325</span><span class="sxs-lookup"><span data-stu-id="f3806-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="f3806-127">此更改是在 ASP.NET Core 3.0-preview 1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="f3806-128">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-128">**Old behavior**</span></span>

<span data-ttu-id="f3806-129">在 ASP.NET Core 3.0 之前，当向 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 添加包引用时，它将包括 EF Core 和一些 EF Core 数据提供程序（如 SQL Server 提供程序）。</span><span class="sxs-lookup"><span data-stu-id="f3806-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="f3806-130">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-130">**New behavior**</span></span>

<span data-ttu-id="f3806-131">从 3.0 开始，ASP.NET Core 共享框架不包括 EF Core 或任何 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="f3806-132">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-132">**Why**</span></span>

<span data-ttu-id="f3806-133">在此更改之前，获取 EF Core 需要不同的步骤，具体取决于应用程序是否是面向 ASP.NET Core 和 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="f3806-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="f3806-134">此外，升级 ASP.NET Core 会强制升级 EF Core 和 SQL Server 提供程序，这并不总是可取的。</span><span class="sxs-lookup"><span data-stu-id="f3806-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="f3806-135">通过此更改，通过所有提供程序、支持的 .NET 实现和应用程序类型获取 EF Core 的体验都是一致的。</span><span class="sxs-lookup"><span data-stu-id="f3806-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="f3806-136">开发人员现在还可以准确控制何时升级 EF Core 和 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="f3806-137">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-137">**Mitigations**</span></span>

<span data-ttu-id="f3806-138">若要在 ASP.NET Core 3.0 应用程序或任何其他受支持的应用程序中使用 EF Core，请显式添加对应用程序将使用的 EF Core 数据库提供程序的包引用。</span><span class="sxs-lookup"><span data-stu-id="f3806-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="f3806-139">在调试级别记录查询执行</span><span class="sxs-lookup"><span data-stu-id="f3806-139">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="f3806-140">跟踪问题 #14523</span><span class="sxs-lookup"><span data-stu-id="f3806-140">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="f3806-141">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-141">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-142">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-142">**Old behavior**</span></span>

<span data-ttu-id="f3806-143">EF Core 3.0 之前，是在 `Info` 级别记录查询和其他命令的执行。</span><span class="sxs-lookup"><span data-stu-id="f3806-143">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="f3806-144">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-144">**New behavior**</span></span>

<span data-ttu-id="f3806-145">从 EF Core 3.0 开始，将在 `Debug` 级别记录命令/SQL 的执行。</span><span class="sxs-lookup"><span data-stu-id="f3806-145">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="f3806-146">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-146">**Why**</span></span>

<span data-ttu-id="f3806-147">此更改是为了降低 `Info` 日志级别的噪音。</span><span class="sxs-lookup"><span data-stu-id="f3806-147">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="f3806-148">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-148">**Mitigations**</span></span>

<span data-ttu-id="f3806-149">此日志记录事件由 `RelationalEventId.CommandExecuting` 定义，事件 ID 为 20100。</span><span class="sxs-lookup"><span data-stu-id="f3806-149">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="f3806-150">若要再次在 `Info` 级别记录 SQL，请在 `OnConfiguring` 或 `AddDbContext` 中显式配置级别。</span><span class="sxs-lookup"><span data-stu-id="f3806-150">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="f3806-151">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-151">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="f3806-152">不再在实体实例上设置临时键值</span><span class="sxs-lookup"><span data-stu-id="f3806-152">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="f3806-153">跟踪问题 #12378</span><span class="sxs-lookup"><span data-stu-id="f3806-153">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="f3806-154">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-154">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="f3806-155">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-155">**Old behavior**</span></span>

<span data-ttu-id="f3806-156">在 EF Core 3.0 之前，临时值已分配给所有键属性，这些属性稍后将具有数据库生成的实际值。</span><span class="sxs-lookup"><span data-stu-id="f3806-156">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="f3806-157">通常这些临时值是较大负数。</span><span class="sxs-lookup"><span data-stu-id="f3806-157">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="f3806-158">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-158">**New behavior**</span></span>

<span data-ttu-id="f3806-159">从 3.0 开始，EF Core 将临时键值存储为实体跟踪信息的一部分，并保持键属性本身不变。</span><span class="sxs-lookup"><span data-stu-id="f3806-159">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="f3806-160">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-160">**Why**</span></span>

<span data-ttu-id="f3806-161">此更改是为了防止当之前由某个 `DbContext` 实例跟踪的实体移动到另一个 `DbContext` 实例时，临时键值错误地变成永久值。</span><span class="sxs-lookup"><span data-stu-id="f3806-161">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="f3806-162">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-162">**Mitigations**</span></span>

<span data-ttu-id="f3806-163">如果主键是存储生成的并且属于 `Added` 状态的实体，则将主键值分配到外键以在实体之间形成关联的应用可能会依赖于旧行为。</span><span class="sxs-lookup"><span data-stu-id="f3806-163">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="f3806-164">可通过以下方式避免：</span><span class="sxs-lookup"><span data-stu-id="f3806-164">This can be avoided by:</span></span>
* <span data-ttu-id="f3806-165">不使用存储生成的密钥。</span><span class="sxs-lookup"><span data-stu-id="f3806-165">Not using store-generated keys.</span></span>
* <span data-ttu-id="f3806-166">设置导航属性以形成关系，而不是设置外键值。</span><span class="sxs-lookup"><span data-stu-id="f3806-166">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="f3806-167">从实体的跟踪信息中获取实际的临时键值。</span><span class="sxs-lookup"><span data-stu-id="f3806-167">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="f3806-168">例如，即使 `blog.Id` 本身尚未设置，`context.Entry(blog).Property(e => e.Id).CurrentValue` 也将返回临时值。</span><span class="sxs-lookup"><span data-stu-id="f3806-168">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="f3806-169">DetectChanges 遵循存储生成的键值</span><span class="sxs-lookup"><span data-stu-id="f3806-169">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="f3806-170">跟踪问题 #14616</span><span class="sxs-lookup"><span data-stu-id="f3806-170">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="f3806-171">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-171">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-172">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-172">**Old behavior**</span></span>

<span data-ttu-id="f3806-173">在 EF Core 3.0 之前，`DetectChanges` 找到的未跟踪实体将在 `Added` 状态中被跟踪，并在调用 `SaveChanges` 时作为新行插入。</span><span class="sxs-lookup"><span data-stu-id="f3806-173">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="f3806-174">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-174">**New behavior**</span></span>

<span data-ttu-id="f3806-175">从 EF Core 3.0 开始，如果实体使用生成的键值并设置了某个键值，则将在 `Modified` 状态下跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="f3806-175">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="f3806-176">这意味着假定存在实体的行，并且在调用 `SaveChanges` 时将更新该行。</span><span class="sxs-lookup"><span data-stu-id="f3806-176">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="f3806-177">如果未设置键值，或者实体类型未使用生成的键，则新实体仍将像先前版本一样被作为 `Added` 跟踪。</span><span class="sxs-lookup"><span data-stu-id="f3806-177">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="f3806-178">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-178">**Why**</span></span>

<span data-ttu-id="f3806-179">进行此更改是为了在使用存储生成的键时更轻松、更一致地使用断开连接的实体图。</span><span class="sxs-lookup"><span data-stu-id="f3806-179">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="f3806-180">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-180">**Mitigations**</span></span>

<span data-ttu-id="f3806-181">如果将实体类型配置为使用生成的键，但为新实例显式设置了键值，则此更改可能会中断应用程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-181">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="f3806-182">解决方案是显式配置键属性，使其不使用生成的值。</span><span class="sxs-lookup"><span data-stu-id="f3806-182">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="f3806-183">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="f3806-183">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="f3806-184">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="f3806-184">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="f3806-185">默认情况下，现在会立即发生级联删除</span><span class="sxs-lookup"><span data-stu-id="f3806-185">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="f3806-186">跟踪问题 #10114</span><span class="sxs-lookup"><span data-stu-id="f3806-186">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="f3806-187">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-188">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-188">**Old behavior**</span></span>

<span data-ttu-id="f3806-189">在 3.0 之前，直到调用 SaveChanges 时，EF Core 才会应用级联操作（删除所需主体时或者在切断与所需主体的关系时删除依赖实体）。</span><span class="sxs-lookup"><span data-stu-id="f3806-189">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="f3806-190">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-190">**New behavior**</span></span>

<span data-ttu-id="f3806-191">从 3.0 开始，一旦检测到触发条件，EF Core 就会应用级联操作。</span><span class="sxs-lookup"><span data-stu-id="f3806-191">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="f3806-192">例如，调用 `context.Remove()` 来删除主体实体将导致所有跟踪的相关必需依赖项也立即设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="f3806-192">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="f3806-193">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-193">**Why**</span></span>

<span data-ttu-id="f3806-194">此更改是为了改善数据绑定和审核方案的体验，在相关体验中，需要了解在调用 `SaveChanges` _之前_会删除哪些实体。</span><span class="sxs-lookup"><span data-stu-id="f3806-194">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="f3806-195">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-195">**Mitigations**</span></span>

<span data-ttu-id="f3806-196">可以通过 `context.ChangedTracker` 上的设置还原以前的行为。</span><span class="sxs-lookup"><span data-stu-id="f3806-196">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="f3806-197">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-197">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="f3806-198">查询类型与实体类型合并</span><span class="sxs-lookup"><span data-stu-id="f3806-198">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="f3806-199">跟踪问题 #14194</span><span class="sxs-lookup"><span data-stu-id="f3806-199">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="f3806-200">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-200">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-201">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-201">**Old behavior**</span></span>

<span data-ttu-id="f3806-202">在 EF Core 3.0 之前，[查询类型](xref:core/modeling/query-types)是一种查询未以结构化方式定义主键的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="f3806-202">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="f3806-203">也就是说，查询类型用于映射没有键的实体类型（更可能来自视图，但也可能来自表），而当有可用的键时则使用常规实体类型（更可能来自表，但也可能来自视图）。</span><span class="sxs-lookup"><span data-stu-id="f3806-203">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="f3806-204">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-204">**New behavior**</span></span>

<span data-ttu-id="f3806-205">现在，查询类型只是一个没有主键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="f3806-205">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="f3806-206">无键实体类型与先前版本中的查询类型具有相同的功能。</span><span class="sxs-lookup"><span data-stu-id="f3806-206">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="f3806-207">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-207">**Why**</span></span>

<span data-ttu-id="f3806-208">这样做是为了减少对查询类型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="f3806-208">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="f3806-209">具体来说，它们是无键实体类型，因此本质上是只读的，但是不应该仅仅因为实体类型需要是只读的就使用它们。</span><span class="sxs-lookup"><span data-stu-id="f3806-209">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="f3806-210">同样，它们通常映射到视图，但这只是因为视图通常不定义键。</span><span class="sxs-lookup"><span data-stu-id="f3806-210">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="f3806-211">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-211">**Mitigations**</span></span>

<span data-ttu-id="f3806-212">API 的以下部分现已过时：</span><span class="sxs-lookup"><span data-stu-id="f3806-212">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="f3806-213">**`ModelBuilder.Query<>()`** - 需要调用 `ModelBuilder.Entity<>().HasNoKey()` 来将实体类型标记为没有键。</span><span class="sxs-lookup"><span data-stu-id="f3806-213">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="f3806-214">这仍然不会按约定配置，以避免在需要主键但是与约定不匹配时发生配置错误。</span><span class="sxs-lookup"><span data-stu-id="f3806-214">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="f3806-215">**`DbQuery<>`** - 应使用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="f3806-215">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="f3806-216">**`DbContext.Query<>()`** - 应使用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="f3806-216">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="f3806-217">从属类型关系的配置 API 已更改</span><span class="sxs-lookup"><span data-stu-id="f3806-217">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="f3806-218">[跟踪问题 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[跟踪问题 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[跟踪问题 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="f3806-218">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="f3806-219">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-219">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-220">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-220">**Old behavior**</span></span>

<span data-ttu-id="f3806-221">EF Core 3.0 之前，会在 `OwnsOne` 或 `OwnsMany` 调用之后直接执行所拥有关系的配置。</span><span class="sxs-lookup"><span data-stu-id="f3806-221">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="f3806-222">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-222">**New behavior**</span></span>

<span data-ttu-id="f3806-223">从 EF Core 3.0 开始，Fluent API 会使用 `WithOwner()` 为所有者配置导航属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-223">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="f3806-224">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-224">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="f3806-225">与所有者之间关系相关的配置现会在 `WithOwner()` 之后关联起来，就像配置其他关系一样。</span><span class="sxs-lookup"><span data-stu-id="f3806-225">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="f3806-226">但从属类型本身的配置仍会在 `OwnsOne()/OwnsMany()` 之后关联。</span><span class="sxs-lookup"><span data-stu-id="f3806-226">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="f3806-227">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-227">For example:</span></span>

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

<span data-ttu-id="f3806-228">此外，使用固有类型目标调用 `Entity()``HasOne()` 或 `Set()` 现将引发异常。</span><span class="sxs-lookup"><span data-stu-id="f3806-228">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="f3806-229">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-229">**Why**</span></span>

<span data-ttu-id="f3806-230">此更改是为了更清晰的区分从属类型本身的配置和与从属类型的_关系_的配置。</span><span class="sxs-lookup"><span data-stu-id="f3806-230">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="f3806-231">这反过来消除了诸如 `HasForeignKey` 之类的方法的模糊性和混淆。</span><span class="sxs-lookup"><span data-stu-id="f3806-231">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="f3806-232">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-232">**Mitigations**</span></span>

<span data-ttu-id="f3806-233">更改从属类型关系的配置以使用新的 API 曲面，如上例所示。</span><span class="sxs-lookup"><span data-stu-id="f3806-233">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="f3806-234">外键属性约定不再匹配与主体属性相同的名称</span><span class="sxs-lookup"><span data-stu-id="f3806-234">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="f3806-235">跟踪问题 #13274</span><span class="sxs-lookup"><span data-stu-id="f3806-235">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="f3806-236">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-236">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-237">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-237">**Old behavior**</span></span>

<span data-ttu-id="f3806-238">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="f3806-238">Consider the following model:</span></span>
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
<span data-ttu-id="f3806-239">在 EF Core 3.0 之前，`CustomerId` 属性将按约定用于外键。</span><span class="sxs-lookup"><span data-stu-id="f3806-239">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="f3806-240">但是，如果 `Order` 是从属类型，那么这也会使 `CustomerId` 成为主键，这通常不是预期结果。</span><span class="sxs-lookup"><span data-stu-id="f3806-240">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="f3806-241">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-241">**New behavior**</span></span>

<span data-ttu-id="f3806-242">从 3.0 开始，如果外键具有与主体属性相同的名称，则 EF Core 不会尝试按约定使用外键属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-242">Starting with 3.0, EF Core won't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="f3806-243">与主体属性名称关联的主体类型名称和与主体属性名称模式关联的导航名称仍然相匹配。</span><span class="sxs-lookup"><span data-stu-id="f3806-243">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="f3806-244">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-244">For example:</span></span>

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

<span data-ttu-id="f3806-245">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-245">**Why**</span></span>

<span data-ttu-id="f3806-246">此更改是为了避免错误地在从属类型上定义主键属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-246">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="f3806-247">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-247">**Mitigations**</span></span>

<span data-ttu-id="f3806-248">如果该属性将成为外键，即为主键的一部分，则显式配置它。</span><span class="sxs-lookup"><span data-stu-id="f3806-248">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="f3806-249">每个属性使用独立的内存中整数键生成</span><span class="sxs-lookup"><span data-stu-id="f3806-249">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="f3806-250">跟踪问题 #6872</span><span class="sxs-lookup"><span data-stu-id="f3806-250">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="f3806-251">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-251">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-252">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-252">**Old behavior**</span></span>

<span data-ttu-id="f3806-253">在 EF Core 3.0 之前，一个共享值生成器用于所有内存中的整数键属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-253">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="f3806-254">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-254">**New behavior**</span></span>

<span data-ttu-id="f3806-255">从 EF Core 3.0 开始，每个整数键属性在使用内存数据库时都会获取其自己的值生成器。</span><span class="sxs-lookup"><span data-stu-id="f3806-255">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="f3806-256">此外，如果删除了数据库，则会为所有表重置键生成。</span><span class="sxs-lookup"><span data-stu-id="f3806-256">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="f3806-257">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-257">**Why**</span></span>

<span data-ttu-id="f3806-258">此更改的目的是使内存中的键生成更接近于实际的数据库键生成，并改进在使用内存中的数据库时隔离测试的功能。</span><span class="sxs-lookup"><span data-stu-id="f3806-258">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="f3806-259">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-259">**Mitigations**</span></span>

<span data-ttu-id="f3806-260">这可能会中断依赖于特定内存中键值的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-260">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="f3806-261">请考虑不依赖于特定键值，或者进行更新以匹配新行为。</span><span class="sxs-lookup"><span data-stu-id="f3806-261">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="f3806-262">默认情况下使用支持字段</span><span class="sxs-lookup"><span data-stu-id="f3806-262">Backing fields are used by default</span></span>

[<span data-ttu-id="f3806-263">跟踪问题 #12430</span><span class="sxs-lookup"><span data-stu-id="f3806-263">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="f3806-264">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-264">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="f3806-265">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-265">**Old behavior**</span></span>

<span data-ttu-id="f3806-266">在 3.0 之前，即使已知属性的支持字段，EF Core 仍将默认使用属性 getter 和 setter 方法读取和写入属性值。</span><span class="sxs-lookup"><span data-stu-id="f3806-266">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="f3806-267">例外情况是查询执行，如果已知，将直接设置支持字段。</span><span class="sxs-lookup"><span data-stu-id="f3806-267">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="f3806-268">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-268">**New behavior**</span></span>

<span data-ttu-id="f3806-269">从 EF Core 3.0 开始，如果已知属性的支持字段，则始终使用支持字段读取和写入该属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-269">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="f3806-270">如果应用程序依赖于编码到 getter 或 setter 方法中的其他行为，则可能导致应用程序中断。</span><span class="sxs-lookup"><span data-stu-id="f3806-270">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="f3806-271">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-271">**Why**</span></span>

<span data-ttu-id="f3806-272">此更改是为了防止 EF Core 在执行涉及实体的数据库操作时默认错误地触发业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="f3806-272">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="f3806-273">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-273">**Mitigations**</span></span>

<span data-ttu-id="f3806-274">可以通过在 modelBuilder fluent API 中配置属性访问模式来恢复 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="f3806-274">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="f3806-275">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-275">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="f3806-276">如果找到多个兼容的支持字段，则引发</span><span class="sxs-lookup"><span data-stu-id="f3806-276">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="f3806-277">跟踪问题 #12523</span><span class="sxs-lookup"><span data-stu-id="f3806-277">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="f3806-278">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-279">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-279">**Old behavior**</span></span>

<span data-ttu-id="f3806-280">在 EF Core 3.0 之前，如果多个字段与查找属性的后备字段的规则匹配，则将基于某种优先顺序选择一个字段。</span><span class="sxs-lookup"><span data-stu-id="f3806-280">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="f3806-281">这可能导致在不明确的情况下使用错误字段。</span><span class="sxs-lookup"><span data-stu-id="f3806-281">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="f3806-282">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-282">**New behavior**</span></span>

<span data-ttu-id="f3806-283">从 EF Core 3.0 开始，如果多个字段与同一属性匹配，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="f3806-283">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="f3806-284">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-284">**Why**</span></span>

<span data-ttu-id="f3806-285">此更改是为了避免在只有一个字段是正确的情况下无提示地使用另一个字段。</span><span class="sxs-lookup"><span data-stu-id="f3806-285">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="f3806-286">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-286">**Mitigations**</span></span>

<span data-ttu-id="f3806-287">具有不明确的支持字段的属性必须具有显式指定的字段。</span><span class="sxs-lookup"><span data-stu-id="f3806-287">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="f3806-288">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="f3806-288">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="f3806-289">AddDbContext/AddDbContextPool 不再调用 AddLogging 和 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="f3806-289">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="f3806-290">跟踪问题 #14756</span><span class="sxs-lookup"><span data-stu-id="f3806-290">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="f3806-291">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-291">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-292">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-292">**Old behavior**</span></span>

<span data-ttu-id="f3806-293">在 EF Core 3.0 之前，调用 `AddDbContext` 或 `AddDbContextPool` 的操作也可以通过调用 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 和 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 在 DI 中注册日志记录和内存缓存服务。</span><span class="sxs-lookup"><span data-stu-id="f3806-293">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="f3806-294">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-294">**New behavior**</span></span>

<span data-ttu-id="f3806-295">从 EF Core 3.0 开始，`AddDbContext` 和 `AddDbContextPool` 将无法再在依赖注入 (DI) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="f3806-295">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="f3806-296">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-296">**Why**</span></span>

<span data-ttu-id="f3806-297">EF Core 3.0 不要求这些服务位于应用程序的 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="f3806-297">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="f3806-298">但是，如果 `ILoggerFactory` 在应用程序的 DI 容器中注册，它仍然会由 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="f3806-298">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="f3806-299">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-299">**Mitigations**</span></span>

<span data-ttu-id="f3806-300">如果应用程序需要这些服务，则使用 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 将它们显式注册到 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="f3806-300">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="f3806-301">DbContext.Entry 现在执行本地 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="f3806-301">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="f3806-302">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="f3806-302">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="f3806-303">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-303">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-304">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-304">**Old behavior**</span></span>

<span data-ttu-id="f3806-305">在 EF Core 3.0 之前，调用 `DbContext.Entry` 将导致检测到所有被跟踪实体的更改。</span><span class="sxs-lookup"><span data-stu-id="f3806-305">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="f3806-306">这确保了 `EntityEntry` 中暴露的状态是最新的。</span><span class="sxs-lookup"><span data-stu-id="f3806-306">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="f3806-307">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-307">**New behavior**</span></span>

<span data-ttu-id="f3806-308">从 EF Core 3.0 开始，调用 `DbContext.Entry` 现在只会尝试检测给定实体和与之相关的任何跟踪主体实体的更改。</span><span class="sxs-lookup"><span data-stu-id="f3806-308">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="f3806-309">这意味着可能无法通过调用此方法检测到其他位置的更改，这可能会影响应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="f3806-309">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="f3806-310">请注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 设置为 `false`，则即使是本地更改检测也将被禁用。</span><span class="sxs-lookup"><span data-stu-id="f3806-310">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="f3806-311">导致更改检测的其他方法（例如 `ChangeTracker.Entries` 和 `SaveChanges`）仍然会导致所有被跟踪实体的完整 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="f3806-311">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="f3806-312">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-312">**Why**</span></span>

<span data-ttu-id="f3806-313">此更改是为了提高使用 `context.Entry` 的默认性能。</span><span class="sxs-lookup"><span data-stu-id="f3806-313">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="f3806-314">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-314">**Mitigations**</span></span>

<span data-ttu-id="f3806-315">在调用 `Entry` 之前显式调用 `ChgangeTracker.DetectChanges()` 以确保 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="f3806-315">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="f3806-316">默认情况下，字符串和字节数组键不是客户端生成的</span><span class="sxs-lookup"><span data-stu-id="f3806-316">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="f3806-317">跟踪问题 #14617</span><span class="sxs-lookup"><span data-stu-id="f3806-317">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="f3806-318">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-318">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-319">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-319">**Old behavior**</span></span>

<span data-ttu-id="f3806-320">在 EF Core 3.0 之前，可以使用 `string` 和 `byte[]` 键属性，而不需要显式地设置非 null 值。</span><span class="sxs-lookup"><span data-stu-id="f3806-320">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="f3806-321">在这种情况下，键值将在客户端上生成为 GUID，并序列化为 `byte[]` 的字节。</span><span class="sxs-lookup"><span data-stu-id="f3806-321">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="f3806-322">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-322">**New behavior**</span></span>

<span data-ttu-id="f3806-323">从 EF Core 3.0 开始，将引发异常，指示未设置任何键值。</span><span class="sxs-lookup"><span data-stu-id="f3806-323">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="f3806-324">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-324">**Why**</span></span>

<span data-ttu-id="f3806-325">之所以进行此更改是因为客户端生成的 `string`/`byte[]` 值通常没有用，并且默认行为使得很难以通用方式推断生成的键值。</span><span class="sxs-lookup"><span data-stu-id="f3806-325">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="f3806-326">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-326">**Mitigations**</span></span>

<span data-ttu-id="f3806-327">如果没有设置其他非 null 值，则可以通过显式指定键属性应使用生成的值来获得 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="f3806-327">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="f3806-328">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="f3806-328">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="f3806-329">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="f3806-329">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="f3806-330">ILoggerFactory 现在是一个在一定范围内有效的服务</span><span class="sxs-lookup"><span data-stu-id="f3806-330">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="f3806-331">跟踪问题 #14698</span><span class="sxs-lookup"><span data-stu-id="f3806-331">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="f3806-332">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-332">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-333">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-333">**Old behavior**</span></span>

<span data-ttu-id="f3806-334">在 EF Core 3.0 之前，`ILoggerFactory` 被注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="f3806-334">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="f3806-335">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-335">**New behavior**</span></span>

<span data-ttu-id="f3806-336">从 EF Core 3.0 开始，`ILoggerFactory` 现已注册为作用域。</span><span class="sxs-lookup"><span data-stu-id="f3806-336">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="f3806-337">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-337">**Why**</span></span>

<span data-ttu-id="f3806-338">此更改是为了允许记录器与 `DbContext` 实例关联，从而启用其他功能并删除一些反常行为，例如内部服务提供商爆炸式增长的情况。</span><span class="sxs-lookup"><span data-stu-id="f3806-338">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="f3806-339">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-339">**Mitigations**</span></span>

<span data-ttu-id="f3806-340">除非在 EF Core 内部服务提供商上注册和使用自定义服务，否则此更改不应影响应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="f3806-340">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="f3806-341">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="f3806-341">This isn't common.</span></span>
<span data-ttu-id="f3806-342">在这些情况下，大多数事情仍然有效，但是需要更改依赖于 `ILoggerFactory` 的任何单一实例服务以便以不同的方式获取 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="f3806-342">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="f3806-343">如果遇到此类情况，请在 [EF Core GitHub 问题跟踪程序](https://github.com/aspnet/EntityFrameworkCore/issues)上提交一个问题，让我们知道你是如何使用 `ILoggerFactory` 的，以便我们更好地理解今后如何避免这种情况再次发生。</span><span class="sxs-lookup"><span data-stu-id="f3806-343">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="f3806-344">IDbContextOptionsExtensionWithDebugInfo 合并为 IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="f3806-344">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="f3806-345">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="f3806-345">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="f3806-346">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-346">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-347">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-347">**Old behavior**</span></span>

<span data-ttu-id="f3806-348">`IDbContextOptionsExtensionWithDebugInfo` 是从 `IDbContextOptionsExtension` 扩展的附加可选接口，以避免在 2.x 发布周期期间对接口进行重大更改。</span><span class="sxs-lookup"><span data-stu-id="f3806-348">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="f3806-349">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-349">**New behavior**</span></span>

<span data-ttu-id="f3806-350">接口现在合并到 `IDbContextOptionsExtension` 中。</span><span class="sxs-lookup"><span data-stu-id="f3806-350">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="f3806-351">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-351">**Why**</span></span>

<span data-ttu-id="f3806-352">之所以进行此更改，是因为所有接口在概念上是一个接口。</span><span class="sxs-lookup"><span data-stu-id="f3806-352">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="f3806-353">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-353">**Mitigations**</span></span>

<span data-ttu-id="f3806-354">任何 `IDbContextOptionsExtension` 实现都需要更新以支持新成员。</span><span class="sxs-lookup"><span data-stu-id="f3806-354">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="f3806-355">延迟加载代理不再假定导航属性已完全加载</span><span class="sxs-lookup"><span data-stu-id="f3806-355">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="f3806-356">跟踪问题 #12780</span><span class="sxs-lookup"><span data-stu-id="f3806-356">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="f3806-357">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-357">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-358">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-358">**Old behavior**</span></span>

<span data-ttu-id="f3806-359">在 EF Core 3.0 之前，一旦 `DbContext` 被处置，就无法知道从该上下文获得的实体上的给定导航属性是否已完全加载。</span><span class="sxs-lookup"><span data-stu-id="f3806-359">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="f3806-360">相反，如果导航有一个非 null 值，代理将假定加载一个引用导航，如果导航非空，则假定加载集合导航。</span><span class="sxs-lookup"><span data-stu-id="f3806-360">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="f3806-361">在这些情况下，尝试延迟加载将是无效的。</span><span class="sxs-lookup"><span data-stu-id="f3806-361">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="f3806-362">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-362">**New behavior**</span></span>

<span data-ttu-id="f3806-363">从 EF Core 3.0 开始，代理会跟踪是否加载了导航属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-363">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="f3806-364">这意味着如果尝试访问在释放了上下文之后加载的导航属性，其结果始终是无操作，即使加载的导航为空或为 null。</span><span class="sxs-lookup"><span data-stu-id="f3806-364">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="f3806-365">相反，即使导航属性是非空集合，尝试访问未加载的导航属性也会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f3806-365">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="f3806-366">如果出现这种情况，则表示应用程序代码在无效时间尝试使用延迟加载，应将应用程序更改为不执行此操作。</span><span class="sxs-lookup"><span data-stu-id="f3806-366">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="f3806-367">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-367">**Why**</span></span>

<span data-ttu-id="f3806-368">此更改是为了在尝试对已释放的 `DbContext` 实例进行延迟加载时使行为保持一致和正确。</span><span class="sxs-lookup"><span data-stu-id="f3806-368">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="f3806-369">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-369">**Mitigations**</span></span>

<span data-ttu-id="f3806-370">更新应用程序代码，以避免尝试对已释放的上下文进行延迟加载，或者将其配置为无操作，如异常消息中所述。</span><span class="sxs-lookup"><span data-stu-id="f3806-370">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="f3806-371">默认情况下，现在过度创建内部服务提供程序是一个错误</span><span class="sxs-lookup"><span data-stu-id="f3806-371">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="f3806-372">跟踪问题 #10236</span><span class="sxs-lookup"><span data-stu-id="f3806-372">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="f3806-373">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-373">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-374">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-374">**Old behavior**</span></span>

<span data-ttu-id="f3806-375">在 EF Core 3.0 之前，对于创建了大量内部服务提供程序的应用程序，将会记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="f3806-375">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="f3806-376">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-376">**New behavior**</span></span>

<span data-ttu-id="f3806-377">从 EF Core 3.0 开始，现在会考虑此警告，并引发错误和异常。</span><span class="sxs-lookup"><span data-stu-id="f3806-377">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="f3806-378">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-378">**Why**</span></span>

<span data-ttu-id="f3806-379">此更改是为了通过更明显地暴露这个病态案例来驱动生成更好的应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="f3806-379">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="f3806-380">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-380">**Mitigations**</span></span>

<span data-ttu-id="f3806-381">遇到此错误时，最合适的操作是了解根本原因并停止创建如此多的内部服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-381">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="f3806-382">但是，可以通过 `DbContextOptionsBuilder` 上的配置将错误转换回警告（或忽略）。</span><span class="sxs-lookup"><span data-stu-id="f3806-382">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="f3806-383">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-383">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="f3806-384">使用单个字符串调用 HasOne/HasMany 的新行为</span><span class="sxs-lookup"><span data-stu-id="f3806-384">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="f3806-385">跟踪问题 #9171</span><span class="sxs-lookup"><span data-stu-id="f3806-385">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="f3806-386">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="f3806-386">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-387">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-387">**Old behavior**</span></span>

<span data-ttu-id="f3806-388">在 EF Core 3.0 之前，对通过单个字符串调用 `HasOne` 或 `HasMany` 的代码的解释令人困惑。</span><span class="sxs-lookup"><span data-stu-id="f3806-388">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="f3806-389">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-389">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="f3806-390">该代码看起来是使用 `Entrance` 导航属性（可能是私有属性）将 `Samurai` 与某些其他实体类型关联起来的。</span><span class="sxs-lookup"><span data-stu-id="f3806-390">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="f3806-391">实际上，此代码试图创建与某个名为 `Entrance` 的实体类型的关系，该实体类型没有导航属性。</span><span class="sxs-lookup"><span data-stu-id="f3806-391">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="f3806-392">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-392">**New behavior**</span></span>

<span data-ttu-id="f3806-393">从 EF Core 3.0 开始，上面的代码现执行它以前应执行的操作。</span><span class="sxs-lookup"><span data-stu-id="f3806-393">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="f3806-394">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-394">**Why**</span></span>

<span data-ttu-id="f3806-395">这一旧行为令人非常困惑，尤其是在读取配置代码和查找错误时。</span><span class="sxs-lookup"><span data-stu-id="f3806-395">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="f3806-396">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-396">**Mitigations**</span></span>

<span data-ttu-id="f3806-397">这只会中断使用类型名称字符串显式配置关系而无需显式指定导航属性的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-397">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="f3806-398">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="f3806-398">This is not common.</span></span>
<span data-ttu-id="f3806-399">以前的行为可以通过显式传递导航属性名称的 `null` 获得。</span><span class="sxs-lookup"><span data-stu-id="f3806-399">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="f3806-400">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-400">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="f3806-401">关系式：TypeMapping 注释现在只是 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="f3806-401">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="f3806-402">跟踪问题 #9913</span><span class="sxs-lookup"><span data-stu-id="f3806-402">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="f3806-403">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-403">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="f3806-404">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-404">**Old behavior**</span></span>

<span data-ttu-id="f3806-405">类型映射注释的注释名称是“Relational：TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="f3806-405">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="f3806-406">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-406">**New behavior**</span></span>

<span data-ttu-id="f3806-407">类型映射注释的注释名称现在是“TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="f3806-407">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="f3806-408">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-408">**Why**</span></span>

<span data-ttu-id="f3806-409">类型映射现在不仅用于关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="f3806-409">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="f3806-410">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-410">**Mitigations**</span></span>

<span data-ttu-id="f3806-411">这只会中断直接作为注释访问类型映射的应用程序，这不常见。</span><span class="sxs-lookup"><span data-stu-id="f3806-411">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="f3806-412">最合适的修复操作是使用 API 曲面来访问类型映射，而不是直接使用注释。</span><span class="sxs-lookup"><span data-stu-id="f3806-412">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="f3806-413">派生类型上的 ToTable 会引发异常</span><span class="sxs-lookup"><span data-stu-id="f3806-413">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="f3806-414">跟踪问题 #11811</span><span class="sxs-lookup"><span data-stu-id="f3806-414">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="f3806-415">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-415">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-416">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-416">**Old behavior**</span></span>

<span data-ttu-id="f3806-417">在 EF Core 3.0 之前，将忽略调用派生类型的 `ToTable()`，因为只有继承映射策略是 TPH，这是无效的。</span><span class="sxs-lookup"><span data-stu-id="f3806-417">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="f3806-418">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-418">**New behavior**</span></span>

<span data-ttu-id="f3806-419">从 EF Core 3.0 开始，同时为在以后的版本中添加 TPT 和 TPC 支持做准备，调用派生类型的 `ToTable()` 现在将引发异常，以避免将来发生意外的映射更改。</span><span class="sxs-lookup"><span data-stu-id="f3806-419">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="f3806-420">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-420">**Why**</span></span>

<span data-ttu-id="f3806-421">目前，将派生类型映射到不同的表是无效的。</span><span class="sxs-lookup"><span data-stu-id="f3806-421">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="f3806-422">这种改变避免了在将来当它变为有效时被中断。</span><span class="sxs-lookup"><span data-stu-id="f3806-422">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="f3806-423">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-423">**Mitigations**</span></span>

<span data-ttu-id="f3806-424">删除将派生类型映射到其他表的任何尝试。</span><span class="sxs-lookup"><span data-stu-id="f3806-424">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="f3806-425">用 HasIndex 替换 ForSqlServerHasIndex</span><span class="sxs-lookup"><span data-stu-id="f3806-425">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="f3806-426">跟踪问题 #12366</span><span class="sxs-lookup"><span data-stu-id="f3806-426">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="f3806-427">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-427">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-428">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-428">**Old behavior**</span></span>

<span data-ttu-id="f3806-429">在 EF Core 3.0 之前，`ForSqlServerHasIndex().ForSqlServerInclude()` 提供了一种配置与 `INCLUDE` 一起使用的列的方法。</span><span class="sxs-lookup"><span data-stu-id="f3806-429">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="f3806-430">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-430">**New behavior**</span></span>

<span data-ttu-id="f3806-431">从 EF Core 3.0 开始，现在支持在关系级别上对索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="f3806-431">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="f3806-432">请使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="f3806-432">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="f3806-433">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-433">**Why**</span></span>

<span data-ttu-id="f3806-434">此更改是为了将用于索引的 API 与 `Includes` 合并到一个位置，以供所有数据库提供程序使用。</span><span class="sxs-lookup"><span data-stu-id="f3806-434">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

<span data-ttu-id="f3806-435">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-435">**Mitigations**</span></span>

<span data-ttu-id="f3806-436">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="f3806-436">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="f3806-437">EF Core 不再发送 pragma 来执行 SQLite FK</span><span class="sxs-lookup"><span data-stu-id="f3806-437">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="f3806-438">跟踪问题 #12151</span><span class="sxs-lookup"><span data-stu-id="f3806-438">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="f3806-439">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-439">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="f3806-440">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-440">**Old behavior**</span></span>

<span data-ttu-id="f3806-441">在 EF Core 3.0 之前，当打开与 SQLite 的连接时，EF Core 会发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="f3806-441">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="f3806-442">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-442">**New behavior**</span></span>

<span data-ttu-id="f3806-443">从 EF Core 3.0 开始，当打开到 SQLite 的连接时，EF Core 不再发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="f3806-443">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="f3806-444">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-444">**Why**</span></span>

<span data-ttu-id="f3806-445">之所以进行此更改，是因为 EF Core 默认使用 `SQLitePCLRaw.bundle_e_sqlite3`，这意味着 FK 强制执行操作在默认情况下是打开的，并且不需要在每次打开连接时显式启用。</span><span class="sxs-lookup"><span data-stu-id="f3806-445">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="f3806-446">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-446">**Mitigations**</span></span>

<span data-ttu-id="f3806-447">默认情况下，SQLitePCLRaw.bundle_e_sqlite3 中启用了默认用于 EF Core 的外键。</span><span class="sxs-lookup"><span data-stu-id="f3806-447">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="f3806-448">对于其他情况，可以通过在连接字符串中指定 `Foreign Keys=True` 来启用外键。</span><span class="sxs-lookup"><span data-stu-id="f3806-448">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="f3806-449">Microsoft.EntityFrameworkCore.Sqlite 现在依赖于 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="f3806-449">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="f3806-450">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-450">**Old behavior**</span></span>

<span data-ttu-id="f3806-451">在 EF Core 3.0 之前，EF Core 使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="f3806-451">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="f3806-452">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-452">**New behavior**</span></span>

<span data-ttu-id="f3806-453">从 EF Core 3.0 开始，EF Core 使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="f3806-453">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="f3806-454">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-454">**Why**</span></span>

<span data-ttu-id="f3806-455">此更改是为了使 iOS 上使用的 SQLite 版本与其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="f3806-455">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="f3806-456">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-456">**Mitigations**</span></span>

<span data-ttu-id="f3806-457">若要在 iOS 上使用本机 SQLite 版本，请配置 `Microsoft.Data.Sqlite` 以使用其他 `SQLitePCLRaw` 捆绑包。</span><span class="sxs-lookup"><span data-stu-id="f3806-457">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="f3806-458">GUID 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="f3806-458">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="f3806-459">跟踪问题 #15078</span><span class="sxs-lookup"><span data-stu-id="f3806-459">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="f3806-460">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-460">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-461">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-461">**Old behavior**</span></span>

<span data-ttu-id="f3806-462">GUID 值之前以 BLOB 值形式存储在 SQLite 上。</span><span class="sxs-lookup"><span data-stu-id="f3806-462">Guid values were previously sored as BLOB values on SQLite.</span></span>

<span data-ttu-id="f3806-463">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-463">**New behavior**</span></span>

<span data-ttu-id="f3806-464">GUID 值现在以文本形式存储。</span><span class="sxs-lookup"><span data-stu-id="f3806-464">Guid values are now sotred as TEXT.</span></span>

<span data-ttu-id="f3806-465">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-465">**Why**</span></span>

<span data-ttu-id="f3806-466">GUID 的二进制格式不会进行标准化。</span><span class="sxs-lookup"><span data-stu-id="f3806-466">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="f3806-467">以文本形式存储值使数据库与其他技术更兼容。</span><span class="sxs-lookup"><span data-stu-id="f3806-467">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="f3806-468">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-468">**Mitigations**</span></span>

<span data-ttu-id="f3806-469">现在通过执行如下的 SQL，可以将现有数据库转成新的格式。</span><span class="sxs-lookup"><span data-stu-id="f3806-469">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
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

<span data-ttu-id="f3806-470">在 EF Core 中，通过对这些属性配置值转换器，还可以使用以前的行为模式继续操作。</span><span class="sxs-lookup"><span data-stu-id="f3806-470">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="f3806-471">Microsoft.Data.Sqlite 仍然能够从“BLOB”和“文本”列读取 GUID 值；但是，由于参数和常量的默认格式已更改，可能需要针对涉及 GUID 的大多数情况采取措施。</span><span class="sxs-lookup"><span data-stu-id="f3806-471">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="f3806-472">Char 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="f3806-472">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="f3806-473">跟踪问题 #15020</span><span class="sxs-lookup"><span data-stu-id="f3806-473">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="f3806-474">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-474">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-475">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-475">**Old behavior**</span></span>

<span data-ttu-id="f3806-476">Char 值之前以整数值形式存储在 SQLite 上。</span><span class="sxs-lookup"><span data-stu-id="f3806-476">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="f3806-477">例如，A 的 char 值存储为整数值 65。</span><span class="sxs-lookup"><span data-stu-id="f3806-477">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="f3806-478">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-478">**New behavior**</span></span>

<span data-ttu-id="f3806-479">Char 值现在以文本形式存储。</span><span class="sxs-lookup"><span data-stu-id="f3806-479">Char values are now sotred as TEXT.</span></span>

<span data-ttu-id="f3806-480">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-480">**Why**</span></span>

<span data-ttu-id="f3806-481">以文本形式存储值显得更加自然，并且使数据库与其他技术更兼容。</span><span class="sxs-lookup"><span data-stu-id="f3806-481">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="f3806-482">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-482">**Mitigations**</span></span>

<span data-ttu-id="f3806-483">现在通过执行如下的 SQL，可以将现有数据库转成新的格式。</span><span class="sxs-lookup"><span data-stu-id="f3806-483">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="f3806-484">在 EF Core 中，通过对这些属性配置值转换器，还可以使用以前的行为模式继续操作。</span><span class="sxs-lookup"><span data-stu-id="f3806-484">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="f3806-485">Microsoft.Data.Sqlite 也仍然能够读取整数列和文本列的字符值，因此某些情况可能不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="f3806-485">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="f3806-486">现在使用固定区域性的日历生成迁移 ID</span><span class="sxs-lookup"><span data-stu-id="f3806-486">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="f3806-487">跟踪问题 #12978</span><span class="sxs-lookup"><span data-stu-id="f3806-487">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="f3806-488">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-488">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-489">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-489">**Old behavior**</span></span>

<span data-ttu-id="f3806-490">以前使用当前区域性的日历无意生成迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="f3806-490">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

<span data-ttu-id="f3806-491">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-491">**New behavior**</span></span>

<span data-ttu-id="f3806-492">现在始终使用固定区域性的日历（公历）生成迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="f3806-492">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="f3806-493">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-493">**Why**</span></span>

<span data-ttu-id="f3806-494">更新数据库或解决合并冲突时，迁移的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="f3806-494">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="f3806-495">使用固定日历可以避免因团队成员采用不同系统日历而产生的顺序问题。</span><span class="sxs-lookup"><span data-stu-id="f3806-495">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="f3806-496">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-496">**Mitigations**</span></span>

<span data-ttu-id="f3806-497">如果有人使用年份时间大于公历日历的非公历日历（如泰国佛历），则会受到影响。</span><span class="sxs-lookup"><span data-stu-id="f3806-497">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="f3806-498">现有迁移 ID 需要进行更新，以便新迁移排在现有迁移之后。</span><span class="sxs-lookup"><span data-stu-id="f3806-498">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="f3806-499">在迁移的设计者文件的 Migration 属性中可以找到迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="f3806-499">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="f3806-500">迁移历史记录表还需要更新。</span><span class="sxs-lookup"><span data-stu-id="f3806-500">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="f3806-501">已重命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="f3806-501">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="f3806-502">跟踪问题 #10985</span><span class="sxs-lookup"><span data-stu-id="f3806-502">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="f3806-503">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-503">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-504">**更改**</span><span class="sxs-lookup"><span data-stu-id="f3806-504">**Change**</span></span>

<span data-ttu-id="f3806-505">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` 已重命名为 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。</span><span class="sxs-lookup"><span data-stu-id="f3806-505">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="f3806-506">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-506">**Why**</span></span>

<span data-ttu-id="f3806-507">将此警告事件的命名方式与所有其他警告事件保持一致。</span><span class="sxs-lookup"><span data-stu-id="f3806-507">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="f3806-508">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-508">**Mitigations**</span></span>

<span data-ttu-id="f3806-509">使用新名称。</span><span class="sxs-lookup"><span data-stu-id="f3806-509">Use the new name.</span></span> <span data-ttu-id="f3806-510">（注意：事件 ID 号码未更改。）</span><span class="sxs-lookup"><span data-stu-id="f3806-510">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="f3806-511">阐明 API 的外键约束名称</span><span class="sxs-lookup"><span data-stu-id="f3806-511">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="f3806-512">跟踪问题 #10730</span><span class="sxs-lookup"><span data-stu-id="f3806-512">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="f3806-513">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3806-513">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="f3806-514">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-514">**Old behavior**</span></span>

<span data-ttu-id="f3806-515">在 EF Core 3.0 之前，外键约束名称被简单地称为“名称”。</span><span class="sxs-lookup"><span data-stu-id="f3806-515">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="f3806-516">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-516">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="f3806-517">**新行为**</span><span class="sxs-lookup"><span data-stu-id="f3806-517">**New behavior**</span></span>

<span data-ttu-id="f3806-518">从 EF Core 3.0 开始，外键约束名称现在被称为“约束名称”。</span><span class="sxs-lookup"><span data-stu-id="f3806-518">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constaint name".</span></span> <span data-ttu-id="f3806-519">例如:</span><span class="sxs-lookup"><span data-stu-id="f3806-519">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="f3806-520">**为什么**</span><span class="sxs-lookup"><span data-stu-id="f3806-520">**Why**</span></span>

<span data-ttu-id="f3806-521">这样不仅可以让此领域的命名方式保持一致，还阐明了这是外键约束的名称，不是定义外键所依据的列或属性名称。</span><span class="sxs-lookup"><span data-stu-id="f3806-521">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constaint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="f3806-522">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="f3806-522">**Mitigations**</span></span>

<span data-ttu-id="f3806-523">使用新名称。</span><span class="sxs-lookup"><span data-stu-id="f3806-523">Use the new name.</span></span>
