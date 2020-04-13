---
title: EF Core 5.0 中的新增功能
description: EF Core 5.0 中的新功能概述
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c047a308cadf44eea577dcab29b68b36942a50df
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634277"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="58cb6-103">EF Core 5.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="58cb6-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="58cb6-104">EF Core 5.0 目前正在开发中。</span><span class="sxs-lookup"><span data-stu-id="58cb6-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="58cb6-105">此页面将包含每个预览版中引入的令人关注的更改的简要介绍。</span><span class="sxs-lookup"><span data-stu-id="58cb6-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="58cb6-106">此页面不会复制 [EF Core 5.0](plan.md) 的计划。</span><span class="sxs-lookup"><span data-stu-id="58cb6-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="58cb6-107">计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。</span><span class="sxs-lookup"><span data-stu-id="58cb6-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="58cb6-108">发布时，我们会将此处的链接添加到官方文档。</span><span class="sxs-lookup"><span data-stu-id="58cb6-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-2"></a><span data-ttu-id="58cb6-109">预览版 2</span><span class="sxs-lookup"><span data-stu-id="58cb6-109">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="58cb6-110">使用 C# 特性指定属性支持字段</span><span class="sxs-lookup"><span data-stu-id="58cb6-110">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="58cb6-111">现在，可以使用 C# 特性指定属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="58cb6-111">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="58cb6-112">使用此特性，即使在不能自动找到支持字段时，EF Core 也能正常读写支持字段。</span><span class="sxs-lookup"><span data-stu-id="58cb6-112">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="58cb6-113">例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-113">For example:</span></span>

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

<span data-ttu-id="58cb6-114">文档可通过问题 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-114">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="58cb6-115">完成鉴别器映射</span><span class="sxs-lookup"><span data-stu-id="58cb6-115">Complete discriminator mapping</span></span>

