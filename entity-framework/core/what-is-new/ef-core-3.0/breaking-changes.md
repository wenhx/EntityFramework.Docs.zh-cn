---
title: EF Core 3.0 中的中断性变更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: fd593b2832a5a6ffe27cd4493127b5d405f684ba
ms.sourcegitcommit: ce44f85a5bce32ef2d3d09b7682108d3473511b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "58914122"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="0eaf9-102">EF Core 3.0 中包含的中断性变更（目前处于预览状态）</span><span class="sxs-lookup"><span data-stu-id="0eaf9-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0eaf9-103">请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="0eaf9-104">以下 API 和行为更改有可能在将 EF Core 2.2.x 升级到 3.0.0 时中断为其开发的应用程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="0eaf9-105">我们将仅影响数据库提供程序的更改记录在[提供程序更改](../../providers/provider-log.md)下。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="0eaf9-106">其中没有记录从一个 3.0 预览版引入到另一个 3.0 预览版的新功能的中断。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="0eaf9-107">不再在客户端上计算 LINQ 查询</span><span class="sxs-lookup"><span data-stu-id="0eaf9-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="0eaf9-108">[跟踪问题 #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[另请参阅问题 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="0eaf9-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="0eaf9-109">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-110">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-110">Old behavior</span></span>**

<span data-ttu-id="0eaf9-111">在 3.0 之前，当 EF Core 无法将查询中的表达式转换为 SQL 或参数时，它会在客户端上自动计算表达式的值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="0eaf9-112">默认情况下，客户端对潜在的昂贵表达式的计算仅触发警告。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

**<span data-ttu-id="0eaf9-113">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-113">New behavior</span></span>**

<span data-ttu-id="0eaf9-114">从 3.0 开始，EF Core 仅允许在客户端上计算顶级投影中的表达式（查询中的最后一个 `Select()` 调用）。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="0eaf9-115">当查询的任何其他部分中的表达式无法转换为 SQL 或参数时，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

**<span data-ttu-id="0eaf9-116">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-116">Why</span></span>**

<span data-ttu-id="0eaf9-117">自动的客户端查询计算允许执行许多查询，即使它们的重要组成部分无法转换。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="0eaf9-118">此行为可能导致意外且具有潜在破坏性的行为，这些行为可能仅在生产中变得明显。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="0eaf9-119">例如，`Where()` 调用中无法转换的条件可能导致表中的所有行从数据库服务器传输且筛选器应用于客户端。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="0eaf9-120">如果在开发中表中只包含几行，则不容易检测到这种情况，但是当应用程序转入生产环节时，由于表中可能包含数百万行，这种情况会非常严重。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="0eaf9-121">在开发过程中，客户端求值警告也很容易被忽视。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="0eaf9-122">除此之外，自动客户端计算可能会导致问题，其中改进特定表达式的查询转换会导致版本之间发生意外中断性变更。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

**<span data-ttu-id="0eaf9-123">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-123">Mitigations</span></span>**

<span data-ttu-id="0eaf9-124">如果无法完全转换查询，则以可转换的形式重写查询，或使用 `AsEnumerable()`、`ToList()` 或类似信息将数据显式返回客户端，然后可以进一步使用 LINQ 到对象处理。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="0eaf9-125">Entity Framework Core 不再是 ASP.NET Core 共享框架的一部分</span><span class="sxs-lookup"><span data-stu-id="0eaf9-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="0eaf9-126">跟踪问题公告#325</span><span class="sxs-lookup"><span data-stu-id="0eaf9-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="0eaf9-127">此更改是在 ASP.NET Core 3.0-preview 1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

**<span data-ttu-id="0eaf9-128">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-128">Old behavior</span></span>**

<span data-ttu-id="0eaf9-129">在 ASP.NET Core 3.0 之前，当向 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 添加包引用时，它将包括 EF Core 和一些 EF Core 数据提供程序（如 SQL Server 提供程序）。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

**<span data-ttu-id="0eaf9-130">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-130">New behavior</span></span>**

<span data-ttu-id="0eaf9-131">从 3.0 开始，ASP.NET Core 共享框架不包括 EF Core 或任何 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

**<span data-ttu-id="0eaf9-132">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-132">Why</span></span>**

<span data-ttu-id="0eaf9-133">在此更改之前，获取 EF Core 需要不同的步骤，具体取决于应用程序是否是面向 ASP.NET Core 和 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="0eaf9-134">此外，升级 ASP.NET Core 会强制升级 EF Core 和 SQL Server 提供程序，这并不总是可取的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="0eaf9-135">通过此更改，通过所有提供程序、支持的 .NET 实现和应用程序类型获取 EF Core 的体验都是一致的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="0eaf9-136">开发人员现在还可以准确控制何时升级 EF Core 和 EF Core 数据提供程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

**<span data-ttu-id="0eaf9-137">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-137">Mitigations</span></span>**

<span data-ttu-id="0eaf9-138">若要在 ASP.NET Core 3.0 应用程序或任何其他受支持的应用程序中使用 EF Core，请显式添加对应用程序将使用的 EF Core 数据库提供程序的包引用。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="0eaf9-139">FromSql、ExecuteSql 和 ExecuteSqlAsync 已重命名</span><span class="sxs-lookup"><span data-stu-id="0eaf9-139">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="0eaf9-140">跟踪问题 #10996</span><span class="sxs-lookup"><span data-stu-id="0eaf9-140">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="0eaf9-141">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-141">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-142">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-142">Old behavior</span></span>**

<span data-ttu-id="0eaf9-143">在 EF Core 3.0 之前，这些方法名称是重载的，它们使用普通字符串或应内插到 SQL 和参数中的字符串。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-143">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

**<span data-ttu-id="0eaf9-144">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-144">New behavior</span></span>**

<span data-ttu-id="0eaf9-145">自 EF Core 3.0 起，可使用 `FromSqlRaw`、`ExecuteSqlRaw` 和 `ExecuteSqlRawAsync` 创建一个参数化的查询，其中参数是从查询字符串中单独传递的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-145">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="0eaf9-146">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-146">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="0eaf9-147">使用 `FromSqlInterpolated`、`ExecuteSqlInterpolated` 和 `ExecuteSqlInterpolatedAsync` 创建一个参数化的查询，其中参数作为内插查询字符串的一部分进行传递。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-147">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="0eaf9-148">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-148">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="0eaf9-149">请注意，上述两个查询都将生成 SQL 参数相同的同一参数化的 SQL。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-149">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

**<span data-ttu-id="0eaf9-150">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-150">Why</span></span>**

<span data-ttu-id="0eaf9-151">此类方法重载使得在意图调用内插字符串方法时很容易意外调用原始字符串方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-151">Method overloads like this make it very easy to accidentally call the raw srting method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="0eaf9-152">这会导致查询中的本该参数化的结果没有参数化。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-152">This could result in queries not being parameterized when they should have been.</span></span>

**<span data-ttu-id="0eaf9-153">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-153">Mitigations</span></span>**

<span data-ttu-id="0eaf9-154">切换到使用新的方法名称。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-154">Switch to use the new method names.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="0eaf9-155">在调试级别记录查询执行</span><span class="sxs-lookup"><span data-stu-id="0eaf9-155">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="0eaf9-156">跟踪问题 #14523</span><span class="sxs-lookup"><span data-stu-id="0eaf9-156">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="0eaf9-157">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-157">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-158">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-158">Old behavior</span></span>**

