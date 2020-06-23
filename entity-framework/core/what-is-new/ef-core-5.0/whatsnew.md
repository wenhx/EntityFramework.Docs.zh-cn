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
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 中的新增功能

EF Core 5.0 目前正在开发中。
此页面将包含每个预览版中引入的令人关注的更改的简要介绍。

此页面不会复制 [EF Core 5.0](plan.md) 的计划。
计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。

发布时，我们会将此处的链接添加到官方文档。

## <a name="preview-5"></a>预览版 5

### <a name="database-collations"></a>数据库排序规则

现可在 EF 模式中指定数据库的默认排序规则。
这将传输到生成的迁移，在创建数据库时设置排序规则。
例如：

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

然后，迁移会生成以下内容，在 SQL Server 上创建数据库：

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

可指定用于特定数据库列的排序规则。
例如：

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

对于不使用迁移的列，可在创建 DbContext 基架时从数据库对排序规则进行反向工程处理。

最后，可通过 `EF.Functions.Collate()` 使用不同的排序规则进行即席查询。
例如：

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

这将为 SQL Server 生成以下查询：

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

请注意，应谨慎使用即席排序规则，因为它们可能会对数据库性能造成不良影响。

文档可通过问题 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) 进行跟踪。

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>将参数传输到 IDesignTimeDbContextFactory

参数现从命令行传输到 [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1) 的 `CreateDbContext` 方法。 例如，若要指示这是一个开发生成，可在命令行上传递自定义参数（例如 `dev`）：

```
dotnet ef migrations add two --verbose --dev
``` 

然后，该参数将传输到工厂，它在这里可用于控制如何创建和初始化上下文。
例如：

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

文档可通过问题 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) 进行跟踪。

### <a name="no-tracking-queries-with-identity-resolution"></a>具有标识解析的非跟踪查询

现可将非跟踪查询配置来执行标识解析。
例如，以下查询将为每个 Post 创建一个新的 Blog 实例，即使每个 Blog 的主键相同也是如此。 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

然而，可更改此查询来确保只创建一个 Blog 实例，代价通常是稍微拖慢一些速度和总是使用更多的内存：

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

请注意，这仅适用于非跟踪查询，因为所有跟踪查询已显示此行为。 此外，在 API 评审后，将更改 `PerformIdentityResolution` 语法。
请查看 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。

文档可通过问题 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) 进行跟踪。

### <a name="stored-persisted-computed-columns"></a>存储的（持久化）计算列

大多数数据库都允许在计算后存储计算得到的列值。
虽然这会占用磁盘空间，但仅在更新时对计算的列计算一次，而不是在每次检索它的值时都计算。
这样也可对某些数据库编制列的索引。

EF Core 5.0 允许将计算列配置为存储计算列。
例如：
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>SQLite 计算列

EF Core 现支持在 SQLite 数据库中使用计算列。

## <a name="preview-4"></a>预览版 4

### <a name="configure-database-precisionscale-in-model"></a>在模型中配置数据库精度/小数位数

现在，可以使用模型生成器指定属性的精度和小数位数。
例如：

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

还可以通过完整的数据库类型（如“decimal(16,4)”）设置精度和小数位数。 

文档可通过问题 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) 进行跟踪。

### <a name="specify-sql-server-index-fill-factor"></a>指定 SQL Server 索引填充因子

现在可以在 SQL Server 上创建索引时指定填充因子。
例如：

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>预览版 3

### <a name="filtered-include"></a>经过筛选的包含

Include 方法现在支持筛选包含的实体。
例如：

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

此查询将一并返回包含每个关联文章的博客（仅当文章标题包含“Cheese”时）。

Skip 和 Take 也可用于减少包含的实体数量。
例如：
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
此查询将返回博客，每个博客最多包含 5 篇文章。

