---
title: EF Core 5.0 中的中断性变更 - EF Core
description: Entity Framework Core 5.0 中引入的中断性变更的完整列表
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618678"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的中断性变更

API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。

## <a name="summary"></a>摘要

| **中断性变更**                                                                                                                   | **影响** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [具有不同语义的从主体到依赖项的导航中必需](#required-dependent)                                 | 中     |
| [从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法](#geometric-sqlite)                                                   | 低        |
| [当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器](#non-added-generation)  | 低        |
| [IMigrationsModelDiffer 当前使用 IRelationalModel](#relational-model)                                                                 | 低        |
| [鉴别器是只读的](#read-only-discriminators)                                                                             | 低        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法

[跟踪问题 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**旧行为**

HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。 但是，之前它只影响数据库创建。 不需要指定它来查询具有其他维度的值。 之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。

**新行为**

要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。 这与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。

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