<span data-ttu-id="0eaf9-159">EF Core 3.0 之前，是在 `Info` 级别记录查询和其他命令的执行。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-159">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

**<span data-ttu-id="0eaf9-160">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-160">New behavior</span></span>**

<span data-ttu-id="0eaf9-161">从 EF Core 3.0 开始，将在 `Debug` 级别记录命令/SQL 的执行。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-161">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

**<span data-ttu-id="0eaf9-162">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-162">Why</span></span>**

<span data-ttu-id="0eaf9-163">此更改是为了降低 `Info` 日志级别的噪音。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-163">This change was made to reduce the noise at the `Info` log level.</span></span>

**<span data-ttu-id="0eaf9-164">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-164">Mitigations</span></span>**

<span data-ttu-id="0eaf9-165">此日志记录事件由 `RelationalEventId.CommandExecuting` 定义，事件 ID 为 20100。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-165">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="0eaf9-166">若要再次在 `Info` 级别记录 SQL，请在 `OnConfiguring` 或 `AddDbContext` 中显式配置级别。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-166">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="0eaf9-167">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-167">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="0eaf9-168">不再在实体实例上设置临时键值</span><span class="sxs-lookup"><span data-stu-id="0eaf9-168">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="0eaf9-169">跟踪问题 #12378</span><span class="sxs-lookup"><span data-stu-id="0eaf9-169">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="0eaf9-170">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-170">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="0eaf9-171">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-171">Old behavior</span></span>**

<span data-ttu-id="0eaf9-172">在 EF Core 3.0 之前，临时值已分配给所有键属性，这些属性稍后将具有数据库生成的实际值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-172">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="0eaf9-173">通常这些临时值是较大负数。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-173">Usually these temporary values were large negative numbers.</span></span>

**<span data-ttu-id="0eaf9-174">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-174">New behavior</span></span>**

<span data-ttu-id="0eaf9-175">从 3.0 开始，EF Core 将临时键值存储为实体跟踪信息的一部分，并保持键属性本身不变。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-175">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

**<span data-ttu-id="0eaf9-176">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-176">Why</span></span>**

<span data-ttu-id="0eaf9-177">此更改是为了防止当之前由某个 `DbContext` 实例跟踪的实体移动到另一个 `DbContext` 实例时，临时键值错误地变成永久值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-177">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

**<span data-ttu-id="0eaf9-178">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-178">Mitigations</span></span>**

<span data-ttu-id="0eaf9-179">如果主键是存储生成的并且属于 `Added` 状态的实体，则将主键值分配到外键以在实体之间形成关联的应用可能会依赖于旧行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-179">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="0eaf9-180">可通过以下方式避免：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-180">This can be avoided by:</span></span>
* <span data-ttu-id="0eaf9-181">不使用存储生成的密钥。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-181">Not using store-generated keys.</span></span>
* <span data-ttu-id="0eaf9-182">设置导航属性以形成关系，而不是设置外键值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-182">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="0eaf9-183">从实体的跟踪信息中获取实际的临时键值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-183">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="0eaf9-184">例如，即使 `blog.Id` 本身尚未设置，`context.Entry(blog).Property(e => e.Id).CurrentValue` 也将返回临时值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-184">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="0eaf9-185">DetectChanges 遵循存储生成的键值</span><span class="sxs-lookup"><span data-stu-id="0eaf9-185">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="0eaf9-186">跟踪问题 #14616</span><span class="sxs-lookup"><span data-stu-id="0eaf9-186">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="0eaf9-187">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-188">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-188">Old behavior</span></span>**

<span data-ttu-id="0eaf9-189">在 EF Core 3.0 之前，`DetectChanges` 找到的未跟踪实体将在 `Added` 状态中被跟踪，并在调用 `SaveChanges` 时作为新行插入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-189">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

**<span data-ttu-id="0eaf9-190">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-190">New behavior</span></span>**

<span data-ttu-id="0eaf9-191">从 EF Core 3.0 开始，如果实体使用生成的键值并设置了某个键值，则将在 `Modified` 状态下跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-191">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="0eaf9-192">这意味着假定存在实体的行，并且在调用 `SaveChanges` 时将更新该行。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-192">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="0eaf9-193">如果未设置键值，或者实体类型未使用生成的键，则新实体仍将像先前版本一样被作为 `Added` 跟踪。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-193">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

**<span data-ttu-id="0eaf9-194">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-194">Why</span></span>**

<span data-ttu-id="0eaf9-195">进行此更改是为了在使用存储生成的键时更轻松、更一致地使用断开连接的实体图。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-195">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

**<span data-ttu-id="0eaf9-196">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-196">Mitigations</span></span>**

<span data-ttu-id="0eaf9-197">如果将实体类型配置为使用生成的键，但为新实例显式设置了键值，则此更改可能会中断应用程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-197">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="0eaf9-198">解决方案是显式配置键属性，使其不使用生成的值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-198">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="0eaf9-199">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-199">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="0eaf9-200">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-200">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="0eaf9-201">默认情况下，现在会立即发生级联删除</span><span class="sxs-lookup"><span data-stu-id="0eaf9-201">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="0eaf9-202">跟踪问题 #10114</span><span class="sxs-lookup"><span data-stu-id="0eaf9-202">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="0eaf9-203">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-203">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-204">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-204">Old behavior</span></span>**

<span data-ttu-id="0eaf9-205">在 3.0 之前，直到调用 SaveChanges 时，EF Core 才会应用级联操作（删除所需主体时或者在切断与所需主体的关系时删除依赖实体）。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-205">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

**<span data-ttu-id="0eaf9-206">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-206">New behavior</span></span>**

<span data-ttu-id="0eaf9-207">从 3.0 开始，一旦检测到触发条件，EF Core 就会应用级联操作。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-207">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="0eaf9-208">例如，调用 `context.Remove()` 来删除主体实体将导致所有跟踪的相关必需依赖项也立即设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-208">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

**<span data-ttu-id="0eaf9-209">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-209">Why</span></span>**

<span data-ttu-id="0eaf9-210">此更改是为了改善数据绑定和审核方案的体验，在相关体验中，需要了解在调用 `SaveChanges` _之前_会删除哪些实体。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-210">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

**<span data-ttu-id="0eaf9-211">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-211">Mitigations</span></span>**

<span data-ttu-id="0eaf9-212">可以通过 `context.ChangedTracker` 上的设置还原以前的行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-212">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="0eaf9-213">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-213">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="0eaf9-214">查询类型与实体类型合并</span><span class="sxs-lookup"><span data-stu-id="0eaf9-214">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="0eaf9-215">跟踪问题 #14194</span><span class="sxs-lookup"><span data-stu-id="0eaf9-215">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="0eaf9-216">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-216">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-217">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-217">Old behavior</span></span>**

<span data-ttu-id="0eaf9-218">在 EF Core 3.0 之前，[查询类型](xref:core/modeling/query-types)是一种查询未以结构化方式定义主键的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-218">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="0eaf9-219">也就是说，查询类型用于映射没有键的实体类型（更可能来自视图，但也可能来自表），而当有可用的键时则使用常规实体类型（更可能来自表，但也可能来自视图）。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-219">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

**<span data-ttu-id="0eaf9-220">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-220">New behavior</span></span>**