<span data-ttu-id="58cb6-116">EF Core 使用鉴别器列进行 [继承层次结构的 TPH 映射](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="58cb6-116">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="58cb6-117">只要 EF Core 知道该鉴别器的所有可能的值，就可能实现某些性能改进。</span><span class="sxs-lookup"><span data-stu-id="58cb6-117">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="58cb6-118">EF Core 5.0 现在可实现这些改进。</span><span class="sxs-lookup"><span data-stu-id="58cb6-118">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="58cb6-119">例如，对于返回层次结构中所有类型的查询，旧版 EF Core 总是会生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="58cb6-119">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="58cb6-120">配置完整的鉴别器映射后，EF Core 5.0 现在将生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="58cb6-120">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="58cb6-121">从预览版 3 开始，这将成为默认行为。</span><span class="sxs-lookup"><span data-stu-id="58cb6-121">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="58cb6-122">Microsoft.Data.Sqlite 中的性能改进</span><span class="sxs-lookup"><span data-stu-id="58cb6-122">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="58cb6-123">我们对 SQLIte 进行了两项性能改进：</span><span class="sxs-lookup"><span data-stu-id="58cb6-123">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="58cb6-124">现在，利用 SqliteBlob 和流，可以更高效地使用 GetBytes、GetChars 和 GetTextReader 检索二进制和字符串数据。</span><span class="sxs-lookup"><span data-stu-id="58cb6-124">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="58cb6-125">SqliteConnection 的初始化现在很慢。</span><span class="sxs-lookup"><span data-stu-id="58cb6-125">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="58cb6-126">这些改进已实施到 ADO.NET Microsoft.Data.Sqlite 提供程序中，因此还可以在 EF Core 之外提升性能。</span><span class="sxs-lookup"><span data-stu-id="58cb6-126">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="58cb6-127">预览版 1</span><span class="sxs-lookup"><span data-stu-id="58cb6-127">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="58cb6-128">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="58cb6-128">Simple logging</span></span>

<span data-ttu-id="58cb6-129">此特性会添加类似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="58cb6-129">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="58cb6-130">也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。</span><span class="sxs-lookup"><span data-stu-id="58cb6-130">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="58cb6-131">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="58cb6-131">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="58cb6-132">其他文档可通过问题 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-132">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="58cb6-133">获取生成的 SQL 的简单方法</span><span class="sxs-lookup"><span data-stu-id="58cb6-133">Simple way to get generated SQL</span></span>

<span data-ttu-id="58cb6-134">EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="58cb6-134">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="58cb6-135">初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。</span><span class="sxs-lookup"><span data-stu-id="58cb6-135">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="58cb6-136">其他文档可通过问题 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-136">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="58cb6-137">使用 C# 特性指示实体没有键</span><span class="sxs-lookup"><span data-stu-id="58cb6-137">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="58cb6-138">现在可以将实体类型配置为不使用新 `KeylessAttribute`的键。</span><span class="sxs-lookup"><span data-stu-id="58cb6-138">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="58cb6-139">例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-139">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="58cb6-140">文档可通过问题 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-140">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="58cb6-141">可在初始化的 DbContext 上更改连接或连接字符串</span><span class="sxs-lookup"><span data-stu-id="58cb6-141">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="58cb6-142">现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="58cb6-142">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="58cb6-143">而且，现在可以在上下文实例上更改连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="58cb6-143">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="58cb6-144">此功能使得同一个上下文实例能够动态地连接到不同的数据库。</span><span class="sxs-lookup"><span data-stu-id="58cb6-144">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="58cb6-145">文档可通过问题 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-145">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="58cb6-146">更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="58cb6-146">Change-tracking proxies</span></span>

<span data-ttu-id="58cb6-147">EF Core 现在可以生成自动实现 [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) 的运行时代理。</span><span class="sxs-lookup"><span data-stu-id="58cb6-147">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="58cb6-148">这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。</span><span class="sxs-lookup"><span data-stu-id="58cb6-148">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="58cb6-149">不过，代理有其自身的一组限制，因此并不适合所有人使用。</span><span class="sxs-lookup"><span data-stu-id="58cb6-149">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="58cb6-150">文档可通过问题 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-150">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="58cb6-151">增强的调试视图</span><span class="sxs-lookup"><span data-stu-id="58cb6-151">Enhanced debug views</span></span>

<span data-ttu-id="58cb6-152">调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。</span><span class="sxs-lookup"><span data-stu-id="58cb6-152">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="58cb6-153">在一段时间之前，我们就已经实现了模型的调试视图。</span><span class="sxs-lookup"><span data-stu-id="58cb6-153">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="58cb6-154">对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。</span><span class="sxs-lookup"><span data-stu-id="58cb6-154">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="58cb6-155">初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。</span><span class="sxs-lookup"><span data-stu-id="58cb6-155">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="58cb6-156">其他文档可通过问题 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-156">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="58cb6-157">改进了对数据库 null 语义的处理</span><span class="sxs-lookup"><span data-stu-id="58cb6-157">Improved handling of database null semantics</span></span>

<span data-ttu-id="58cb6-158">关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="58cb6-158">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="58cb6-159">而 C# 将 null 视为与其他任何 null 相比均相等的定义值。</span><span class="sxs-lookup"><span data-stu-id="58cb6-159">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="58cb6-160">默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。</span><span class="sxs-lookup"><span data-stu-id="58cb6-160">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="58cb6-161">EF Core 5.0 极大地提升了这些转换操作的效率。</span><span class="sxs-lookup"><span data-stu-id="58cb6-161">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="58cb6-162">文档可通过问题 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-162">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="58cb6-163">索引器属性</span><span class="sxs-lookup"><span data-stu-id="58cb6-163">Indexer properties</span></span>

<span data-ttu-id="58cb6-164">EF Core 5.0 支持 C# 索引器属性的映射。</span><span class="sxs-lookup"><span data-stu-id="58cb6-164">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="58cb6-165">这写属性使得实体能够充当属性包，实体中的列被映射为包中的命名属性。</span><span class="sxs-lookup"><span data-stu-id="58cb6-165">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="58cb6-166">文档可通过问题 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-166">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="58cb6-167">为枚举映射生成检查约束</span><span class="sxs-lookup"><span data-stu-id="58cb6-167">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="58cb6-168">EF Core 5.0 迁移现可为枚举属性映射生成检查约束。</span><span class="sxs-lookup"><span data-stu-id="58cb6-168">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="58cb6-169">例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-169">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="58cb6-170">文档可通过问题 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-170">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="58cb6-171">IsRelational</span><span class="sxs-lookup"><span data-stu-id="58cb6-171">IsRelational</span></span>

<span data-ttu-id="58cb6-172">除了现有 `IsSqlServer`、`IsSqlite` 和 `IsInMemory` 外，还添加了新的 `IsRelational` 方法。</span><span class="sxs-lookup"><span data-stu-id="58cb6-172">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="58cb6-173">此方法可用于测试 DbContext 是否使用任何关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="58cb6-173">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="58cb6-174">例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-174">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="58cb6-175">文档可通过问题 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-175">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="58cb6-176">使用 ETag 的 Cosmos 开放式并发</span><span class="sxs-lookup"><span data-stu-id="58cb6-176">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="58cb6-177">Azure Cosmos DB 数据库提供程序现在支持使用 ETag 的开放式并发。</span><span class="sxs-lookup"><span data-stu-id="58cb6-177">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="58cb6-178">使用 OnModelCreating 中的模型生成器配置 ETag：</span><span class="sxs-lookup"><span data-stu-id="58cb6-178">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="58cb6-179">然后，SaveChanges 将在并发冲突上引发 `DbUpdateConcurrencyException`，[可以处理](https://docs.microsoft.com/ef/core/saving/concurrency)它来实现重试等。</span><span class="sxs-lookup"><span data-stu-id="58cb6-179">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="58cb6-180">文档可通过问题 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-180">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="58cb6-181">查询转换以获取更多 DateTime 构造</span><span class="sxs-lookup"><span data-stu-id="58cb6-181">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="58cb6-182">现在，包含新 DataTime 构造的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="58cb6-182">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="58cb6-183">此外，现在映射了以下 SQL Server 函数：</span><span class="sxs-lookup"><span data-stu-id="58cb6-183">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="58cb6-184">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="58cb6-184">DateDiffWeek</span></span>
* <span data-ttu-id="58cb6-185">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="58cb6-185">DateFromParts</span></span>

<span data-ttu-id="58cb6-186">例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-186">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="58cb6-187">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-187">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="58cb6-188">查询转换以获取更多字节数组构造</span><span class="sxs-lookup"><span data-stu-id="58cb6-188">Query translations for more byte array constructs</span></span>

<span data-ttu-id="58cb6-189">现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。</span><span class="sxs-lookup"><span data-stu-id="58cb6-189">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="58cb6-190">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="58cb6-190">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="58cb6-191">更多文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-191">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="58cb6-192">反向查询转换</span><span class="sxs-lookup"><span data-stu-id="58cb6-192">Query translation for Reverse</span></span>

<span data-ttu-id="58cb6-193">现在，使用 `Reverse` 的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="58cb6-193">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="58cb6-194">例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-194">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="58cb6-195">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-195">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="58cb6-196">按位运算符的查询转换</span><span class="sxs-lookup"><span data-stu-id="58cb6-196">Query translation for bitwise operators</span></span>

<span data-ttu-id="58cb6-197">现在，使用按位运算符的查询会在更多的情况下被转换，例如：</span><span class="sxs-lookup"><span data-stu-id="58cb6-197">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="58cb6-198">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-198">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="58cb6-199">Cosmos 上的字符串的查询转换</span><span class="sxs-lookup"><span data-stu-id="58cb6-199">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="58cb6-200">现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。</span><span class="sxs-lookup"><span data-stu-id="58cb6-200">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="58cb6-201">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="58cb6-201">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
