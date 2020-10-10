---
title: EF Core 5.0 中的中断性变更 - EF Core
description: Entity Framework Core 5.0 中引入的中断性变更的完整列表
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210362"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的中断性变更

API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。

## <a name="summary"></a>摘要

| **中断性变更**                                                                                                                   | **影响** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [具有不同语义的从主体到依赖项的导航中必需](#required-dependent)                                 | 中     |
| [定义查询替换为特定于提供程序的方法](#defining-query)                                                          | 中     |
| [从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法](#geometric-sqlite)                                                   | 低        |
| [Cosmos：分区键现已添加到主键](#cosmos-partition-key)                                                        | 低        |
| [Cosmos：`id` 属性重命名为 `__id`](#cosmos-id)                                                                                 | 低        |
| [Cosmos：byte[] 现在存储为 base64 字符串而不是数字数组](#cosmos-byte)                                             | 低        |
| [Cosmos：GetPropertyName 和 SetPropertyName 已重命名](#cosmos-metadata)                                                          | 低        |
| [当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器](#non-added-generation) | 低        |
| [IMigrationsModelDiffer 当前使用 IRelationalModel](#relational-model)                                                                 | 低        |
| [鉴别器是只读的](#read-only-discriminators)                                                                             | 低        |
| [特定于提供程序的 EF.Functions 方法针对 InMemory 提供程序引发](#no-client-methods)                                              | 低        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法

[跟踪问题 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**旧行为**

HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。 但是，之前它只影响数据库创建。 不需要指定它来查询具有其他维度的值。 之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。

**新行为**

要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。 该 API 与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。

**为什么**

在列类型中指定维度后，不需要使用 HasGeometricDimension，该方法也很多余，因此我们已将它彻底删除。

**缓解措施**

使用 `HasColumnType` 指定维度：

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>具有不同语义的从主体到依赖项的导航中必需

[跟踪问题 #17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**旧行为**

只有到主体的导航才能配置为“必需”。 因此，在对到依赖项（包含外键的实体）的导航使用 `RequiredAttribute` 时，将会改为在定义实体类型上创建外键。

**新行为**

借助对所需依赖项新增的支持后，现在可以将任何引用导航标记为“必需”，这意味着在上述情况下，外键将在关系的另一端进行定义，并且这些属性不会标记为“必需”。

目前在指定依赖端之前是否调用 `IsRequired` 尚不明确：

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**为什么**

为了支持所需的依赖项，需要新行为（[请参阅 #12100](https://github.com/dotnet/efcore/issues/12100)）。

**缓解措施**

从到依赖项的导航中删除 `RequiredAttribute`，转而将其放置在到主体的导航中或在 `OnModelCreating` 中配置关系：

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos：分区键现已添加到主键

[跟踪问题 #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**旧行为**

分区键仅添加到包含 `id` 的备用键。

**新行为**

分区键现在还按约定添加到主键。

**为什么**

此更改使模型更好地与 Azure Cosmos DB 语义对齐，并改进 `Find` 和某些查询的性能。

**缓解措施**

若要防止将分区键属性添加到主键，请在 `OnModelCreating` 中配置它。

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos：`id` 属性重命名为 `__id`

[跟踪问题 #17751](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

**旧行为**

映射到 `id` JSON 属性的阴影属性也称为 `id`。

**新行为**

按照约定创建的阴影属性现在命名为 `__id`。

**为什么**

此更改使 `id` 属性与实体类型上的现有属性冲突的可能性更小。

**缓解措施**

若要返回到 3.x 行为，请在 `OnModelCreating` 中配置 `id` 属性。

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos：byte[] 现在存储为 base64 字符串而不是数字数组

[跟踪问题 #17306](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

**旧行为**

类型 byte[] 的属性存储为数字数组。

**新行为**

类型 byte[] 的属性现存储为 base64 字符串。

**为什么**

byte[] 的这种表示形式与预期更好地对齐，并且是主要 JSON 序列化库的默认行为。

**缓解措施**

仍将正确查询作为数字数组存储的现有数据，但目前没有支持更改插入行为的方法。 如果此限制对于你的方案是一种阻碍，请对[此问题](https://github.com/aspnet/EntityFrameworkCore/issues/17306)发表评论

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos：GetPropertyName 和 SetPropertyName 已重命名

[跟踪问题 #17874](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

**旧行为**

扩展方法此前称为 `GetPropertyName` 和 `SetPropertyName`

**新行为**

旧 API 已删除，添加了以下新方法：`GetJsonPropertyName`、`SetJsonPropertyName`

**为什么**

此更改消除了有关这些方法配置方式的歧义。

**缓解措施**

使用新 API。

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器

[跟踪问题 #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**旧行为**

只有当实体状态更改为“已添加”时，才调用值生成器。

**新行为**

目前，当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”，且属性包含默认值时，将调用值生成器。

**为什么**

此更改是必要的，以便改进未保存到数据存储并始终在客户端上生成其值的属性方面的体验。

**缓解措施**

若要防止调用值生成器，请在状态更改前向属性分配一个非默认值。

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer 当前使用 IRelationalModel

[跟踪问题 #20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**旧行为**

`IMigrationsModelDiffer` API 使用了 `IModel` 进行定义。

**新行为**

`IMigrationsModelDiffer` API 当前使用 `IRelationalModel`。 不过，模型快照仍只包含 `IModel`，因为此代码是应用程序的一部分，并且实体框架无法在不做出较大中断性变更的情况下对其进行更改。

**为什么**

`IRelationalModel` 是新添加的数据库架构的表示形式。 使用它可以更快速、更精确地查找差异。

**缓解措施**

使用以下代码将 `context` 中的模型与 `snapshot` 中的模型进行比较：

```cs
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

我们计划在 6.0 中改进这种体验（[请参阅 #22031](https://github.com/dotnet/efcore/issues/22031)）

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a>鉴别器是只读的

[跟踪问题 #21154](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**旧行为**

在调用 `SaveChanges` 之前，可以更改鉴别器值

**新行为**

在上述情况下，将引发异常。

**为什么**

EF 不希望实体类型仍在受到跟踪时就发生更改，因此更改鉴别器值会使上下文处于不一致的状态，这可能导致意外行为。

**缓解措施**

如果更改鉴别器值是必需的，并且在调用 `SaveChanges` 后将立即处理上下文，则可将鉴别器设置为可变：

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>定义查询替换为特定于提供程序的方法

[跟踪问题 #18903](https://github.com/dotnet/efcore/issues/18903)

**旧行为**

实体类型映射到定义核心级别的查询。 每当在查询中使用实体类型时，实体类型的根将被替换为任何提供程序的定义查询。

**新行为**

已弃用用于定义查询的 API。 引入了特定于提供程序的新 API。

**为什么**

在查询中使用查询根时，定义查询作为替换查询实现，但它有一些问题：

- 如果定义查询使用 `Select` 方法中的 `new { ... }` 预测实体类型，则将查询标识为实体需要额外的工作，并且与 EF Core 在查询中处理名义类型的方式不一致。
- 对于关系提供程序 `FromSql`，仍然需要以 LINQ 表达式格式传递 SQL 字符串。

定义查询最初是作为客户端视图引入的，用于无键实体的内存中提供程序（类似于关系数据库中的数据库视图）。 这种定义使得对内存中数据库测试应用程序变得容易。 之后，它们变得广泛适用，这很有用，但带来了不一致和难以理解的行为。 因此，我们决定简化概念。 我们使基于 LINQ 的定义查询独占内存中提供程序，并以不同的方式对其进行处理。 有关详细信息，[请参阅此问题](https://github.com/dotnet/efcore/issues/20023)。

**缓解措施**

对于关系提供程序，在 `OnModelCreating` 中使用 `ToSqlQuery` 方法，然后传递 SQL 字符串以用于实体类型。
对于内存中提供程序，在 `OnModelCreating` 中使用 `ToInMemoryQuery` 方法，然后传递要用于实体类型 LINQ 查询。

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>特定于提供程序的 EF.Functions 方法针对 InMemory 提供程序引发

[跟踪问题 #20294](https://github.com/dotnet/efcore/issues/20294)

**旧行为**

特定于提供程序的 EF.Functions 方法包含对客户端执行的实现，从而使这些方法可以在 InMemory 提供程序上执行。 例如，`EF.Functions.DateDiffDay` 是特定于 SQL Server 的方法，它在 InMemory 提供程序上运行。

**新行为**

特定于提供程序的方法已更新为在其方法主体中引发异常，以阻止在客户端上对它们进行评估。

**为什么**

特定于提供程序的方法映射到数据库函数。 在 LINQ 中，映射的数据库函数执行的计算无法每次都在客户端上进行复制。 当在客户端执行相同的方法时，这可能会导致来自服务器的结果有所不同。 由于这些方法会用于 LINQ，来转换到特定数据库函数，因此无需在客户端上评估这些方法。 由于 InMemory 提供程序是另一种数据库，因此这些方法不能用于此提供程序。 如果尝试为 InMemory 提供程序或任何其他无法转换这些方法的提供程序执行它们时，将引发异常。

**缓解措施**

由于无法准确模拟数据库函数的行为，因此应根据生产中的同一种数据库测试包含这些函数的查询。