<span data-ttu-id="0eaf9-221">现在，查询类型只是一个没有主键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-221">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="0eaf9-222">无键实体类型与先前版本中的查询类型具有相同的功能。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-222">Keyless entity types have the same functionality as query types in previous versions.</span></span>

**<span data-ttu-id="0eaf9-223">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-223">Why</span></span>**

<span data-ttu-id="0eaf9-224">这样做是为了减少对查询类型用途的混淆。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-224">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="0eaf9-225">具体来说，它们是无键实体类型，因此本质上是只读的，但是不应该仅仅因为实体类型需要是只读的就使用它们。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-225">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="0eaf9-226">同样，它们通常映射到视图，但这只是因为视图通常不定义键。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-226">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

**<span data-ttu-id="0eaf9-227">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-227">Mitigations</span></span>**

<span data-ttu-id="0eaf9-228">API 的以下部分现已过时：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-228">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="0eaf9-229">**`ModelBuilder.Query<>()`** - 需要调用 `ModelBuilder.Entity<>().HasNoKey()` 来将实体类型标记为没有键。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-229">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="0eaf9-230">这仍然不会按约定配置，以避免在需要主键但是与约定不匹配时发生配置错误。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-230">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="0eaf9-231">**`DbQuery<>`** - 应使用 `DbSet<>`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-231">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="0eaf9-232">**`DbContext.Query<>()`** - 应使用 `DbContext.Set<>()`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-232">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="0eaf9-233">从属类型关系的配置 API 已更改</span><span class="sxs-lookup"><span data-stu-id="0eaf9-233">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="0eaf9-234">[跟踪问题 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[跟踪问题 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[跟踪问题 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="0eaf9-234">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="0eaf9-235">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-235">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-236">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-236">Old behavior</span></span>**

<span data-ttu-id="0eaf9-237">EF Core 3.0 之前，会在 `OwnsOne` 或 `OwnsMany` 调用之后直接执行所拥有关系的配置。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-237">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

**<span data-ttu-id="0eaf9-238">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-238">New behavior</span></span>**

<span data-ttu-id="0eaf9-239">从 EF Core 3.0 开始，Fluent API 会使用 `WithOwner()` 为所有者配置导航属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-239">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="0eaf9-240">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-240">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="0eaf9-241">与所有者之间关系相关的配置现会在 `WithOwner()` 之后关联起来，就像配置其他关系一样。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-241">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="0eaf9-242">但从属类型本身的配置仍会在 `OwnsOne()/OwnsMany()` 之后关联。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-242">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="0eaf9-243">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-243">For example:</span></span>

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

<span data-ttu-id="0eaf9-244">此外，使用固有类型目标调用 `Entity()``HasOne()` 或 `Set()` 现将引发异常。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-244">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

**<span data-ttu-id="0eaf9-245">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-245">Why</span></span>**

<span data-ttu-id="0eaf9-246">此更改是为了更清晰的区分从属类型本身的配置和与从属类型的_关系_的配置。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-246">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="0eaf9-247">这反过来消除了诸如 `HasForeignKey` 之类的方法的模糊性和混淆。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-247">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

**<span data-ttu-id="0eaf9-248">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-248">Mitigations</span></span>**

<span data-ttu-id="0eaf9-249">更改从属类型关系的配置以使用新的 API 曲面，如上例所示。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-249">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="0eaf9-250">与主体共享表的依赖实体现为可选项</span><span class="sxs-lookup"><span data-stu-id="0eaf9-250">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="0eaf9-251">跟踪问题 #9005</span><span class="sxs-lookup"><span data-stu-id="0eaf9-251">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="0eaf9-252">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-252">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-253">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-253">Old behavior</span></span>**

<span data-ttu-id="0eaf9-254">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-254">Consider the following model:</span></span>
```C#
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
<span data-ttu-id="0eaf9-255">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表，则在添加新的 `Order` 时，始终需要 `OrderDetails` 实例。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-255">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


**<span data-ttu-id="0eaf9-256">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-256">New behavior</span></span>**

<span data-ttu-id="0eaf9-257">自 3.0 起，EF Core 允许添加 `Order` 而不添加 `OrderDetails`，并将主键之外的所有 `OrderDetails` 属性映射到不为 null 的列中。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-257">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="0eaf9-258">查询时，如果其任意所需属性均没有值，或者它在主键之外没有任何必需属性且所有属性均为 `null`，则 EF Core 会将 `OrderDetails` 设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-258">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

**<span data-ttu-id="0eaf9-259">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-259">Mitigations</span></span>**

<span data-ttu-id="0eaf9-260">如果你的模型具有依赖于所有可选列的表共享，但指向该共享的导航不应为 `null`，则应修改应用程序，使其处理导航为 `null` 的情况。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-260">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="0eaf9-261">如果此方法不可行，则应向实体类型添加一个必需属性，或者至少要有一个属性分配有非 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-261">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="0eaf9-262">与并发标记列共享表的所有实体均必须将其映射到属性</span><span class="sxs-lookup"><span data-stu-id="0eaf9-262">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="0eaf9-263">跟踪问题 #14154</span><span class="sxs-lookup"><span data-stu-id="0eaf9-263">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="0eaf9-264">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-264">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-265">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-265">Old behavior</span></span>**

<span data-ttu-id="0eaf9-266">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-266">Consider the following model:</span></span>
```C#
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
<span data-ttu-id="0eaf9-267">在 EF Core 3.0 之前，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表，则只更新 `OrderDetails` 时，将不更新客户端上的 `Version` 值且下次更新将失败。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-267">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


**<span data-ttu-id="0eaf9-268">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-268">New behavior</span></span>**

<span data-ttu-id="0eaf9-269">自 3.0 起，如果 新的 `Version` 值拥有 `OrderDetails`则 EF Core 会将该值传播给 `Order`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-269">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="0eaf9-270">否则，会在模型验证期间引发异常。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-270">Otherwise an exception is thrown during model validation.</span></span>

**<span data-ttu-id="0eaf9-271">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-271">Why</span></span>**

<span data-ttu-id="0eaf9-272">进行此更改的目的是避免在仅更新映射到同一张表的其中一个实体时使用过时的并发标记值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-272">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

**<span data-ttu-id="0eaf9-273">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-273">Mitigations</span></span>**

<span data-ttu-id="0eaf9-274">共享表的所有实体都必须包含一个映射到并发标记列的属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-274">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="0eaf9-275">可在影子状态中创建一个：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-275">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="0eaf9-276">对于所有派生的类型而言，从未映射的类型继承的属性现在会映射到一个列中</span><span class="sxs-lookup"><span data-stu-id="0eaf9-276">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="0eaf9-277">跟踪问题 #13998</span><span class="sxs-lookup"><span data-stu-id="0eaf9-277">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="0eaf9-278">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-279">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-279">Old behavior</span></span>**

<span data-ttu-id="0eaf9-280">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-280">Consider the following model:</span></span>
```C#
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

<span data-ttu-id="0eaf9-281">在 EF Core 3.0 之前，`ShippingAddress` 属性会为 `BulkOrder` 和 `Order` 默认映射到单独的列中。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-281">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

**<span data-ttu-id="0eaf9-282">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-282">New behavior</span></span>**

<span data-ttu-id="0eaf9-283">自 3.0 起，EF Core只会为 `ShippingAddress` 创建一个列。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-283">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

**<span data-ttu-id="0eaf9-284">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-284">Why</span></span>**

<span data-ttu-id="0eaf9-285">旧行为不是预期行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-285">The old behavoir was unexpected.</span></span>

**<span data-ttu-id="0eaf9-286">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-286">Mitigations</span></span>**

<span data-ttu-id="0eaf9-287">属性仍可显式映射到所派生的类型上的单独的列中：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-287">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```C#
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

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="0eaf9-288">外键属性约定不再匹配与主体属性相同的名称</span><span class="sxs-lookup"><span data-stu-id="0eaf9-288">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="0eaf9-289">跟踪问题 #13274</span><span class="sxs-lookup"><span data-stu-id="0eaf9-289">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="0eaf9-290">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-290">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-291">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-291">Old behavior</span></span>**

