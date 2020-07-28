---
title: EF Core 5.0 中的新增功能
description: EF Core 5.0 中的新功能概述
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d42b2811d07516e9febedbc51fcb206000d38371
ms.sourcegitcommit: 51148929e3889c48227d96c95c4e310d53a3d2c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86873378"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="6ba72-103">EF Core 5.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="6ba72-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="6ba72-104">EF Core 5.0 目前正在开发中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="6ba72-105">此页面将包含每个预览版中引入的令人关注的更改的简要介绍。</span><span class="sxs-lookup"><span data-stu-id="6ba72-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="6ba72-106">此页面不会复制 [EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan) 的计划。</span><span class="sxs-lookup"><span data-stu-id="6ba72-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="6ba72-107">计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。</span><span class="sxs-lookup"><span data-stu-id="6ba72-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="6ba72-108">发布时，我们会将此处的链接添加到官方文档。</span><span class="sxs-lookup"><span data-stu-id="6ba72-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="6ba72-109">预览版 7</span><span class="sxs-lookup"><span data-stu-id="6ba72-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="6ba72-110">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="6ba72-110">DbContextFactory</span></span>

<span data-ttu-id="6ba72-111">EF Core 5.0 引入了 `AddDbContextFactory` 和 `AddPooledDbContextFactory`，可用于在应用程序的依赖关系注入 (D.I.) 容器中注册工厂来创建 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="6ba72-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="6ba72-112">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="6ba72-113">应用程序服务（例如 ASP.NET Core 控制器）之后可依赖于服务构造函数中的 `IDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="6ba72-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="6ba72-114">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-114">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="6ba72-115">之后可根据需要创建和使用 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="6ba72-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="6ba72-116">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-116">For example:</span></span>