有关完整详细信息，请参阅 [Include 文档](xref:core/querying/related-data#filtered-include)。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>用于导航属性的新 ModelBuilder API

导航属性主要在[定义关系](xref:core/modeling/relationships)时配置。
但是，在导航属性需要额外配置的情况下，可以使用新的 `Navigation` 方法。
例如，如需在根据约定找不到支持字段时设置导航的支持字段，请执行以下操作：

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

请注意：`Navigation` API 不会替换关系配置。
但是，它允许在已发现或定义的关系中进行其他导航属性配置。

请参阅[配置导航属性文档](xref:core/modeling/relationships#configuring-navigation-properties)。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>用于命名空间和连接字符串的新命令行参数 

迁移和基架现在允许在命令行上指定命名空间。
例如，如需对数据库进行反向工程，将上下文和模型类放在不同的命名空间中： 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

有关完整详细信息，请参阅[迁移](xref:core/managing-schemas/migrations/index#namespaces)和[反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)文档。

---
此外，连接字符串现在可以传递到 `database-update` 命令：

```
dotnet ef database update --connection "connection string"
```

有关完整的详细信息，请参阅[工具文档](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。

等效参数已添加到 VS 程序包管理器控制台中使用的 PowerShell 命令中。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors 已返回

出于性能原因，在从数据库读取值时，EF 不会执行额外的 null 检查。
当遇到意外的 null 时，这可能会导致难以查找根本原因的异常。

使用 `EnableDetailedErrors` 会将额外的 null 检查添加到查询中，这样一来，对于较小的性能开销，这些错误就更容易追溯到根本原因。  

例如：
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

文档可通过问题 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) 进行跟踪。

### <a name="cosmos-partition-keys"></a>Cosmos 分区键

现在可以在查询中指定用于给定查询的分区键。
例如：

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

文档可通过问题 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) 进行跟踪。

### <a name="support-for-the-sql-server-datalength-function"></a>支持 SQL Server DATALENGTH 函数

可以使用新的 `EF.Functions.DataLength` 方法访问它。
例如：
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>预览版 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>使用 C# 特性指定属性支持字段

现在，可以使用 C# 特性指定属性的支持字段。
使用此特性，即使在不能自动找到支持字段时，EF Core 也能正常读写支持字段。
例如：

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

文档可通过问题 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) 进行跟踪。

### <a name="complete-discriminator-mapping"></a>完成鉴别器映射

EF Core 使用鉴别器列进行 [继承层次结构的 TPH 映射](/ef/core/modeling/inheritance)。
只要 EF Core 知道该鉴别器的所有可能的值，就可能实现某些性能改进。
EF Core 5.0 现在可实现这些改进。

例如，对于返回层次结构中所有类型的查询，旧版 EF Core 总是会生成以下 SQL：

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

配置完整的鉴别器映射后，EF Core 5.0 现在将生成以下内容：

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

从预览版 3 开始，这将成为默认行为。

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Microsoft.Data.Sqlite 中的性能改进

我们对 SQLIte 进行了两项性能改进：

* 现在，利用 SqliteBlob 和流，可以更高效地使用 GetBytes、GetChars 和 GetTextReader 检索二进制和字符串数据。
* SqliteConnection 的初始化现在很慢。

这些改进已实施到 ADO.NET Microsoft.Data.Sqlite 提供程序中，因此还可以在 EF Core 之外提升性能。

## <a name="preview-1"></a>预览版 1

### <a name="simple-logging"></a>简单的日志记录

此特性会添加类似于 EF6 中 `Database.Log` 的功能。
也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。

初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。

其他文档可通过问题 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) 进行跟踪。

### <a name="simple-way-to-get-generated-sql"></a>获取生成的 SQL 的简单方法

EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。

初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。

其他文档可通过问题 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) 进行跟踪。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>使用 C# 特性指示实体没有键

现在可以将实体类型配置为不使用新 `KeylessAttribute`的键。
例如：

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

文档可通过问题 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) 进行跟踪。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>可在初始化的 DbContext 上更改连接或连接字符串

现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。
而且，现在可以在上下文实例上更改连接或连接字符串。
此功能使得同一个上下文实例能够动态地连接到不同的数据库。

文档可通过问题 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) 进行跟踪。

### <a name="change-tracking-proxies"></a>更改跟踪代理

EF Core 现在可以生成自动实现 [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) 的运行时代理。
这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。
不过，代理有其自身的一组限制，因此并不适合所有人使用。

文档可通过问题 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) 进行跟踪。

### <a name="enhanced-debug-views"></a>增强的调试视图

调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。
在一段时间之前，我们就已经实现了模型的调试视图。
对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。

初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。

其他文档可通过问题 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) 进行跟踪。

### <a name="improved-handling-of-database-null-semantics"></a>改进了对数据库 null 语义的处理

关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。
而 C# 将 null 视为与其他任何 null 相比均相等的定义值。
默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。
EF Core 5.0 极大地提升了这些转换操作的效率。

文档可通过问题 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) 进行跟踪。

### <a name="indexer-properties"></a>索引器属性

EF Core 5.0 支持 C# 索引器属性的映射。
这写属性使得实体能够充当属性包，实体中的列被映射为包中的命名属性。

文档可通过问题 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) 进行跟踪。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>为枚举映射生成检查约束

EF Core 5.0 迁移现可为枚举属性映射生成检查约束。
例如：

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

文档可通过问题 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) 进行跟踪。

### <a name="isrelational"></a>IsRelational

除了现有 `IsSqlServer`、`IsSqlite` 和 `IsInMemory` 外，还添加了新的 `IsRelational` 方法。
此方法可用于测试 DbContext 是否使用任何关系数据库提供程序。
例如：

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

文档可通过问题 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) 进行跟踪。

### <a name="cosmos-optimistic-concurrency-with-etags"></a>使用 ETag 的 Cosmos 开放式并发

Azure Cosmos DB 数据库提供程序现在支持使用 ETag 的开放式并发。
使用 OnModelCreating 中的模型生成器配置 ETag：

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

然后，SaveChanges 将在并发冲突上引发 `DbUpdateConcurrencyException`，[可以处理](https://docs.microsoft.com/ef/core/saving/concurrency)它来实现重试等。

文档可通过问题 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) 进行跟踪。

### <a name="query-translations-for-more-datetime-constructs"></a>查询转换以获取更多 DateTime 构造

现在，包含新 DataTime 构造的查询会被转换。

此外，现在映射了以下 SQL Server 函数：

* DateDiffWeek
* DateFromParts

例如：

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translations-for-more-byte-array-constructs"></a>查询转换以获取更多字节数组构造

现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。

初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。

更多文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translation-for-reverse"></a>反向查询转换

现在，使用 `Reverse` 的查询会被转换。
例如：

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translation-for-bitwise-operators"></a>按位运算符的查询转换

现在，使用按位运算符的查询会在更多的情况下被转换，例如：

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos 上的字符串的查询转换

现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。

文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。
