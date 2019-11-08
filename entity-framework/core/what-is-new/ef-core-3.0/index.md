---
title: Entity Framework Core 3.0 中的新功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: 24368b4c87e785e779b3f2b2f10de19766451c9b
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656235"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="3cb1f-102">Entity Framework Core 3.0 中的新功能</span><span class="sxs-lookup"><span data-stu-id="3cb1f-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="3cb1f-103">以下列表包括 EF Core 3.0 的主要新功能。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="3cb1f-104">EF Core 3.0 是一个主要版本，还包含多个[重大变更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，即可能对现有应用程序产生负面影响的 API 改进。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="3cb1f-105">LINQ 检查</span><span class="sxs-lookup"><span data-stu-id="3cb1f-105">LINQ overhaul</span></span>

<span data-ttu-id="3cb1f-106">通过 LINQ，可使用所选 .NET 语言编写数据库查询，利用丰富的类型信息来提供 IntelliSense 和编译时类型检查。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="3cb1f-107">而 LINQ 还允许编写包含任意表达式（方法调用或操作）的不限数量的复杂查询。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="3cb1f-108">如何处理所有的这些组合是 LINQ 提供程序面临的主要挑战。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="3cb1f-109">在 EF Core 3.0 中，我们重构了 LINQ 提供程序，以便能够将更多的查询模式转换为 SQL，可以在更多场景下生成高效的查询，并防止未检测出效率低的查询。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="3cb1f-110">新的 LINQ 提供程序是能够在未来版本中提供新的查询功能和性能改进并且无需中断现有应用程序和数据提供程序的基础。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="3cb1f-111">客户端评估受限</span><span class="sxs-lookup"><span data-stu-id="3cb1f-111">Restricted client evaluation</span></span>

<span data-ttu-id="3cb1f-112">最重大的设计变更是必须解决如何处理无法转换为参数或无法转换为 SQL 的 LINQ 表达式。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="3cb1f-113">在前几个版本中，EF Core 明确查询的哪些部分可以转换为 SQL，并在客户端上执行查询的其余部分。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="3cb1f-114">在某些情况下，这种客户端执行是可取的，但在其他许多情况下，这可能会导致低效的查询。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="3cb1f-115">例如，如果 EF Core 2.2 无法转换 `Where()` 调用中的谓词，则会执行一个不带筛选器的 SQL 语句，从数据库传输所有行，然后在内存中对其进行筛选：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="3cb1f-116">如果数据库包含少量行，那么这可能是可以接受的，但如果数据库包含大量行，则可能导致严重的性能问题甚至应用程序故障。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>

<span data-ttu-id="3cb1f-117">在 EF Core 3.0 中，我们限制客户端评估仅发生在顶级投影（基本上为最后一次调用 `Select()`）上。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="3cb1f-118">当 EF Core 3.0 检测到无法在查询中的任何其他位置转换的表达式时，它会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="3cb1f-119">若要按照上一个示例的方式在客户端上评估谓词条件，开发人员现在必须将查询评估显式切换为 LINQ to Objects：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="3cb1f-120">有关这对现有应用程序的影响的详细信息，请参阅[重大变更文档](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="3cb1f-121">每个 LINQ 查询有一个 SQL 语句</span><span class="sxs-lookup"><span data-stu-id="3cb1f-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="3cb1f-122">3\.0 中的另一个重大设计变更为：现在始终为每个 LINQ 查询生成一个 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="3cb1f-123">在前面的版本中，在某些场景下我们通常生成多个 SQL 语句，例如在集合导航属性上转换 `Include()` 调用，以及转换遵循某些包含子查询的模式的查询。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-123">In previous versions, we used to generate multiple SQL statements in certain cases, translated `Include()` calls on collection navigation properties and translated queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="3cb1f-124">尽管在某些场景下这种做法很方便，并且对于 `Include()` 而言，这甚至有助于避免通过线路发送冗余数据，但实现较为复杂，这会导致一些效率极为低下的行为（N+1 个查询）。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, and it resulted in some extremely inefficient behaviors (N+1 queries).</span></span> <span data-ttu-id="3cb1f-125">在一些情况下多个查询返回的数据可能不一致。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-125">There were situations in which the data returned across multiple queries was potentially inconsistent.</span></span>

<span data-ttu-id="3cb1f-126">与客户端评估类似，若 EF Core 3.0 无法将 LINQ 查询转换为单个 SQL 语句，它将引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-126">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="3cb1f-127">但我们已使 EF Core 能够借助联接将用于生成多个查询的许多常见模式转换为单个查询。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-127">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="3cb1f-128">Cosmos DB 支持</span><span class="sxs-lookup"><span data-stu-id="3cb1f-128">Cosmos DB support</span></span>

<span data-ttu-id="3cb1f-129">通过 EF Core 的 Cosmos DB 提供程序，熟悉 EF 编程模型的开发人员可以轻松地将 Azure Cosmos DB 定为应用程序数据库目标。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-129">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="3cb1f-130">目标是利用 Cosmos DB 的一些优势，如全局分发、“始终开启”可用性、弹性可伸缩性和低延迟，甚至包括 .NET 开发人员可以更轻松地访问它。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-130">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="3cb1f-131">此提供程序将针对 Cosmos DB 中的 SQL API 启用大部分 EF Core 功能，如自动更改跟踪、LINQ 和值转换。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-131">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="3cb1f-132">有关详细信息，请参阅 [Cosmos DB 提供程序文档](xref:core/providers/cosmos/index)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-132">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="3cb1f-133">C#8.0 支持</span><span class="sxs-lookup"><span data-stu-id="3cb1f-133">C# 8.0 support</span></span>

<span data-ttu-id="3cb1f-134">EF Core 3.0 利用了 [C#8.0 中的一些新功能](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8)：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-134">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="3cb1f-135">异步流</span><span class="sxs-lookup"><span data-stu-id="3cb1f-135">Asynchronous streams</span></span>

<span data-ttu-id="3cb1f-136">异步查询结果现在使用新的标准 `IAsyncEnumerable<T>` 接口公开，并且可以通过 `await foreach` 使用。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-136">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

<span data-ttu-id="3cb1f-137">有关详细信息，请参阅 [C# 文档中的异步流](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-137">See the [asynchronous streams in the C# documentation](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="3cb1f-138">可为空引用类型</span><span class="sxs-lookup"><span data-stu-id="3cb1f-138">Nullable reference types</span></span>

<span data-ttu-id="3cb1f-139">在代码中启用此新功能后，EF Core 将检查引用类型属性的为 Null 性，并将它应用到数据库中的相应列和关系：将按照不可为 Null 的引用类型的属性具有 `[Required]` 数据注释属性来处理它们。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-139">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="3cb1f-140">例如，在下面的类中，将把标记为类型 `string?` 的属性配置为可选，而将 `string` 配置为必需：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-140">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

<span data-ttu-id="3cb1f-141">有关详细信息，请参阅 EF Core 文档中的[处理可为 Null 的引用类型](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-141">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="3cb1f-142">截获数据库操作</span><span class="sxs-lookup"><span data-stu-id="3cb1f-142">Interception of database operations</span></span>

<span data-ttu-id="3cb1f-143">EF Core 3.0 中的新截获 API 允许提供自定义逻辑，以便在发生低级别数据库操作时作为 EF Core 正常运行的一部分自动调用它们。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-143">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="3cb1f-144">例如，打开连接、提交事务或执行命令时。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-144">For example, when opening connections, committing transactions, or executing commands.</span></span>

<span data-ttu-id="3cb1f-145">与 EF 6 中的截获功能相似，借助侦听器，你可以在操作发生之前或之后拦截它们。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-145">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="3cb1f-146">在操作发生前截获它们时，将允许你绕过执行，并从截获逻辑提供备用结果。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-146">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span>

<span data-ttu-id="3cb1f-147">例如，若要操作命令文本，可以创建 `IDbCommandInterceptor`：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-147">For example, to manipulate command text, you can create an `IDbCommandInterceptor`:</span></span>

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

<span data-ttu-id="3cb1f-148">然后将它注册到  `DbContext`：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-148">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="3cb1f-149">数据库视图的反向工程</span><span class="sxs-lookup"><span data-stu-id="3cb1f-149">Reverse engineering of database views</span></span>

<span data-ttu-id="3cb1f-150">查询类型表示可从数据库读取但无法更新的数据，它已重命名为[无键实体类型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-150">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span>
<span data-ttu-id="3cb1f-151">由于它们非常适用于映射多数场景中的数据库视图，当执行数据库视图反向工程时，EF Core 现在将自动创建无键实体类型。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-151">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="3cb1f-152">例如，利用 [dotnet ef 命令行工具](xref:core/miscellaneous/cli/dotnet)，可以键入：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-152">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

``` console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="3cb1f-153">此工具现在将自动为无键的视图和表构架类型：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-153">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="3cb1f-154">与主体共享表的依赖实体现为可选项</span><span class="sxs-lookup"><span data-stu-id="3cb1f-154">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="3cb1f-155">自 EF Core 3.0 起，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表中，则它将可能添加 `Order` 而不添加 `OrderDetails`，并且除主键外的所有 `OrderDetails` 属性都将映射到不为 null 的列中。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-155">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="3cb1f-156">查询时，如果其任意所需属性均没有值，或者它在主键之外没有任何必需属性且所有属性均为 `null`，则 EF Core 会将 `OrderDetails` 设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-156">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="3cb1f-157">.NET Core 上的 EF 6.3</span><span class="sxs-lookup"><span data-stu-id="3cb1f-157">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="3cb1f-158">这并不是真正的 EF Core 3.0 功能，但我们认为这对当前客户而言非常重要。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-158">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span>

<span data-ttu-id="3cb1f-159">我们知道许多现有应用程序使用以前版本的 EF，而仅为了利用 .NET Core 将其移植到 EF Core 需要大量工作。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-159">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span>
<span data-ttu-id="3cb1f-160">为此，我们已决定将最新版本的 EF 6 移植为在 .NET Core 3.0 上运行。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-160">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span>

<span data-ttu-id="3cb1f-161">有关更多详细信息，请参阅 [EF 6 中的新增功能](xref:ef6/what-is-new/index)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-161">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="3cb1f-162">推迟的功能</span><span class="sxs-lookup"><span data-stu-id="3cb1f-162">Postponed features</span></span>

<span data-ttu-id="3cb1f-163">最初计划为 EF Core 3.0 提供的一些功能已推迟到将来的版本：</span><span class="sxs-lookup"><span data-stu-id="3cb1f-163">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="3cb1f-164">在迁移部分忽略模型的功能，跟踪编号为 [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-164">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="3cb1f-165">属性包实体，由两个单独的问题跟踪：关于共享类型实体的 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) 和关于索引属性映射支持的 [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。</span><span class="sxs-lookup"><span data-stu-id="3cb1f-165">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