```csharp
public void DoSomehing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="6ba72-117">请注意，以这种方式创建的 DbContext 实例并非由应用程序的服务提供程序进行管理，因此必须由应用程序释放。</span><span class="sxs-lookup"><span data-stu-id="6ba72-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="6ba72-118">此类分离对于 Blazor 应用程序非常有用（此情况下建议使用 `IDbContextFactory`），但在其他方案中也很有用。</span><span class="sxs-lookup"><span data-stu-id="6ba72-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="6ba72-119">你可通过调用 `AddPooledDbContextFactory` 来共用 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="6ba72-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="6ba72-120">此类共用的原理与 `AddDbContextPool` 相同，且限制也相同。</span><span class="sxs-lookup"><span data-stu-id="6ba72-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="6ba72-121">记录信息可通过问题 [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="6ba72-122">重置 DbContext 状态</span><span class="sxs-lookup"><span data-stu-id="6ba72-122">Reset DbContext state</span></span>

<span data-ttu-id="6ba72-123">EF Core 5.0 引入了 `ChangeTracker.Clear()`，它可清除所有被跟踪实体的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="6ba72-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="6ba72-124">当使用为每个工作单元创建生存期较短的新上下文实例的最佳做法时，通常不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="6ba72-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="6ba72-125">但如果需要重置 DbContext 实例的状态，则使用新的 `Clear()` 方法比大量分离所有实体的性能和可靠性更强。</span><span class="sxs-lookup"><span data-stu-id="6ba72-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="6ba72-126">记录信息可通过问题 [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="6ba72-127">存储生成的默认值的新模式</span><span class="sxs-lookup"><span data-stu-id="6ba72-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="6ba72-128">EF Core 允许为可能还有默认值约束的列设置显式值。</span><span class="sxs-lookup"><span data-stu-id="6ba72-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="6ba72-129">EF Core 使用 type 属性类型的 CLR 默认值作为此项的 sentinel；如果该值不是 CLR 默认值，则会将其插入，否则将使用数据库默认值。</span><span class="sxs-lookup"><span data-stu-id="6ba72-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="6ba72-130">这会使 CLR 默认值不是良好的 sentinel（最值得注意的是 `bool` 属性）的类型出现问题。</span><span class="sxs-lookup"><span data-stu-id="6ba72-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="6ba72-131">EF Core 5.0 现允许支持字段在此类情况下可为 null。</span><span class="sxs-lookup"><span data-stu-id="6ba72-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="6ba72-132">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-132">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="6ba72-133">请注意，支持字段可为 null，但公开属性不可为 null。</span><span class="sxs-lookup"><span data-stu-id="6ba72-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="6ba72-134">这允许 sentinel 值为 `null`，而不影响实体类型的公开内容。</span><span class="sxs-lookup"><span data-stu-id="6ba72-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="6ba72-135">在本例中，如果绝不设置 `IsValid`，则将使用数据库默认值，因为支持字段仍为 null。</span><span class="sxs-lookup"><span data-stu-id="6ba72-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="6ba72-136">如果设置 `true` 或 `false`，则此值将显式保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="6ba72-137">记录信息可通过问题 [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="6ba72-138">Cosmos 分区键</span><span class="sxs-lookup"><span data-stu-id="6ba72-138">Cosmos partition keys</span></span>

<span data-ttu-id="6ba72-139">EF Core 允许将 Cosmos 分区键包含在 EF 模型中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="6ba72-140">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="6ba72-141">从预览版 7 开始，分区键将包含在实体类型的 PK 中，用于改进某些查询的性能。</span><span class="sxs-lookup"><span data-stu-id="6ba72-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="6ba72-142">记录信息可通过问题 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="6ba72-143">Cosmos 配置</span><span class="sxs-lookup"><span data-stu-id="6ba72-143">Cosmos configuration</span></span>

<span data-ttu-id="6ba72-144">EF Core 5.0 改进了 Cosmos 和 Cosmos 连接的配置。</span><span class="sxs-lookup"><span data-stu-id="6ba72-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="6ba72-145">之前，EF Core 需要在连接到 Cosmos 数据库时显式指定终结点和密钥。</span><span class="sxs-lookup"><span data-stu-id="6ba72-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="6ba72-146">而 EF Core 5.0 允许使用连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6ba72-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="6ba72-147">此外，EF Core 5.0 还允许显式设置 WebProxy 实例。</span><span class="sxs-lookup"><span data-stu-id="6ba72-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="6ba72-148">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="6ba72-149">现在还可配置许多其他超时值、限制等。</span><span class="sxs-lookup"><span data-stu-id="6ba72-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="6ba72-150">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-150">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="6ba72-151">最后，默认的连接模式现为 `ConnectionMode.Gateway`，兼容性通常更好。</span><span class="sxs-lookup"><span data-stu-id="6ba72-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="6ba72-152">记录信息可通过问题 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="6ba72-153">Scaffold-DbContext 现为单数形式</span><span class="sxs-lookup"><span data-stu-id="6ba72-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="6ba72-154">此前，当从现有数据库搭建 DbContext 时，EF Core 将创建与数据库中的表名称匹配的实体类型名称。</span><span class="sxs-lookup"><span data-stu-id="6ba72-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="6ba72-155">例如，表 `People` 和 `Addresses` 对应的实体类型名称为 `People` 和 `Addresses`。</span><span class="sxs-lookup"><span data-stu-id="6ba72-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="6ba72-156">在之前的版本中，此行为是通过注册复数化服务来配置的。</span><span class="sxs-lookup"><span data-stu-id="6ba72-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="6ba72-157">现在，在 EF Core 5.0 中，[Humanizer](https://www.nuget.org/packages/Humanizer.Core/) 包用作默认复数化服务。</span><span class="sxs-lookup"><span data-stu-id="6ba72-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="6ba72-158">这意味着 `People` 和 `Addresses` 现将被反向工程处理为名叫 `Person` 和 `Address` 的实体类型。</span><span class="sxs-lookup"><span data-stu-id="6ba72-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="6ba72-159">保存点</span><span class="sxs-lookup"><span data-stu-id="6ba72-159">Savepoints</span></span>

<span data-ttu-id="6ba72-160">EF Core 现支持[保存点](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks)，它们可更好地控制执行多个操作的事务。</span><span class="sxs-lookup"><span data-stu-id="6ba72-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="6ba72-161">你可手动创建、发布和回滚保存点。</span><span class="sxs-lookup"><span data-stu-id="6ba72-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="6ba72-162">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="6ba72-163">此外，在执行 `SaveChanges` 失败时，EF Core 现在会回滚到上一个保存点。</span><span class="sxs-lookup"><span data-stu-id="6ba72-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="6ba72-164">因此，可重试 SaveChanges，而不用重试整个事务。</span><span class="sxs-lookup"><span data-stu-id="6ba72-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="6ba72-165">记录信息可通过问题 [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="6ba72-166">预览版 6</span><span class="sxs-lookup"><span data-stu-id="6ba72-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="6ba72-167">适合相关集合的拆分查询</span><span class="sxs-lookup"><span data-stu-id="6ba72-167">Split queries for related collections</span></span>

<span data-ttu-id="6ba72-168">从 EF Core 3.0 开始，EF Core 始终会为每个 LINQ 查询生成一个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="6ba72-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="6ba72-169">这可确保在使用中的事务模式的约束内返回的数据保持一致。</span><span class="sxs-lookup"><span data-stu-id="6ba72-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="6ba72-170">但是，当查询使用 `Include` 或投影来返回多个相关集合时，速度可能会变得非常慢。</span><span class="sxs-lookup"><span data-stu-id="6ba72-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="6ba72-171">EF Core 5.0 现允许将包含相关集合的一个 LINQ 查询拆分成多个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="6ba72-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="6ba72-172">这可能明显提升性能，但如果在两次查询之间数据发生了变化，则可能导致返回的结果不一致。</span><span class="sxs-lookup"><span data-stu-id="6ba72-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="6ba72-173">你能使用可序列化的事务或快照事务来缓解这种情况并通过拆分查询实现一致性，但这可能会带来其他性能成本并导致行为差异。</span><span class="sxs-lookup"><span data-stu-id="6ba72-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="6ba72-174">用 Include 拆分查询</span><span class="sxs-lookup"><span data-stu-id="6ba72-174">Split queries with Include</span></span>

<span data-ttu-id="6ba72-175">例如，请考虑使用 `Include` 提取两个级别的相关集合的查询：</span><span class="sxs-lookup"><span data-stu-id="6ba72-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="6ba72-176">默认情况下，EF Core 将使用 SQLite 提供程序生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="6ba72-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="6ba72-177">可使用新的 `AsSplitQuery` API 来更改此行为。</span><span class="sxs-lookup"><span data-stu-id="6ba72-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="6ba72-178">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="6ba72-179">AsSplitQuery 适用于所有关系数据库提供程序，且如同 AsNoTracking 一样，可在查询中的任何位置使用。</span><span class="sxs-lookup"><span data-stu-id="6ba72-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="6ba72-180">EF Core 现将生成以下 3 个 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="6ba72-180">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="6ba72-181">支持对查询根目录执行各种操作。</span><span class="sxs-lookup"><span data-stu-id="6ba72-181">All operations on the query root are supported.</span></span> <span data-ttu-id="6ba72-182">其中包括 OrderBy/Skip/Take、联接操作、FirstOrDefault 和类似的单结果选择操作。</span><span class="sxs-lookup"><span data-stu-id="6ba72-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="6ba72-183">请注意，预览版 6 中不支持带 OrderBy/Skip/Take 的 Filtered Include，但它们可在日常版本中使用，且将包含在预览版 7 中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="6ba72-184">用集合投影来拆分查询</span><span class="sxs-lookup"><span data-stu-id="6ba72-184">Split queries with collection projections</span></span>

<span data-ttu-id="6ba72-185">在投影中加载集合时，也可使用 `AsSplitQuery`。</span><span class="sxs-lookup"><span data-stu-id="6ba72-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="6ba72-186">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="6ba72-187">使用 SQLite 提供程序时，此 LINQ 查询会生成以下两个 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="6ba72-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="6ba72-188">请注意，仅支持将集合具体化。</span><span class="sxs-lookup"><span data-stu-id="6ba72-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="6ba72-189">上例中 `e.Albums` 之后的任何组合都不会产生拆分查询。</span><span class="sxs-lookup"><span data-stu-id="6ba72-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="6ba72-190">这方面的改进由 [#21234](https://github.com/dotnet/efcore/issues/21234) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="6ba72-191">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="6ba72-191">IndexAttribute</span></span>

<span data-ttu-id="6ba72-192">这个新的 IndexAttribute 可置于实体类型上来指定单列的索引。</span><span class="sxs-lookup"><span data-stu-id="6ba72-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="6ba72-193">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="6ba72-194">对于 SQL Server，迁移随后将生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="6ba72-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="6ba72-195">IndexAttribute 也可用于指定横跨多个列的索引。</span><span class="sxs-lookup"><span data-stu-id="6ba72-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="6ba72-196">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-196">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="6ba72-197">对于 SQL Server，这会导致：</span><span class="sxs-lookup"><span data-stu-id="6ba72-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="6ba72-198">记录信息可通过问题 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="6ba72-199">改进了查询转换异常时显示的消息</span><span class="sxs-lookup"><span data-stu-id="6ba72-199">Improved query translation exceptions</span></span>

<span data-ttu-id="6ba72-200">我们将继续改进在查询转换失败时生成的异常消息。</span><span class="sxs-lookup"><span data-stu-id="6ba72-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="6ba72-201">例如，以下查询使用未映射的属性 `IsSigned`：</span><span class="sxs-lookup"><span data-stu-id="6ba72-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="6ba72-202">EF Core 将引发以下异常，指出由于 `IsSigned` 未映射而导致转换失败：</span><span class="sxs-lookup"><span data-stu-id="6ba72-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="6ba72-203">未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="6ba72-203">Unhandled exception.</span></span> <span data-ttu-id="6ba72-204">System.InvalidOperationException：无法转换 LINQ 表达式 "DbSet<Artist>() .Where(a => a.IsSigned)"。</span><span class="sxs-lookup"><span data-stu-id="6ba72-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="6ba72-205">其他信息：实体类型 "Artist" 上成员 "IsSigned" 的转换失败。</span><span class="sxs-lookup"><span data-stu-id="6ba72-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="6ba72-206">可能未映射指定的成员。</span><span class="sxs-lookup"><span data-stu-id="6ba72-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="6ba72-207">请用可转换的形式重新编写查询，或者插入对 AsEnumerable()、AsAsyncEnumerable()、ToList() 或 ToListAsync() 的调用来显式切换到客户端评估。</span><span class="sxs-lookup"><span data-stu-id="6ba72-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="6ba72-208">有关更多信息，请参见 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="6ba72-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="6ba72-209">同样地，在尝试用依赖区域性的语义来转换字符串比较时，现将生成信息更丰富的异常消息。</span><span class="sxs-lookup"><span data-stu-id="6ba72-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="6ba72-210">例如，以下查询尝试使用 `StringComparison.CurrentCulture`：</span><span class="sxs-lookup"><span data-stu-id="6ba72-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="6ba72-211">EF Core 现将引发以下异常：</span><span class="sxs-lookup"><span data-stu-id="6ba72-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="6ba72-212">未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="6ba72-212">Unhandled exception.</span></span> <span data-ttu-id="6ba72-213">System.InvalidOperationException：无法转换 LINQ 表达式 "DbSet<Artist>() .Where(a => a.Name.Equals( value:"The Unicorns", comparisonType:CurrentCulture))"。</span><span class="sxs-lookup"><span data-stu-id="6ba72-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="6ba72-214">其他信息：不支持采用 "StringComparison" 的 "string.Equals" 方法的转换。</span><span class="sxs-lookup"><span data-stu-id="6ba72-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="6ba72-215">有关更多信息，请参见 https://go.microsoft.com/fwlink/?linkid=2129535 。</span><span class="sxs-lookup"><span data-stu-id="6ba72-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="6ba72-216">请用可转换的形式重新编写查询，或者插入对 AsEnumerable()、AsAsyncEnumerable()、ToList() 或 ToListAsync() 的调用来显式切换到客户端评估。</span><span class="sxs-lookup"><span data-stu-id="6ba72-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="6ba72-217">有关更多信息，请参见 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="6ba72-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="6ba72-218">指定事务 ID</span><span class="sxs-lookup"><span data-stu-id="6ba72-218">Specify transaction ID</span></span>

<span data-ttu-id="6ba72-219">此功能是 [@Marusyk](https://github.com/Marusyk) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="6ba72-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="6ba72-220">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="6ba72-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="6ba72-221">EF Core 公开一个事务 ID 来跨调用关联事务。</span><span class="sxs-lookup"><span data-stu-id="6ba72-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="6ba72-222">此 ID 通常是在启动事务时由 EF Core 设置的。</span><span class="sxs-lookup"><span data-stu-id="6ba72-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="6ba72-223">如果转而由应用程序启动事务，则应用程序可使用此功能来显式设置事务 ID，使其在所用的每个位置都正确关联。</span><span class="sxs-lookup"><span data-stu-id="6ba72-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="6ba72-224">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="6ba72-225">IPAddress 映射</span><span class="sxs-lookup"><span data-stu-id="6ba72-225">IPAddress mapping</span></span>

<span data-ttu-id="6ba72-226">此功能是 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="6ba72-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="6ba72-227">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="6ba72-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="6ba72-228">标准 .NET [IPAddress 类](/dotnet/api/system.net.ipaddress) 现自动映射到尚不具备原生支持的数据库的字符串列中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="6ba72-229">例如，请考虑映射以下实体类型：</span><span class="sxs-lookup"><span data-stu-id="6ba72-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="6ba72-230">在 SQL Server 上，这将导致迁移创建以下表：</span><span class="sxs-lookup"><span data-stu-id="6ba72-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="6ba72-231">之后，可按正常的方式添加实体：</span><span class="sxs-lookup"><span data-stu-id="6ba72-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="6ba72-232">所生成的 SQL 将插入规范化的 IPv4 或 IPv6 地址：</span><span class="sxs-lookup"><span data-stu-id="6ba72-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="6ba72-233">在创建基架时排除 OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="6ba72-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="6ba72-234">在基于现有数据库创建 DbContext 的基架时，EF Core 会默认创建一个 OnConfiguring 重载，该重载有一个连接字符串，以便上下文可立即供用户使用。</span><span class="sxs-lookup"><span data-stu-id="6ba72-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="6ba72-235">不过，如果你只有带 OnConfiguring 的分部类，或者你要用其他方式部署上下文，则此方法将不起作用。</span><span class="sxs-lookup"><span data-stu-id="6ba72-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="6ba72-236">若要解决这种情况，现可指示基架命令不生成 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="6ba72-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="6ba72-237">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="6ba72-238">或者在包管理器控制台中：</span><span class="sxs-lookup"><span data-stu-id="6ba72-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="6ba72-239">请注意，建议使用[命名的连接字符串和安全存储（如用户机密）](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets)。</span><span class="sxs-lookup"><span data-stu-id="6ba72-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="6ba72-240">转换字符串中的 FirstOrDefault</span><span class="sxs-lookup"><span data-stu-id="6ba72-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="6ba72-241">此功能是 [@dvoreckyaa](https://github.com/dvoreckyaa) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="6ba72-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="6ba72-242">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="6ba72-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="6ba72-243">现将转换字符串中字符的 FirstOrDefault 和类似运算符。</span><span class="sxs-lookup"><span data-stu-id="6ba72-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="6ba72-244">例如，使用 SQL Server 时，以下 LINQ 查询：</span><span class="sxs-lookup"><span data-stu-id="6ba72-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="6ba72-245">将转换为以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="6ba72-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="6ba72-246">简化 CASE 块</span><span class="sxs-lookup"><span data-stu-id="6ba72-246">Simplify case blocks</span></span>

<span data-ttu-id="6ba72-247">EF Core 现使用 CASE 块生成效果更佳的查询。</span><span class="sxs-lookup"><span data-stu-id="6ba72-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="6ba72-248">例如，以下 LINQ 查询：</span><span class="sxs-lookup"><span data-stu-id="6ba72-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="6ba72-249">之前在 SQL Server 上正式转换为：</span><span class="sxs-lookup"><span data-stu-id="6ba72-249">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

<span data-ttu-id="6ba72-250">但它现在转换为：</span><span class="sxs-lookup"><span data-stu-id="6ba72-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="6ba72-251">预览版 5</span><span class="sxs-lookup"><span data-stu-id="6ba72-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="6ba72-252">数据库排序规则</span><span class="sxs-lookup"><span data-stu-id="6ba72-252">Database collations</span></span>

<span data-ttu-id="6ba72-253">现可在 EF 模式中指定数据库的默认排序规则。</span><span class="sxs-lookup"><span data-stu-id="6ba72-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="6ba72-254">这将传输到生成的迁移，在创建数据库时设置排序规则。</span><span class="sxs-lookup"><span data-stu-id="6ba72-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="6ba72-255">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="6ba72-256">然后，迁移会生成以下内容，在 SQL Server 上创建数据库：</span><span class="sxs-lookup"><span data-stu-id="6ba72-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="6ba72-257">可指定用于特定数据库列的排序规则。</span><span class="sxs-lookup"><span data-stu-id="6ba72-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="6ba72-258">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="6ba72-259">对于不使用迁移的列，可在创建 DbContext 基架时从数据库对排序规则进行反向工程处理。</span><span class="sxs-lookup"><span data-stu-id="6ba72-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="6ba72-260">最后，可通过 `EF.Functions.Collate()` 使用不同的排序规则进行即席查询。</span><span class="sxs-lookup"><span data-stu-id="6ba72-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="6ba72-261">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="6ba72-262">这将为 SQL Server 生成以下查询：</span><span class="sxs-lookup"><span data-stu-id="6ba72-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="6ba72-263">请注意，应谨慎使用即席排序规则，因为它们可能会对数据库性能造成不良影响。</span><span class="sxs-lookup"><span data-stu-id="6ba72-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="6ba72-264">文档可通过问题 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="6ba72-265">将参数传输到 IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="6ba72-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="6ba72-266">参数现从命令行传输到 [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1) 的 `CreateDbContext` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ba72-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="6ba72-267">例如，若要指明这是开发生成，可以在命令行上传递自定义参数（例如 `dev`）：</span><span class="sxs-lookup"><span data-stu-id="6ba72-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="6ba72-268">然后，该参数将传输到工厂，它在这里可用于控制如何创建和初始化上下文。</span><span class="sxs-lookup"><span data-stu-id="6ba72-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="6ba72-269">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="6ba72-270">文档可通过问题 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="6ba72-271">具有标识解析的非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="6ba72-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="6ba72-272">现可将非跟踪查询配置来执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="6ba72-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="6ba72-273">例如，以下查询将为每个 Post 创建一个新的 Blog 实例，即使每个 Blog 的主键相同也是如此。</span><span class="sxs-lookup"><span data-stu-id="6ba72-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="6ba72-274">然而，可更改此查询来确保只创建一个 Blog 实例，代价通常是稍微拖慢一些速度和总是使用更多的内存：</span><span class="sxs-lookup"><span data-stu-id="6ba72-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="6ba72-275">请注意，这仅适用于非跟踪查询，因为所有跟踪查询已显示此行为。</span><span class="sxs-lookup"><span data-stu-id="6ba72-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="6ba72-276">此外，在 API 评审后，将更改 `PerformIdentityResolution` 语法。</span><span class="sxs-lookup"><span data-stu-id="6ba72-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="6ba72-277">请查看 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="6ba72-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="6ba72-278">文档可通过问题 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="6ba72-279">存储的（持久化）计算列</span><span class="sxs-lookup"><span data-stu-id="6ba72-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="6ba72-280">大多数数据库都允许在计算后存储计算得到的列值。</span><span class="sxs-lookup"><span data-stu-id="6ba72-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="6ba72-281">虽然这会占用磁盘空间，但仅在更新时对计算的列计算一次，而不是在每次检索它的值时都计算。</span><span class="sxs-lookup"><span data-stu-id="6ba72-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="6ba72-282">这样也可对某些数据库编制列的索引。</span><span class="sxs-lookup"><span data-stu-id="6ba72-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="6ba72-283">EF Core 5.0 允许将计算列配置为存储计算列。</span><span class="sxs-lookup"><span data-stu-id="6ba72-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="6ba72-284">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="6ba72-285">SQLite 计算列</span><span class="sxs-lookup"><span data-stu-id="6ba72-285">SQLite computed columns</span></span>

<span data-ttu-id="6ba72-286">EF Core 现支持在 SQLite 数据库中使用计算列。</span><span class="sxs-lookup"><span data-stu-id="6ba72-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="6ba72-287">预览版 4</span><span class="sxs-lookup"><span data-stu-id="6ba72-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="6ba72-288">在模型中配置数据库精度/小数位数</span><span class="sxs-lookup"><span data-stu-id="6ba72-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="6ba72-289">现在，可以使用模型生成器指定属性的精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="6ba72-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="6ba72-290">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="6ba72-291">还可以通过完整的数据库类型（如“decimal(16,4)”）设置精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="6ba72-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="6ba72-292">文档可通过问题 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="6ba72-293">指定 SQL Server 索引填充因子</span><span class="sxs-lookup"><span data-stu-id="6ba72-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="6ba72-294">现在可以在 SQL Server 上创建索引时指定填充因子。</span><span class="sxs-lookup"><span data-stu-id="6ba72-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="6ba72-295">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="6ba72-296">预览版 3</span><span class="sxs-lookup"><span data-stu-id="6ba72-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="6ba72-297">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="6ba72-297">Filtered Include</span></span>

<span data-ttu-id="6ba72-298">Include 方法现在支持筛选包含的实体。</span><span class="sxs-lookup"><span data-stu-id="6ba72-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="6ba72-299">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="6ba72-300">此查询将一并返回包含每个关联文章的博客（仅当文章标题包含“Cheese”时）。</span><span class="sxs-lookup"><span data-stu-id="6ba72-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="6ba72-301">Skip 和 Take 也可用于减少包含的实体数量。</span><span class="sxs-lookup"><span data-stu-id="6ba72-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="6ba72-302">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="6ba72-303">此查询将返回博客，每个博客最多包含 5 篇文章。</span><span class="sxs-lookup"><span data-stu-id="6ba72-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="6ba72-304">有关完整详细信息，请参阅 [Include 文档](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="6ba72-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="6ba72-305">用于导航属性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="6ba72-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="6ba72-306">导航属性主要在[定义关系](xref:core/modeling/relationships)时配置。</span><span class="sxs-lookup"><span data-stu-id="6ba72-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="6ba72-307">但是，在导航属性需要额外配置的情况下，可以使用新的 `Navigation` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ba72-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="6ba72-308">例如，如需在根据约定找不到支持字段时设置导航的支持字段，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6ba72-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="6ba72-309">请注意：`Navigation` API 不会替换关系配置。</span><span class="sxs-lookup"><span data-stu-id="6ba72-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="6ba72-310">但是，它允许在已发现或定义的关系中进行其他导航属性配置。</span><span class="sxs-lookup"><span data-stu-id="6ba72-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="6ba72-311">请参阅[配置导航属性文档](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="6ba72-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="6ba72-312">用于命名空间和连接字符串的新命令行参数</span><span class="sxs-lookup"><span data-stu-id="6ba72-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="6ba72-313">迁移和基架现在允许在命令行上指定命名空间。</span><span class="sxs-lookup"><span data-stu-id="6ba72-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="6ba72-314">例如，如需对数据库进行反向工程，将上下文和模型类放在不同的命名空间中：</span><span class="sxs-lookup"><span data-stu-id="6ba72-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="6ba72-315">有关完整详细信息，请参阅[迁移](xref:core/managing-schemas/migrations/index#namespaces)和[反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)文档。</span><span class="sxs-lookup"><span data-stu-id="6ba72-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="6ba72-316">此外，连接字符串现在可以传递到 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="6ba72-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="6ba72-317">有关完整的详细信息，请参阅[工具文档](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="6ba72-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="6ba72-318">等效参数已添加到 VS 程序包管理器控制台中使用的 PowerShell 命令中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="6ba72-319">EnableDetailedErrors 已返回</span><span class="sxs-lookup"><span data-stu-id="6ba72-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="6ba72-320">出于性能原因，在从数据库读取值时，EF 不会执行额外的 null 检查。</span><span class="sxs-lookup"><span data-stu-id="6ba72-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="6ba72-321">当遇到意外的 null 时，这可能会导致难以查找根本原因的异常。</span><span class="sxs-lookup"><span data-stu-id="6ba72-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="6ba72-322">使用 `EnableDetailedErrors` 会将额外的 null 检查添加到查询中，这样一来，对于较小的性能开销，这些错误就更容易追溯到根本原因。</span><span class="sxs-lookup"><span data-stu-id="6ba72-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="6ba72-323">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="6ba72-324">文档可通过问题 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="6ba72-325">Cosmos 分区键</span><span class="sxs-lookup"><span data-stu-id="6ba72-325">Cosmos partition keys</span></span>

<span data-ttu-id="6ba72-326">现在可以在查询中指定用于给定查询的分区键。</span><span class="sxs-lookup"><span data-stu-id="6ba72-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="6ba72-327">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="6ba72-328">文档可通过问题 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="6ba72-329">支持 SQL Server DATALENGTH 函数</span><span class="sxs-lookup"><span data-stu-id="6ba72-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="6ba72-330">可以使用新的 `EF.Functions.DataLength` 方法访问它。</span><span class="sxs-lookup"><span data-stu-id="6ba72-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="6ba72-331">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="6ba72-332">预览版 2</span><span class="sxs-lookup"><span data-stu-id="6ba72-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="6ba72-333">使用 C# 特性指定属性支持字段</span><span class="sxs-lookup"><span data-stu-id="6ba72-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="6ba72-334">现在，可以使用 C# 特性指定属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="6ba72-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="6ba72-335">使用此特性，即使在不能自动找到支持字段时，EF Core 也能正常读写支持字段。</span><span class="sxs-lookup"><span data-stu-id="6ba72-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="6ba72-336">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-336">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="6ba72-337">文档可通过问题 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="6ba72-338">完成鉴别器映射</span><span class="sxs-lookup"><span data-stu-id="6ba72-338">Complete discriminator mapping</span></span>

<span data-ttu-id="6ba72-339">EF Core 使用鉴别器列进行 [继承层次结构的 TPH 映射](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="6ba72-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="6ba72-340">只要 EF Core 知道该鉴别器的所有可能的值，就可能实现某些性能改进。</span><span class="sxs-lookup"><span data-stu-id="6ba72-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="6ba72-341">EF Core 5.0 现在可实现这些改进。</span><span class="sxs-lookup"><span data-stu-id="6ba72-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="6ba72-342">例如，对于返回层次结构中所有类型的查询，旧版 EF Core 总是会生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="6ba72-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="6ba72-343">配置完整的鉴别器映射后，EF Core 5.0 现在将生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="6ba72-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="6ba72-344">从预览版 3 开始，这将成为默认行为。</span><span class="sxs-lookup"><span data-stu-id="6ba72-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="6ba72-345">Microsoft.Data.Sqlite 中的性能改进</span><span class="sxs-lookup"><span data-stu-id="6ba72-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="6ba72-346">我们对 SQLIte 进行了两项性能改进：</span><span class="sxs-lookup"><span data-stu-id="6ba72-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="6ba72-347">现在，利用 SqliteBlob 和流，可以更高效地使用 GetBytes、GetChars 和 GetTextReader 检索二进制和字符串数据。</span><span class="sxs-lookup"><span data-stu-id="6ba72-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="6ba72-348">SqliteConnection 的初始化现在很慢。</span><span class="sxs-lookup"><span data-stu-id="6ba72-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="6ba72-349">这些改进已实施到 ADO.NET Microsoft.Data.Sqlite 提供程序中，因此还可以在 EF Core 之外提升性能。</span><span class="sxs-lookup"><span data-stu-id="6ba72-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="6ba72-350">预览版 1</span><span class="sxs-lookup"><span data-stu-id="6ba72-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="6ba72-351">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="6ba72-351">Simple logging</span></span>

<span data-ttu-id="6ba72-352">此特性会添加类似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="6ba72-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="6ba72-353">也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。</span><span class="sxs-lookup"><span data-stu-id="6ba72-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="6ba72-354">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="6ba72-355">其他文档可通过问题 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="6ba72-356">获取生成的 SQL 的简单方法</span><span class="sxs-lookup"><span data-stu-id="6ba72-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="6ba72-357">EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="6ba72-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="6ba72-358">初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="6ba72-359">其他文档可通过问题 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="6ba72-360">使用 C# 特性指示实体没有键</span><span class="sxs-lookup"><span data-stu-id="6ba72-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="6ba72-361">现在可以将实体类型配置为不使用新 `KeylessAttribute`的键。</span><span class="sxs-lookup"><span data-stu-id="6ba72-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="6ba72-362">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="6ba72-363">文档可通过问题 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="6ba72-364">可在初始化的 DbContext 上更改连接或连接字符串</span><span class="sxs-lookup"><span data-stu-id="6ba72-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="6ba72-365">现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6ba72-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="6ba72-366">而且，现在可以在上下文实例上更改连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6ba72-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="6ba72-367">此功能使得同一个上下文实例能够动态地连接到不同的数据库。</span><span class="sxs-lookup"><span data-stu-id="6ba72-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="6ba72-368">文档可通过问题 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="6ba72-369">更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="6ba72-369">Change-tracking proxies</span></span>

<span data-ttu-id="6ba72-370">EF Core 现在可以生成自动实现 [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) 的运行时代理。</span><span class="sxs-lookup"><span data-stu-id="6ba72-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="6ba72-371">这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。</span><span class="sxs-lookup"><span data-stu-id="6ba72-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="6ba72-372">不过，代理有其自身的一组限制，因此并不适合所有人使用。</span><span class="sxs-lookup"><span data-stu-id="6ba72-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="6ba72-373">文档可通过问题 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="6ba72-374">增强的调试视图</span><span class="sxs-lookup"><span data-stu-id="6ba72-374">Enhanced debug views</span></span>

<span data-ttu-id="6ba72-375">调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。</span><span class="sxs-lookup"><span data-stu-id="6ba72-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="6ba72-376">在一段时间之前，我们就已经实现了模型的调试视图。</span><span class="sxs-lookup"><span data-stu-id="6ba72-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="6ba72-377">对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。</span><span class="sxs-lookup"><span data-stu-id="6ba72-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="6ba72-378">初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="6ba72-379">其他文档可通过问题 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="6ba72-380">改进了对数据库 null 语义的处理</span><span class="sxs-lookup"><span data-stu-id="6ba72-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="6ba72-381">关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="6ba72-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="6ba72-382">而 C# 将 null 视为与其他任何 null 相比均相等的定义值。</span><span class="sxs-lookup"><span data-stu-id="6ba72-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="6ba72-383">默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。</span><span class="sxs-lookup"><span data-stu-id="6ba72-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="6ba72-384">EF Core 5.0 极大地提升了这些转换操作的效率。</span><span class="sxs-lookup"><span data-stu-id="6ba72-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="6ba72-385">文档可通过问题 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="6ba72-386">索引器属性</span><span class="sxs-lookup"><span data-stu-id="6ba72-386">Indexer properties</span></span>

<span data-ttu-id="6ba72-387">EF Core 5.0 支持 C# 索引器属性的映射。</span><span class="sxs-lookup"><span data-stu-id="6ba72-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="6ba72-388">这写属性使得实体能够充当属性包，实体中的列被映射为包中的命名属性。</span><span class="sxs-lookup"><span data-stu-id="6ba72-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="6ba72-389">文档可通过问题 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="6ba72-390">为枚举映射生成检查约束</span><span class="sxs-lookup"><span data-stu-id="6ba72-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="6ba72-391">EF Core 5.0 迁移现可为枚举属性映射生成检查约束。</span><span class="sxs-lookup"><span data-stu-id="6ba72-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="6ba72-392">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="6ba72-393">文档可通过问题 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="6ba72-394">IsRelational</span><span class="sxs-lookup"><span data-stu-id="6ba72-394">IsRelational</span></span>

<span data-ttu-id="6ba72-395">除了现有 `IsSqlServer`、`IsSqlite` 和 `IsInMemory` 外，还添加了新的 `IsRelational` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ba72-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="6ba72-396">此方法可用于测试 DbContext 是否使用任何关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="6ba72-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="6ba72-397">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="6ba72-398">文档可通过问题 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="6ba72-399">使用 ETag 的 Cosmos 开放式并发</span><span class="sxs-lookup"><span data-stu-id="6ba72-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="6ba72-400">Azure Cosmos DB 数据库提供程序现在支持使用 ETag 的开放式并发。</span><span class="sxs-lookup"><span data-stu-id="6ba72-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="6ba72-401">使用 OnModelCreating 中的模型生成器配置 ETag：</span><span class="sxs-lookup"><span data-stu-id="6ba72-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="6ba72-402">然后，SaveChanges 将在并发冲突上引发 `DbUpdateConcurrencyException`，[可以处理](https://docs.microsoft.com/ef/core/saving/concurrency)它来实现重试等。</span><span class="sxs-lookup"><span data-stu-id="6ba72-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="6ba72-403">文档可通过问题 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="6ba72-404">查询转换以获取更多 DateTime 构造</span><span class="sxs-lookup"><span data-stu-id="6ba72-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="6ba72-405">现在，包含新 DataTime 构造的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="6ba72-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="6ba72-406">此外，现在映射了以下 SQL Server 函数：</span><span class="sxs-lookup"><span data-stu-id="6ba72-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="6ba72-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="6ba72-407">DateDiffWeek</span></span>
* <span data-ttu-id="6ba72-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="6ba72-408">DateFromParts</span></span>

<span data-ttu-id="6ba72-409">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="6ba72-410">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="6ba72-411">查询转换以获取更多字节数组构造</span><span class="sxs-lookup"><span data-stu-id="6ba72-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="6ba72-412">现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。</span><span class="sxs-lookup"><span data-stu-id="6ba72-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="6ba72-413">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="6ba72-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="6ba72-414">更多文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="6ba72-415">反向查询转换</span><span class="sxs-lookup"><span data-stu-id="6ba72-415">Query translation for Reverse</span></span>

<span data-ttu-id="6ba72-416">现在，使用 `Reverse` 的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="6ba72-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="6ba72-417">例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="6ba72-418">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="6ba72-419">按位运算符的查询转换</span><span class="sxs-lookup"><span data-stu-id="6ba72-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="6ba72-420">现在，使用按位运算符的查询会在更多的情况下被转换，例如：</span><span class="sxs-lookup"><span data-stu-id="6ba72-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="6ba72-421">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="6ba72-422">Cosmos 上的字符串的查询转换</span><span class="sxs-lookup"><span data-stu-id="6ba72-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="6ba72-423">现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。</span><span class="sxs-lookup"><span data-stu-id="6ba72-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="6ba72-424">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="6ba72-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
