---
title: EF Core 5.0 中的新增功能
description: EF Core 5.0 中的新功能概述
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418932"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="f433f-103">EF Core 5.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="f433f-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="f433f-104">EF Core 5.0 目前正在开发中。</span><span class="sxs-lookup"><span data-stu-id="f433f-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="f433f-105">此页面将包含每个预览版中引入的令人关注的更改的简要介绍。</span><span class="sxs-lookup"><span data-stu-id="f433f-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="f433f-106">此页面不会复制 [EF Core 5.0](plan.md) 的计划。</span><span class="sxs-lookup"><span data-stu-id="f433f-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="f433f-107">计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。</span><span class="sxs-lookup"><span data-stu-id="f433f-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="f433f-108">发布时，我们会将此处的链接添加到官方文档。</span><span class="sxs-lookup"><span data-stu-id="f433f-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="f433f-109">预览版 5</span><span class="sxs-lookup"><span data-stu-id="f433f-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="f433f-110">数据库排序规则</span><span class="sxs-lookup"><span data-stu-id="f433f-110">Database collations</span></span>

<span data-ttu-id="f433f-111">现可在 EF 模式中指定数据库的默认排序规则。</span><span class="sxs-lookup"><span data-stu-id="f433f-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="f433f-112">这将传输到生成的迁移，在创建数据库时设置排序规则。</span><span class="sxs-lookup"><span data-stu-id="f433f-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="f433f-113">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="f433f-114">然后，迁移会生成以下内容，在 SQL Server 上创建数据库：</span><span class="sxs-lookup"><span data-stu-id="f433f-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="f433f-115">可指定用于特定数据库列的排序规则。</span><span class="sxs-lookup"><span data-stu-id="f433f-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="f433f-116">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="f433f-117">对于不使用迁移的列，可在创建 DbContext 基架时从数据库对排序规则进行反向工程处理。</span><span class="sxs-lookup"><span data-stu-id="f433f-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="f433f-118">最后，可通过 `EF.Functions.Collate()` 使用不同的排序规则进行即席查询。</span><span class="sxs-lookup"><span data-stu-id="f433f-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="f433f-119">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="f433f-120">这将为 SQL Server 生成以下查询：</span><span class="sxs-lookup"><span data-stu-id="f433f-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="f433f-121">请注意，应谨慎使用即席排序规则，因为它们可能会对数据库性能造成不良影响。</span><span class="sxs-lookup"><span data-stu-id="f433f-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="f433f-122">文档可通过问题 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="f433f-123">将参数传输到 IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="f433f-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="f433f-124">参数现从命令行传输到 [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1) 的 `CreateDbContext` 方法。</span><span class="sxs-lookup"><span data-stu-id="f433f-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="f433f-125">例如，若要指示这是一个开发生成，可在命令行上传递自定义参数（例如 `dev`）：</span><span class="sxs-lookup"><span data-stu-id="f433f-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="f433f-126">然后，该参数将传输到工厂，它在这里可用于控制如何创建和初始化上下文。</span><span class="sxs-lookup"><span data-stu-id="f433f-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="f433f-127">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="f433f-128">文档可通过问题 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="f433f-129">具有标识解析的非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="f433f-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="f433f-130">现可将非跟踪查询配置来执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="f433f-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="f433f-131">例如，以下查询将为每个 Post 创建一个新的 Blog 实例，即使每个 Blog 的主键相同也是如此。</span><span class="sxs-lookup"><span data-stu-id="f433f-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="f433f-132">然而，可更改此查询来确保只创建一个 Blog 实例，代价通常是稍微拖慢一些速度和总是使用更多的内存：</span><span class="sxs-lookup"><span data-stu-id="f433f-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="f433f-133">请注意，这仅适用于非跟踪查询，因为所有跟踪查询已显示此行为。</span><span class="sxs-lookup"><span data-stu-id="f433f-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="f433f-134">此外，在 API 评审后，将更改 `PerformIdentityResolution` 语法。</span><span class="sxs-lookup"><span data-stu-id="f433f-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="f433f-135">请查看 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="f433f-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="f433f-136">文档可通过问题 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="f433f-137">存储的（持久化）计算列</span><span class="sxs-lookup"><span data-stu-id="f433f-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="f433f-138">大多数数据库都允许在计算后存储计算得到的列值。</span><span class="sxs-lookup"><span data-stu-id="f433f-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="f433f-139">虽然这会占用磁盘空间，但仅在更新时对计算的列计算一次，而不是在每次检索它的值时都计算。</span><span class="sxs-lookup"><span data-stu-id="f433f-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="f433f-140">这样也可对某些数据库编制列的索引。</span><span class="sxs-lookup"><span data-stu-id="f433f-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="f433f-141">EF Core 5.0 允许将计算列配置为存储计算列。</span><span class="sxs-lookup"><span data-stu-id="f433f-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="f433f-142">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="f433f-143">SQLite 计算列</span><span class="sxs-lookup"><span data-stu-id="f433f-143">SQLite computed columns</span></span>