<span data-ttu-id="0eaf9-292">考虑下列模型：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-292">Consider the following model:</span></span>
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
<span data-ttu-id="0eaf9-293">在 EF Core 3.0 之前，`CustomerId` 属性将按约定用于外键。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-293">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="0eaf9-294">但是，如果 `Order` 是从属类型，那么这也会使 `CustomerId` 成为主键，这通常不是预期结果。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-294">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

**<span data-ttu-id="0eaf9-295">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-295">New behavior</span></span>**

<span data-ttu-id="0eaf9-296">自 3.0 起，如果外键的主体属性名称相同，EF Core 不会尝试通过转换来为外键使用属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-296">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="0eaf9-297">与主体属性名称关联的主体类型名称和与主体属性名称模式关联的导航名称仍然相匹配。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-297">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="0eaf9-298">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-298">For example:</span></span>

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

**<span data-ttu-id="0eaf9-299">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-299">Why</span></span>**

<span data-ttu-id="0eaf9-300">此更改是为了避免错误地在从属类型上定义主键属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-300">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

**<span data-ttu-id="0eaf9-301">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-301">Mitigations</span></span>**

<span data-ttu-id="0eaf9-302">如果该属性将成为外键，即为主键的一部分，则显式配置它。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-302">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="0eaf9-303">现在，如果在 TransactionScope 完成前不再使用数据库连接，则该连接会关闭</span><span class="sxs-lookup"><span data-stu-id="0eaf9-303">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="0eaf9-304">跟踪问题 #14218</span><span class="sxs-lookup"><span data-stu-id="0eaf9-304">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="0eaf9-305">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-305">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-306">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-306">Old behavior</span></span>**

<span data-ttu-id="0eaf9-307">在 EF Core 3.0 之前，如果上下文打开了 `TransactionScope` 中的连接，则该连接在 `TransactionScope` 处于活动期间仍保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-307">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```C#
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

**<span data-ttu-id="0eaf9-308">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-308">New behavior</span></span>**

<span data-ttu-id="0eaf9-309">自 3.0 起，一旦不再使用连接，EF Core 就会将其关闭。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-309">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

**<span data-ttu-id="0eaf9-310">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-310">Why</span></span>**

<span data-ttu-id="0eaf9-311">此更改让你能够在同一 `TransactionScope` 中使用多个上下文。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-311">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="0eaf9-312">新行为也与 EF6 相匹配。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-312">The new behavior alose matches EF6.</span></span>

**<span data-ttu-id="0eaf9-313">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-313">Mitigations</span></span>**

<span data-ttu-id="0eaf9-314">如果连接需要保持打开状态，则显式调用 `OpenConnection()` 将确保 EF Core 不永久关闭此连接：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-314">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```C#
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

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="0eaf9-315">每个属性使用独立的内存中整数键生成</span><span class="sxs-lookup"><span data-stu-id="0eaf9-315">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="0eaf9-316">跟踪问题 #6872</span><span class="sxs-lookup"><span data-stu-id="0eaf9-316">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="0eaf9-317">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-317">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-318">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-318">Old behavior</span></span>**

<span data-ttu-id="0eaf9-319">在 EF Core 3.0 之前，一个共享值生成器用于所有内存中的整数键属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-319">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

**<span data-ttu-id="0eaf9-320">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-320">New behavior</span></span>**

<span data-ttu-id="0eaf9-321">从 EF Core 3.0 开始，每个整数键属性在使用内存数据库时都会获取其自己的值生成器。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-321">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="0eaf9-322">此外，如果删除了数据库，则会为所有表重置键生成。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-322">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

**<span data-ttu-id="0eaf9-323">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-323">Why</span></span>**

<span data-ttu-id="0eaf9-324">此更改的目的是使内存中的键生成更接近于实际的数据库键生成，并改进在使用内存中的数据库时隔离测试的功能。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-324">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

**<span data-ttu-id="0eaf9-325">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-325">Mitigations</span></span>**

<span data-ttu-id="0eaf9-326">这可能会中断依赖于特定内存中键值的应用程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-326">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="0eaf9-327">请考虑不依赖于特定键值，或者进行更新以匹配新行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-327">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="0eaf9-328">默认情况下使用支持字段</span><span class="sxs-lookup"><span data-stu-id="0eaf9-328">Backing fields are used by default</span></span>