<span data-ttu-id="f433f-144">EF Core 现支持在 SQLite 数据库中使用计算列。</span><span class="sxs-lookup"><span data-stu-id="f433f-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="f433f-145">预览版 4</span><span class="sxs-lookup"><span data-stu-id="f433f-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="f433f-146">在模型中配置数据库精度/小数位数</span><span class="sxs-lookup"><span data-stu-id="f433f-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="f433f-147">现在，可以使用模型生成器指定属性的精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="f433f-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="f433f-148">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="f433f-149">还可以通过完整的数据库类型（如“decimal(16,4)”）设置精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="f433f-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="f433f-150">文档可通过问题 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="f433f-151">指定 SQL Server 索引填充因子</span><span class="sxs-lookup"><span data-stu-id="f433f-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="f433f-152">现在可以在 SQL Server 上创建索引时指定填充因子。</span><span class="sxs-lookup"><span data-stu-id="f433f-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="f433f-153">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="f433f-154">预览版 3</span><span class="sxs-lookup"><span data-stu-id="f433f-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="f433f-155">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="f433f-155">Filtered Include</span></span>

<span data-ttu-id="f433f-156">Include 方法现在支持筛选包含的实体。</span><span class="sxs-lookup"><span data-stu-id="f433f-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="f433f-157">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="f433f-158">此查询将一并返回包含每个关联文章的博客（仅当文章标题包含“Cheese”时）。</span><span class="sxs-lookup"><span data-stu-id="f433f-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="f433f-159">Skip 和 Take 也可用于减少包含的实体数量。</span><span class="sxs-lookup"><span data-stu-id="f433f-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="f433f-160">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="f433f-161">此查询将返回博客，每个博客最多包含 5 篇文章。</span><span class="sxs-lookup"><span data-stu-id="f433f-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="f433f-162">有关完整详细信息，请参阅 [Include 文档](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="f433f-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="f433f-163">用于导航属性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="f433f-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="f433f-164">导航属性主要在[定义关系](xref:core/modeling/relationships)时配置。</span><span class="sxs-lookup"><span data-stu-id="f433f-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="f433f-165">但是，在导航属性需要额外配置的情况下，可以使用新的 `Navigation` 方法。</span><span class="sxs-lookup"><span data-stu-id="f433f-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="f433f-166">例如，如需在根据约定找不到支持字段时设置导航的支持字段，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f433f-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="f433f-167">请注意：`Navigation` API 不会替换关系配置。</span><span class="sxs-lookup"><span data-stu-id="f433f-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="f433f-168">但是，它允许在已发现或定义的关系中进行其他导航属性配置。</span><span class="sxs-lookup"><span data-stu-id="f433f-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="f433f-169">请参阅[配置导航属性文档](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="f433f-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="f433f-170">用于命名空间和连接字符串的新命令行参数</span><span class="sxs-lookup"><span data-stu-id="f433f-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="f433f-171">迁移和基架现在允许在命令行上指定命名空间。</span><span class="sxs-lookup"><span data-stu-id="f433f-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="f433f-172">例如，如需对数据库进行反向工程，将上下文和模型类放在不同的命名空间中：</span><span class="sxs-lookup"><span data-stu-id="f433f-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="f433f-173">有关完整详细信息，请参阅[迁移](xref:core/managing-schemas/migrations/index#namespaces)和[反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)文档。</span><span class="sxs-lookup"><span data-stu-id="f433f-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="f433f-174">此外，连接字符串现在可以传递到 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="f433f-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="f433f-175">有关完整的详细信息，请参阅[工具文档](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="f433f-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="f433f-176">等效参数已添加到 VS 程序包管理器控制台中使用的 PowerShell 命令中。</span><span class="sxs-lookup"><span data-stu-id="f433f-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="f433f-177">EnableDetailedErrors 已返回</span><span class="sxs-lookup"><span data-stu-id="f433f-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="f433f-178">出于性能原因，在从数据库读取值时，EF 不会执行额外的 null 检查。</span><span class="sxs-lookup"><span data-stu-id="f433f-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="f433f-179">当遇到意外的 null 时，这可能会导致难以查找根本原因的异常。</span><span class="sxs-lookup"><span data-stu-id="f433f-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="f433f-180">使用 `EnableDetailedErrors` 会将额外的 null 检查添加到查询中，这样一来，对于较小的性能开销，这些错误就更容易追溯到根本原因。</span><span class="sxs-lookup"><span data-stu-id="f433f-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="f433f-181">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="f433f-182">文档可通过问题 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="f433f-183">Cosmos 分区键</span><span class="sxs-lookup"><span data-stu-id="f433f-183">Cosmos partition keys</span></span>

<span data-ttu-id="f433f-184">现在可以在查询中指定用于给定查询的分区键。</span><span class="sxs-lookup"><span data-stu-id="f433f-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="f433f-185">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="f433f-186">文档可通过问题 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="f433f-187">支持 SQL Server DATALENGTH 函数</span><span class="sxs-lookup"><span data-stu-id="f433f-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="f433f-188">可以使用新的 `EF.Functions.DataLength` 方法访问它。</span><span class="sxs-lookup"><span data-stu-id="f433f-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="f433f-189">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="f433f-190">预览版 2</span><span class="sxs-lookup"><span data-stu-id="f433f-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="f433f-191">使用 C# 特性指定属性支持字段</span><span class="sxs-lookup"><span data-stu-id="f433f-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="f433f-192">现在，可以使用 C# 特性指定属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="f433f-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="f433f-193">使用此特性，即使在不能自动找到支持字段时，EF Core 也能正常读写支持字段。</span><span class="sxs-lookup"><span data-stu-id="f433f-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="f433f-194">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-194">For example:</span></span>

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

<span data-ttu-id="f433f-195">文档可通过问题 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="f433f-196">完成鉴别器映射</span><span class="sxs-lookup"><span data-stu-id="f433f-196">Complete discriminator mapping</span></span>

<span data-ttu-id="f433f-197">EF Core 使用鉴别器列进行 [继承层次结构的 TPH 映射](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="f433f-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="f433f-198">只要 EF Core 知道该鉴别器的所有可能的值，就可能实现某些性能改进。</span><span class="sxs-lookup"><span data-stu-id="f433f-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="f433f-199">EF Core 5.0 现在可实现这些改进。</span><span class="sxs-lookup"><span data-stu-id="f433f-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="f433f-200">例如，对于返回层次结构中所有类型的查询，旧版 EF Core 总是会生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="f433f-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="f433f-201">配置完整的鉴别器映射后，EF Core 5.0 现在将生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="f433f-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="f433f-202">从预览版 3 开始，这将成为默认行为。</span><span class="sxs-lookup"><span data-stu-id="f433f-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="f433f-203">Microsoft.Data.Sqlite 中的性能改进</span><span class="sxs-lookup"><span data-stu-id="f433f-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="f433f-204">我们对 SQLIte 进行了两项性能改进：</span><span class="sxs-lookup"><span data-stu-id="f433f-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="f433f-205">现在，利用 SqliteBlob 和流，可以更高效地使用 GetBytes、GetChars 和 GetTextReader 检索二进制和字符串数据。</span><span class="sxs-lookup"><span data-stu-id="f433f-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="f433f-206">SqliteConnection 的初始化现在很慢。</span><span class="sxs-lookup"><span data-stu-id="f433f-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="f433f-207">这些改进已实施到 ADO.NET Microsoft.Data.Sqlite 提供程序中，因此还可以在 EF Core 之外提升性能。</span><span class="sxs-lookup"><span data-stu-id="f433f-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="f433f-208">预览版 1</span><span class="sxs-lookup"><span data-stu-id="f433f-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="f433f-209">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="f433f-209">Simple logging</span></span>

<span data-ttu-id="f433f-210">此特性会添加类似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="f433f-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="f433f-211">也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。</span><span class="sxs-lookup"><span data-stu-id="f433f-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="f433f-212">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="f433f-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="f433f-213">其他文档可通过问题 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="f433f-214">获取生成的 SQL 的简单方法</span><span class="sxs-lookup"><span data-stu-id="f433f-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="f433f-215">EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="f433f-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="f433f-216">初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。</span><span class="sxs-lookup"><span data-stu-id="f433f-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="f433f-217">其他文档可通过问题 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="f433f-218">使用 C# 特性指示实体没有键</span><span class="sxs-lookup"><span data-stu-id="f433f-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="f433f-219">现在可以将实体类型配置为不使用新 `KeylessAttribute`的键。</span><span class="sxs-lookup"><span data-stu-id="f433f-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="f433f-220">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="f433f-221">文档可通过问题 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="f433f-222">可在初始化的 DbContext 上更改连接或连接字符串</span><span class="sxs-lookup"><span data-stu-id="f433f-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="f433f-223">现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="f433f-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="f433f-224">而且，现在可以在上下文实例上更改连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="f433f-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="f433f-225">此功能使得同一个上下文实例能够动态地连接到不同的数据库。</span><span class="sxs-lookup"><span data-stu-id="f433f-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="f433f-226">文档可通过问题 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="f433f-227">更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="f433f-227">Change-tracking proxies</span></span>

<span data-ttu-id="f433f-228">EF Core 现在可以生成自动实现 [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) 的运行时代理。</span><span class="sxs-lookup"><span data-stu-id="f433f-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="f433f-229">这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。</span><span class="sxs-lookup"><span data-stu-id="f433f-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="f433f-230">不过，代理有其自身的一组限制，因此并不适合所有人使用。</span><span class="sxs-lookup"><span data-stu-id="f433f-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="f433f-231">文档可通过问题 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="f433f-232">增强的调试视图</span><span class="sxs-lookup"><span data-stu-id="f433f-232">Enhanced debug views</span></span>

<span data-ttu-id="f433f-233">调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。</span><span class="sxs-lookup"><span data-stu-id="f433f-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="f433f-234">在一段时间之前，我们就已经实现了模型的调试视图。</span><span class="sxs-lookup"><span data-stu-id="f433f-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="f433f-235">对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。</span><span class="sxs-lookup"><span data-stu-id="f433f-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="f433f-236">初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。</span><span class="sxs-lookup"><span data-stu-id="f433f-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="f433f-237">其他文档可通过问题 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="f433f-238">改进了对数据库 null 语义的处理</span><span class="sxs-lookup"><span data-stu-id="f433f-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="f433f-239">关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="f433f-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="f433f-240">而 C# 将 null 视为与其他任何 null 相比均相等的定义值。</span><span class="sxs-lookup"><span data-stu-id="f433f-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="f433f-241">默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。</span><span class="sxs-lookup"><span data-stu-id="f433f-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="f433f-242">EF Core 5.0 极大地提升了这些转换操作的效率。</span><span class="sxs-lookup"><span data-stu-id="f433f-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="f433f-243">文档可通过问题 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="f433f-244">索引器属性</span><span class="sxs-lookup"><span data-stu-id="f433f-244">Indexer properties</span></span>

<span data-ttu-id="f433f-245">EF Core 5.0 支持 C# 索引器属性的映射。</span><span class="sxs-lookup"><span data-stu-id="f433f-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="f433f-246">这写属性使得实体能够充当属性包，实体中的列被映射为包中的命名属性。</span><span class="sxs-lookup"><span data-stu-id="f433f-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="f433f-247">文档可通过问题 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="f433f-248">为枚举映射生成检查约束</span><span class="sxs-lookup"><span data-stu-id="f433f-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="f433f-249">EF Core 5.0 迁移现可为枚举属性映射生成检查约束。</span><span class="sxs-lookup"><span data-stu-id="f433f-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="f433f-250">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="f433f-251">文档可通过问题 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="f433f-252">IsRelational</span><span class="sxs-lookup"><span data-stu-id="f433f-252">IsRelational</span></span>

<span data-ttu-id="f433f-253">除了现有 `IsSqlServer`、`IsSqlite` 和 `IsInMemory` 外，还添加了新的 `IsRelational` 方法。</span><span class="sxs-lookup"><span data-stu-id="f433f-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="f433f-254">此方法可用于测试 DbContext 是否使用任何关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="f433f-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="f433f-255">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="f433f-256">文档可通过问题 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="f433f-257">使用 ETag 的 Cosmos 开放式并发</span><span class="sxs-lookup"><span data-stu-id="f433f-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="f433f-258">Azure Cosmos DB 数据库提供程序现在支持使用 ETag 的开放式并发。</span><span class="sxs-lookup"><span data-stu-id="f433f-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="f433f-259">使用 OnModelCreating 中的模型生成器配置 ETag：</span><span class="sxs-lookup"><span data-stu-id="f433f-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="f433f-260">然后，SaveChanges 将在并发冲突上引发 `DbUpdateConcurrencyException`，[可以处理](https://docs.microsoft.com/ef/core/saving/concurrency)它来实现重试等。</span><span class="sxs-lookup"><span data-stu-id="f433f-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="f433f-261">文档可通过问题 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="f433f-262">查询转换以获取更多 DateTime 构造</span><span class="sxs-lookup"><span data-stu-id="f433f-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="f433f-263">现在，包含新 DataTime 构造的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="f433f-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="f433f-264">此外，现在映射了以下 SQL Server 函数：</span><span class="sxs-lookup"><span data-stu-id="f433f-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="f433f-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="f433f-265">DateDiffWeek</span></span>
* <span data-ttu-id="f433f-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="f433f-266">DateFromParts</span></span>

<span data-ttu-id="f433f-267">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="f433f-268">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="f433f-269">查询转换以获取更多字节数组构造</span><span class="sxs-lookup"><span data-stu-id="f433f-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="f433f-270">现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。</span><span class="sxs-lookup"><span data-stu-id="f433f-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="f433f-271">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="f433f-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="f433f-272">更多文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="f433f-273">反向查询转换</span><span class="sxs-lookup"><span data-stu-id="f433f-273">Query translation for Reverse</span></span>

<span data-ttu-id="f433f-274">现在，使用 `Reverse` 的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="f433f-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="f433f-275">例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="f433f-276">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="f433f-277">按位运算符的查询转换</span><span class="sxs-lookup"><span data-stu-id="f433f-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="f433f-278">现在，使用按位运算符的查询会在更多的情况下被转换，例如：</span><span class="sxs-lookup"><span data-stu-id="f433f-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="f433f-279">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="f433f-280">Cosmos 上的字符串的查询转换</span><span class="sxs-lookup"><span data-stu-id="f433f-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="f433f-281">现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。</span><span class="sxs-lookup"><span data-stu-id="f433f-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="f433f-282">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="f433f-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