[<span data-ttu-id="0eaf9-329">跟踪问题 #12430</span><span class="sxs-lookup"><span data-stu-id="0eaf9-329">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="0eaf9-330">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-330">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="0eaf9-331">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-331">Old behavior</span></span>**

<span data-ttu-id="0eaf9-332">在 3.0 之前，即使已知属性的支持字段，EF Core 仍将默认使用属性 getter 和 setter 方法读取和写入属性值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-332">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="0eaf9-333">例外情况是查询执行，如果已知，将直接设置支持字段。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-333">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

**<span data-ttu-id="0eaf9-334">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-334">New behavior</span></span>**

<span data-ttu-id="0eaf9-335">从 EF Core 3.0 开始，如果已知属性的支持字段，则始终使用支持字段读取和写入该属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-335">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="0eaf9-336">如果应用程序依赖于编码到 getter 或 setter 方法中的其他行为，则可能导致应用程序中断。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-336">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

**<span data-ttu-id="0eaf9-337">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-337">Why</span></span>**

<span data-ttu-id="0eaf9-338">此更改是为了防止 EF Core 在执行涉及实体的数据库操作时默认错误地触发业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-338">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

**<span data-ttu-id="0eaf9-339">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-339">Mitigations</span></span>**

<span data-ttu-id="0eaf9-340">可以通过在 modelBuilder fluent API 中配置属性访问模式来恢复 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-340">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="0eaf9-341">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-341">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="0eaf9-342">如果找到多个兼容的支持字段，则引发</span><span class="sxs-lookup"><span data-stu-id="0eaf9-342">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="0eaf9-343">跟踪问题 #12523</span><span class="sxs-lookup"><span data-stu-id="0eaf9-343">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="0eaf9-344">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-344">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-345">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-345">Old behavior</span></span>**

<span data-ttu-id="0eaf9-346">在 EF Core 3.0 之前，如果多个字段与查找属性的后备字段的规则匹配，则将基于某种优先顺序选择一个字段。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-346">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="0eaf9-347">这可能导致在不明确的情况下使用错误字段。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-347">This could cause the wrong field to be used in ambiguous cases.</span></span>

**<span data-ttu-id="0eaf9-348">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-348">New behavior</span></span>**

<span data-ttu-id="0eaf9-349">从 EF Core 3.0 开始，如果多个字段与同一属性匹配，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-349">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

**<span data-ttu-id="0eaf9-350">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-350">Why</span></span>**

<span data-ttu-id="0eaf9-351">此更改是为了避免在只有一个字段是正确的情况下无提示地使用另一个字段。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-351">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

**<span data-ttu-id="0eaf9-352">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-352">Mitigations</span></span>**

<span data-ttu-id="0eaf9-353">具有不明确的支持字段的属性必须具有显式指定的字段。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-353">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="0eaf9-354">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-354">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="0eaf9-355">AddDbContext/AddDbContextPool 不再调用 AddLogging 和 AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="0eaf9-355">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="0eaf9-356">跟踪问题 #14756</span><span class="sxs-lookup"><span data-stu-id="0eaf9-356">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="0eaf9-357">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-357">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-358">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-358">Old behavior</span></span>**

<span data-ttu-id="0eaf9-359">在 EF Core 3.0 之前，调用 `AddDbContext` 或 `AddDbContextPool` 的操作也可以通过调用 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 和 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 在 DI 中注册日志记录和内存缓存服务。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-359">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

**<span data-ttu-id="0eaf9-360">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-360">New behavior</span></span>**

<span data-ttu-id="0eaf9-361">从 EF Core 3.0 开始，`AddDbContext` 和 `AddDbContextPool` 将无法再在依赖注入 (DI) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-361">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

**<span data-ttu-id="0eaf9-362">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-362">Why</span></span>**

<span data-ttu-id="0eaf9-363">EF Core 3.0 不要求这些服务位于应用程序的 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-363">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="0eaf9-364">但是，如果 `ILoggerFactory` 在应用程序的 DI 容器中注册，它仍然会由 EF Core 使用。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-364">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

**<span data-ttu-id="0eaf9-365">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-365">Mitigations</span></span>**

<span data-ttu-id="0eaf9-366">如果应用程序需要这些服务，则使用 [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) 或 [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) 将它们显式注册到 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-366">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="0eaf9-367">DbContext.Entry 现在执行本地 DetectChanges</span><span class="sxs-lookup"><span data-stu-id="0eaf9-367">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="0eaf9-368">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="0eaf9-368">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="0eaf9-369">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-369">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-370">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-370">Old behavior</span></span>**

<span data-ttu-id="0eaf9-371">在 EF Core 3.0 之前，调用 `DbContext.Entry` 将导致检测到所有被跟踪实体的更改。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-371">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="0eaf9-372">这确保了 `EntityEntry` 中暴露的状态是最新的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-372">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

**<span data-ttu-id="0eaf9-373">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-373">New behavior</span></span>**

<span data-ttu-id="0eaf9-374">从 EF Core 3.0 开始，调用 `DbContext.Entry` 现在只会尝试检测给定实体和与之相关的任何跟踪主体实体的更改。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-374">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="0eaf9-375">这意味着可能无法通过调用此方法检测到其他位置的更改，这可能会影响应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-375">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="0eaf9-376">请注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 设置为 `false`，则即使是本地更改检测也将被禁用。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-376">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="0eaf9-377">导致更改检测的其他方法（例如 `ChangeTracker.Entries` 和 `SaveChanges`）仍然会导致所有被跟踪实体的完整 `DetectChanges`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-377">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

**<span data-ttu-id="0eaf9-378">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-378">Why</span></span>**

<span data-ttu-id="0eaf9-379">此更改是为了提高使用 `context.Entry` 的默认性能。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-379">This change was made to improve the default performance of using `context.Entry`.</span></span>

**<span data-ttu-id="0eaf9-380">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-380">Mitigations</span></span>**

<span data-ttu-id="0eaf9-381">在调用 `Entry` 之前显式调用 `ChgangeTracker.DetectChanges()` 以确保 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-381">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="0eaf9-382">默认情况下，字符串和字节数组键不是客户端生成的</span><span class="sxs-lookup"><span data-stu-id="0eaf9-382">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="0eaf9-383">跟踪问题 #14617</span><span class="sxs-lookup"><span data-stu-id="0eaf9-383">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="0eaf9-384">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-384">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-385">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-385">Old behavior</span></span>**

<span data-ttu-id="0eaf9-386">在 EF Core 3.0 之前，可以使用 `string` 和 `byte[]` 键属性，而不需要显式地设置非 null 值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-386">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="0eaf9-387">在这种情况下，键值将在客户端上生成为 GUID，并序列化为 `byte[]` 的字节。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-387">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

**<span data-ttu-id="0eaf9-388">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-388">New behavior</span></span>**

<span data-ttu-id="0eaf9-389">从 EF Core 3.0 开始，将引发异常，指示未设置任何键值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-389">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

**<span data-ttu-id="0eaf9-390">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-390">Why</span></span>**

<span data-ttu-id="0eaf9-391">之所以进行此更改是因为客户端生成的 `string`/`byte[]` 值通常没有用，并且默认行为使得很难以通用方式推断生成的键值。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-391">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

**<span data-ttu-id="0eaf9-392">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-392">Mitigations</span></span>**

<span data-ttu-id="0eaf9-393">如果没有设置其他非 null 值，则可以通过显式指定键属性应使用生成的值来获得 3.0 之前的行为。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-393">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="0eaf9-394">例如，使用 Fluent API：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-394">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="0eaf9-395">或使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-395">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="0eaf9-396">ILoggerFactory 现在是一个在一定范围内有效的服务</span><span class="sxs-lookup"><span data-stu-id="0eaf9-396">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="0eaf9-397">跟踪问题 #14698</span><span class="sxs-lookup"><span data-stu-id="0eaf9-397">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="0eaf9-398">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-398">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-399">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-399">Old behavior</span></span>**

<span data-ttu-id="0eaf9-400">在 EF Core 3.0 之前，`ILoggerFactory` 被注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-400">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

**<span data-ttu-id="0eaf9-401">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-401">New behavior</span></span>**

<span data-ttu-id="0eaf9-402">从 EF Core 3.0 开始，`ILoggerFactory` 现已注册为作用域。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-402">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

**<span data-ttu-id="0eaf9-403">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-403">Why</span></span>**

<span data-ttu-id="0eaf9-404">此更改是为了允许记录器与 `DbContext` 实例关联，从而启用其他功能并删除一些反常行为，例如内部服务提供商爆炸式增长的情况。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-404">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

**<span data-ttu-id="0eaf9-405">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-405">Mitigations</span></span>**

<span data-ttu-id="0eaf9-406">除非在 EF Core 内部服务提供商上注册和使用自定义服务，否则此更改不应影响应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-406">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="0eaf9-407">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-407">This isn't common.</span></span>
<span data-ttu-id="0eaf9-408">在这些情况下，大多数事情仍然有效，但是需要更改依赖于 `ILoggerFactory` 的任何单一实例服务以便以不同的方式获取 `ILoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-408">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="0eaf9-409">如果遇到此类情况，请在 [EF Core GitHub 问题跟踪程序](https://github.com/aspnet/EntityFrameworkCore/issues)上提交一个问题，让我们知道你是如何使用 `ILoggerFactory` 的，以便我们更好地理解今后如何避免这种情况再次发生。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-409">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="0eaf9-410">IDbContextOptionsExtensionWithDebugInfo 合并为 IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="0eaf9-410">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="0eaf9-411">跟踪问题 #13552</span><span class="sxs-lookup"><span data-stu-id="0eaf9-411">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="0eaf9-412">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-412">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-413">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-413">Old behavior</span></span>**

`IDbContextOptionsExtensionWithDebugInfo` <span data-ttu-id="0eaf9-414">是从 `IDbContextOptionsExtension` 扩展得到的额外可选界面，用于避免在 2.x 版本周期中对界面进行中断性更改。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-414">was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

**<span data-ttu-id="0eaf9-415">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-415">New behavior</span></span>**

<span data-ttu-id="0eaf9-416">接口现在合并到 `IDbContextOptionsExtension` 中。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-416">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

**<span data-ttu-id="0eaf9-417">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-417">Why</span></span>**

<span data-ttu-id="0eaf9-418">之所以进行此更改，是因为所有接口在概念上是一个接口。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-418">This change was made because the interfaces are conceptually one.</span></span>

**<span data-ttu-id="0eaf9-419">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-419">Mitigations</span></span>**

<span data-ttu-id="0eaf9-420">任何 `IDbContextOptionsExtension` 实现都需要更新以支持新成员。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-420">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="0eaf9-421">延迟加载代理不再假定导航属性已完全加载</span><span class="sxs-lookup"><span data-stu-id="0eaf9-421">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="0eaf9-422">跟踪问题 #12780</span><span class="sxs-lookup"><span data-stu-id="0eaf9-422">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="0eaf9-423">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-423">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-424">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-424">Old behavior</span></span>**

<span data-ttu-id="0eaf9-425">在 EF Core 3.0 之前，一旦 `DbContext` 被处置，就无法知道从该上下文获得的实体上的给定导航属性是否已完全加载。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-425">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="0eaf9-426">相反，如果导航有一个非 null 值，代理将假定加载一个引用导航，如果导航非空，则假定加载集合导航。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-426">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="0eaf9-427">在这些情况下，尝试延迟加载将是无效的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-427">In these cases, attempting to lazy-load would be a no-op.</span></span>

**<span data-ttu-id="0eaf9-428">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-428">New behavior</span></span>**

<span data-ttu-id="0eaf9-429">从 EF Core 3.0 开始，代理会跟踪是否加载了导航属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-429">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="0eaf9-430">这意味着如果尝试访问在释放了上下文之后加载的导航属性，其结果始终是无操作，即使加载的导航为空或为 null。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-430">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="0eaf9-431">相反，即使导航属性是非空集合，尝试访问未加载的导航属性也会引发异常。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-431">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="0eaf9-432">如果出现这种情况，则表示应用程序代码在无效时间尝试使用延迟加载，应将应用程序更改为不执行此操作。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-432">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

**<span data-ttu-id="0eaf9-433">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-433">Why</span></span>**

<span data-ttu-id="0eaf9-434">此更改是为了在尝试对已释放的 `DbContext` 实例进行延迟加载时使行为保持一致和正确。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-434">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

**<span data-ttu-id="0eaf9-435">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-435">Mitigations</span></span>**

<span data-ttu-id="0eaf9-436">更新应用程序代码，以避免尝试对已释放的上下文进行延迟加载，或者将其配置为无操作，如异常消息中所述。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-436">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="0eaf9-437">默认情况下，现在过度创建内部服务提供程序是一个错误</span><span class="sxs-lookup"><span data-stu-id="0eaf9-437">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="0eaf9-438">跟踪问题 #10236</span><span class="sxs-lookup"><span data-stu-id="0eaf9-438">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="0eaf9-439">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-439">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-440">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-440">Old behavior</span></span>**

<span data-ttu-id="0eaf9-441">在 EF Core 3.0 之前，对于创建了大量内部服务提供程序的应用程序，将会记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-441">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

**<span data-ttu-id="0eaf9-442">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-442">New behavior</span></span>**

<span data-ttu-id="0eaf9-443">从 EF Core 3.0 开始，现在会考虑此警告，并引发错误和异常。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-443">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

**<span data-ttu-id="0eaf9-444">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-444">Why</span></span>**

<span data-ttu-id="0eaf9-445">此更改是为了通过更明显地暴露这个病态案例来驱动生成更好的应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-445">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

**<span data-ttu-id="0eaf9-446">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-446">Mitigations</span></span>**

<span data-ttu-id="0eaf9-447">遇到此错误时，最合适的操作是了解根本原因并停止创建如此多的内部服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-447">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="0eaf9-448">但是，可以通过 `DbContextOptionsBuilder` 上的配置将错误转换回警告（或忽略）。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-448">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="0eaf9-449">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-449">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="0eaf9-450">使用单个字符串调用 HasOne/HasMany 的新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-450">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="0eaf9-451">跟踪问题 #9171</span><span class="sxs-lookup"><span data-stu-id="0eaf9-451">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="0eaf9-452">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-452">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-453">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-453">Old behavior</span></span>**

<span data-ttu-id="0eaf9-454">在 EF Core 3.0 之前，对通过单个字符串调用 `HasOne` 或 `HasMany` 的代码的解释令人困惑。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-454">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="0eaf9-455">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-455">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="0eaf9-456">该代码看起来是使用 `Entrance` 导航属性（可能是私有属性）将 `Samurai` 与某些其他实体类型关联起来的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-456">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="0eaf9-457">实际上，此代码试图创建与某个名为 `Entrance` 的实体类型的关系，该实体类型没有导航属性。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-457">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

**<span data-ttu-id="0eaf9-458">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-458">New behavior</span></span>**

<span data-ttu-id="0eaf9-459">从 EF Core 3.0 开始，上面的代码现执行它以前应执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-459">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

**<span data-ttu-id="0eaf9-460">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-460">Why</span></span>**

<span data-ttu-id="0eaf9-461">这一旧行为令人非常困惑，尤其是在读取配置代码和查找错误时。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-461">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

**<span data-ttu-id="0eaf9-462">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-462">Mitigations</span></span>**

<span data-ttu-id="0eaf9-463">这只会中断使用类型名称字符串显式配置关系而无需显式指定导航属性的应用程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-463">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="0eaf9-464">这并不常见。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-464">This is not common.</span></span>
<span data-ttu-id="0eaf9-465">以前的行为可以通过显式传递导航属性名称的 `null` 获得。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-465">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="0eaf9-466">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-466">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="0eaf9-467">多个异步方法的返回类型已从 Task 更改为 ValueTask</span><span class="sxs-lookup"><span data-stu-id="0eaf9-467">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="0eaf9-468">跟踪问题 #15184</span><span class="sxs-lookup"><span data-stu-id="0eaf9-468">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="0eaf9-469">此更改将在 EF Core 3.0-preview 4 中引入。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-469">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-470">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-470">Old behavior</span></span>**

<span data-ttu-id="0eaf9-471">以下异步方法之前返回的是 `Task<T>`：</span><span class="sxs-lookup"><span data-stu-id="0eaf9-471">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` <span data-ttu-id="0eaf9-472">（及派生类）</span><span class="sxs-lookup"><span data-stu-id="0eaf9-472">(and deriving classes)</span></span>

**<span data-ttu-id="0eaf9-473">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-473">New behavior</span></span>**

<span data-ttu-id="0eaf9-474">上述方法现返回一个 `ValueTask<T>`，其中 `T` 与前述相同。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-474">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

**<span data-ttu-id="0eaf9-475">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-475">Why</span></span>**

<span data-ttu-id="0eaf9-476">此更改会减少在调用这些方法时发生的堆分配数量，从而提高整体性能。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-476">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

**<span data-ttu-id="0eaf9-477">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-477">Mitigations</span></span>**

<span data-ttu-id="0eaf9-478">仅需重新编译只等待上述 API 的应用程序 -无需更改源。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-478">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="0eaf9-479">更复杂的用法（例如，将返回的 `Task` 传递到 `Task.WhenAny()`）通常需要通过对返回的 `ValueTask<T>` 调用 `AsTask()` 来将其转换为 `Task<T>`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-479">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="0eaf9-480">请注意，这会抵消此更改所带来的分配数减少优势。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-480">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="0eaf9-481">关系式：TypeMapping 注释现在只是 TypeMapping</span><span class="sxs-lookup"><span data-stu-id="0eaf9-481">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="0eaf9-482">跟踪问题 #9913</span><span class="sxs-lookup"><span data-stu-id="0eaf9-482">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="0eaf9-483">此更改是在 EF Core 3.0-preview 2 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-483">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="0eaf9-484">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-484">Old behavior</span></span>**

<span data-ttu-id="0eaf9-485">类型映射注释的注释名称是“Relational：TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-485">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

**<span data-ttu-id="0eaf9-486">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-486">New behavior</span></span>**

<span data-ttu-id="0eaf9-487">类型映射注释的注释名称现在是“TypeMapping”。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-487">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

**<span data-ttu-id="0eaf9-488">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-488">Why</span></span>**

<span data-ttu-id="0eaf9-489">类型映射现在不仅用于关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-489">Type mappings are now used for more than just relational database providers.</span></span>

**<span data-ttu-id="0eaf9-490">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-490">Mitigations</span></span>**

<span data-ttu-id="0eaf9-491">这只会中断直接作为注释访问类型映射的应用程序，这不常见。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-491">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="0eaf9-492">最合适的修复操作是使用 API 曲面来访问类型映射，而不是直接使用注释。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-492">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="0eaf9-493">派生类型上的 ToTable 会引发异常</span><span class="sxs-lookup"><span data-stu-id="0eaf9-493">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="0eaf9-494">跟踪问题 #11811</span><span class="sxs-lookup"><span data-stu-id="0eaf9-494">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="0eaf9-495">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-495">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-496">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-496">Old behavior</span></span>**

<span data-ttu-id="0eaf9-497">在 EF Core 3.0 之前，将忽略调用派生类型的 `ToTable()`，因为只有继承映射策略是 TPH，这是无效的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-497">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

**<span data-ttu-id="0eaf9-498">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-498">New behavior</span></span>**

<span data-ttu-id="0eaf9-499">从 EF Core 3.0 开始，同时为在以后的版本中添加 TPT 和 TPC 支持做准备，调用派生类型的 `ToTable()` 现在将引发异常，以避免将来发生意外的映射更改。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-499">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

**<span data-ttu-id="0eaf9-500">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-500">Why</span></span>**

<span data-ttu-id="0eaf9-501">目前，将派生类型映射到不同的表是无效的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-501">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="0eaf9-502">这种改变避免了在将来当它变为有效时被中断。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-502">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

**<span data-ttu-id="0eaf9-503">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-503">Mitigations</span></span>**

<span data-ttu-id="0eaf9-504">删除将派生类型映射到其他表的任何尝试。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-504">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="0eaf9-505">用 HasIndex 替换 ForSqlServerHasIndex</span><span class="sxs-lookup"><span data-stu-id="0eaf9-505">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="0eaf9-506">跟踪问题 #12366</span><span class="sxs-lookup"><span data-stu-id="0eaf9-506">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="0eaf9-507">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-507">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-508">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-508">Old behavior</span></span>**

<span data-ttu-id="0eaf9-509">在 EF Core 3.0 之前，`ForSqlServerHasIndex().ForSqlServerInclude()` 提供了一种配置与 `INCLUDE` 一起使用的列的方法。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-509">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

**<span data-ttu-id="0eaf9-510">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-510">New behavior</span></span>**

<span data-ttu-id="0eaf9-511">从 EF Core 3.0 开始，现在支持在关系级别上对索引使用 `Include`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-511">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="0eaf9-512">请使用 `HasIndex().ForSqlServerInclude()`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-512">Use `HasIndex().ForSqlServerInclude()`.</span></span>

**<span data-ttu-id="0eaf9-513">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-513">Why</span></span>**

<span data-ttu-id="0eaf9-514">此更改是为了将用于索引的 API 与 `Includes` 合并到一个位置，以供所有数据库提供程序使用。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-514">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

**<span data-ttu-id="0eaf9-515">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-515">Mitigations</span></span>**

<span data-ttu-id="0eaf9-516">使用新的 API，如上所示。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-516">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="0eaf9-517">EF Core 不再发送 pragma 来执行 SQLite FK</span><span class="sxs-lookup"><span data-stu-id="0eaf9-517">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="0eaf9-518">跟踪问题 #12151</span><span class="sxs-lookup"><span data-stu-id="0eaf9-518">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="0eaf9-519">此更改是在 EF Core 3.0-preview 3 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-519">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="0eaf9-520">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-520">Old behavior</span></span>**

<span data-ttu-id="0eaf9-521">在 EF Core 3.0 之前，当打开与 SQLite 的连接时，EF Core 会发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-521">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

**<span data-ttu-id="0eaf9-522">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-522">New behavior</span></span>**

<span data-ttu-id="0eaf9-523">从 EF Core 3.0 开始，当打开到 SQLite 的连接时，EF Core 不再发送 `PRAGMA foreign_keys = 1`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-523">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

**<span data-ttu-id="0eaf9-524">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-524">Why</span></span>**

<span data-ttu-id="0eaf9-525">之所以进行此更改，是因为 EF Core 默认使用 `SQLitePCLRaw.bundle_e_sqlite3`，这意味着 FK 强制执行操作在默认情况下是打开的，并且不需要在每次打开连接时显式启用。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-525">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

**<span data-ttu-id="0eaf9-526">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-526">Mitigations</span></span>**

<span data-ttu-id="0eaf9-527">默认情况下，SQLitePCLRaw.bundle_e_sqlite3 中启用了默认用于 EF Core 的外键。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-527">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="0eaf9-528">对于其他情况，可以通过在连接字符串中指定 `Foreign Keys=True` 来启用外键。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-528">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="0eaf9-529">Microsoft.EntityFrameworkCore.Sqlite 现在依赖于 SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="0eaf9-529">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

**<span data-ttu-id="0eaf9-530">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-530">Old behavior</span></span>**

<span data-ttu-id="0eaf9-531">在 EF Core 3.0 之前，EF Core 使用 `SQLitePCLRaw.bundle_green`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-531">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

**<span data-ttu-id="0eaf9-532">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-532">New behavior</span></span>**

<span data-ttu-id="0eaf9-533">从 EF Core 3.0 开始，EF Core 使用 `SQLitePCLRaw.bundle_e_sqlite3`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-533">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

**<span data-ttu-id="0eaf9-534">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-534">Why</span></span>**

<span data-ttu-id="0eaf9-535">此更改是为了使 iOS 上使用的 SQLite 版本与其他平台一致。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-535">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

**<span data-ttu-id="0eaf9-536">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-536">Mitigations</span></span>**

<span data-ttu-id="0eaf9-537">若要在 iOS 上使用本机 SQLite 版本，请配置 `Microsoft.Data.Sqlite` 以使用其他 `SQLitePCLRaw` 捆绑包。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-537">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="0eaf9-538">GUID 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="0eaf9-538">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="0eaf9-539">跟踪问题 #15078</span><span class="sxs-lookup"><span data-stu-id="0eaf9-539">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="0eaf9-540">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-540">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-541">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-541">Old behavior</span></span>**

<span data-ttu-id="0eaf9-542">GUID 值之前以 BLOB 值形式存储在 SQLite 上。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-542">Guid values were previously sored as BLOB values on SQLite.</span></span>

**<span data-ttu-id="0eaf9-543">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-543">New behavior</span></span>**

<span data-ttu-id="0eaf9-544">GUID 值现在以文本形式存储。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-544">Guid values are now sotred as TEXT.</span></span>

**<span data-ttu-id="0eaf9-545">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-545">Why</span></span>**

<span data-ttu-id="0eaf9-546">GUID 的二进制格式不会进行标准化。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-546">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="0eaf9-547">以文本形式存储值使数据库与其他技术更兼容。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-547">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

**<span data-ttu-id="0eaf9-548">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-548">Mitigations</span></span>**

<span data-ttu-id="0eaf9-549">现在通过执行如下的 SQL，可以将现有数据库转成新的格式。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-549">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="0eaf9-550">在 EF Core 中，通过对这些属性配置值转换器，还可以使用以前的行为模式继续操作。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-550">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="0eaf9-551">Microsoft.Data.Sqlite 仍然能够从“BLOB”和“文本”列读取 GUID 值；但是，由于参数和常量的默认格式已更改，可能需要针对涉及 GUID 的大多数情况采取措施。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-551">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="0eaf9-552">Char 值现在以文本形式存储在 SQLite 上</span><span class="sxs-lookup"><span data-stu-id="0eaf9-552">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="0eaf9-553">跟踪问题 #15020</span><span class="sxs-lookup"><span data-stu-id="0eaf9-553">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="0eaf9-554">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-554">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-555">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-555">Old behavior</span></span>**

<span data-ttu-id="0eaf9-556">Char 值之前以整数值形式存储在 SQLite 上。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-556">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="0eaf9-557">例如，A 的 char 值存储为整数值 65。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-557">For example, a char value of *A* was stored as the integer value 65.</span></span>

**<span data-ttu-id="0eaf9-558">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-558">New behavior</span></span>**

<span data-ttu-id="0eaf9-559">Char 值现在以文本形式存储。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-559">Char values are now sotred as TEXT.</span></span>

**<span data-ttu-id="0eaf9-560">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-560">Why</span></span>**

<span data-ttu-id="0eaf9-561">以文本形式存储值显得更加自然，并且使数据库与其他技术更兼容。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-561">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

**<span data-ttu-id="0eaf9-562">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-562">Mitigations</span></span>**

<span data-ttu-id="0eaf9-563">现在通过执行如下的 SQL，可以将现有数据库转成新的格式。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-563">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="0eaf9-564">在 EF Core 中，通过对这些属性配置值转换器，还可以使用以前的行为模式继续操作。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-564">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="0eaf9-565">Microsoft.Data.Sqlite 也仍然能够读取整数列和文本列的字符值，因此某些情况可能不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-565">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="0eaf9-566">现在使用固定区域性的日历生成迁移 ID</span><span class="sxs-lookup"><span data-stu-id="0eaf9-566">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="0eaf9-567">跟踪问题 #12978</span><span class="sxs-lookup"><span data-stu-id="0eaf9-567">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="0eaf9-568">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-568">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-569">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-569">Old behavior</span></span>**

<span data-ttu-id="0eaf9-570">以前使用当前区域性的日历无意生成迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-570">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

**<span data-ttu-id="0eaf9-571">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-571">New behavior</span></span>**

<span data-ttu-id="0eaf9-572">现在始终使用固定区域性的日历（公历）生成迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-572">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

**<span data-ttu-id="0eaf9-573">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-573">Why</span></span>**

<span data-ttu-id="0eaf9-574">更新数据库或解决合并冲突时，迁移的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-574">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="0eaf9-575">使用固定日历可以避免因团队成员采用不同系统日历而产生的顺序问题。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-575">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

**<span data-ttu-id="0eaf9-576">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-576">Mitigations</span></span>**

<span data-ttu-id="0eaf9-577">如果有人使用年份时间大于公历日历的非公历日历（如泰国佛历），则会受到影响。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-577">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="0eaf9-578">现有迁移 ID 需要进行更新，以便新迁移排在现有迁移之后。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-578">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="0eaf9-579">在迁移的设计者文件的 Migration 属性中可以找到迁移 ID。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-579">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="0eaf9-580">迁移历史记录表还需要更新。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-580">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="0eaf9-581">已重命名 LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="0eaf9-581">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="0eaf9-582">跟踪问题 #10985</span><span class="sxs-lookup"><span data-stu-id="0eaf9-582">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="0eaf9-583">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-583">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-584">更改</span><span class="sxs-lookup"><span data-stu-id="0eaf9-584">Change</span></span>**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` <span data-ttu-id="0eaf9-585">已重命名为 `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-585">has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

**<span data-ttu-id="0eaf9-586">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-586">Why</span></span>**

<span data-ttu-id="0eaf9-587">将此警告事件的命名方式与所有其他警告事件保持一致。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-587">Aligns the naming of this warning event with all other warning events.</span></span>

**<span data-ttu-id="0eaf9-588">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-588">Mitigations</span></span>**

<span data-ttu-id="0eaf9-589">使用新名称。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-589">Use the new name.</span></span> <span data-ttu-id="0eaf9-590">（注意：事件 ID 号码未更改。）</span><span class="sxs-lookup"><span data-stu-id="0eaf9-590">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="0eaf9-591">阐明 API 的外键约束名称</span><span class="sxs-lookup"><span data-stu-id="0eaf9-591">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="0eaf9-592">跟踪问题 #10730</span><span class="sxs-lookup"><span data-stu-id="0eaf9-592">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="0eaf9-593">此更改是在 EF Core 3.0-preview 4 中引入的。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-593">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="0eaf9-594">旧行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-594">Old behavior</span></span>**

<span data-ttu-id="0eaf9-595">在 EF Core 3.0 之前，外键约束名称被简单地称为“名称”。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-595">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="0eaf9-596">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-596">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

**<span data-ttu-id="0eaf9-597">新行为</span><span class="sxs-lookup"><span data-stu-id="0eaf9-597">New behavior</span></span>**

<span data-ttu-id="0eaf9-598">从 EF Core 3.0 开始，外键约束名称现在被称为“约束名称”。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-598">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constaint name".</span></span> <span data-ttu-id="0eaf9-599">例如:</span><span class="sxs-lookup"><span data-stu-id="0eaf9-599">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

**<span data-ttu-id="0eaf9-600">原因</span><span class="sxs-lookup"><span data-stu-id="0eaf9-600">Why</span></span>**

<span data-ttu-id="0eaf9-601">这样不仅可以让此领域的命名方式保持一致，还阐明了这是外键约束的名称，不是定义外键所依据的列或属性名称。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-601">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constaint, and not the column or property name that the foreign key is defined on.</span></span>

**<span data-ttu-id="0eaf9-602">缓解措施</span><span class="sxs-lookup"><span data-stu-id="0eaf9-602">Mitigations</span></span>**

<span data-ttu-id="0eaf9-603">使用新名称。</span><span class="sxs-lookup"><span data-stu-id="0eaf9-603">Use the new name.</span></span>
