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
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="17529-103">EF Core 5.0 中的中断性变更</span><span class="sxs-lookup"><span data-stu-id="17529-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="17529-104">API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。</span><span class="sxs-lookup"><span data-stu-id="17529-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="17529-105">摘要</span><span class="sxs-lookup"><span data-stu-id="17529-105">Summary</span></span>

| <span data-ttu-id="17529-106">**中断性变更**</span><span class="sxs-lookup"><span data-stu-id="17529-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="17529-107">**影响**</span><span class="sxs-lookup"><span data-stu-id="17529-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="17529-108">具有不同语义的从主体到依赖项的导航中必需</span><span class="sxs-lookup"><span data-stu-id="17529-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="17529-109">中</span><span class="sxs-lookup"><span data-stu-id="17529-109">Medium</span></span>     |
| [<span data-ttu-id="17529-110">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="17529-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="17529-111">低</span><span class="sxs-lookup"><span data-stu-id="17529-111">Low</span></span>        |
| [<span data-ttu-id="17529-112">当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器</span><span class="sxs-lookup"><span data-stu-id="17529-112">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>](#non-added-generation)  | <span data-ttu-id="17529-113">低</span><span class="sxs-lookup"><span data-stu-id="17529-113">Low</span></span>        |
| [<span data-ttu-id="17529-114">IMigrationsModelDiffer 当前使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="17529-114">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="17529-115">低</span><span class="sxs-lookup"><span data-stu-id="17529-115">Low</span></span>        |
| [<span data-ttu-id="17529-116">鉴别器是只读的</span><span class="sxs-lookup"><span data-stu-id="17529-116">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="17529-117">低</span><span class="sxs-lookup"><span data-stu-id="17529-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="17529-118">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="17529-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="17529-119">跟踪问题 #14257</span><span class="sxs-lookup"><span data-stu-id="17529-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="17529-120">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="17529-120">**Old behavior**</span></span>

<span data-ttu-id="17529-121">HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="17529-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="17529-122">但是，之前它只影响数据库创建。</span><span class="sxs-lookup"><span data-stu-id="17529-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="17529-123">不需要指定它来查询具有其他维度的值。</span><span class="sxs-lookup"><span data-stu-id="17529-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="17529-124">之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。</span><span class="sxs-lookup"><span data-stu-id="17529-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="17529-125">**新行为**</span><span class="sxs-lookup"><span data-stu-id="17529-125">**New behavior**</span></span>

<span data-ttu-id="17529-126">要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="17529-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="17529-127">这与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。</span><span class="sxs-lookup"><span data-stu-id="17529-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="17529-128">**为什么**</span><span class="sxs-lookup"><span data-stu-id="17529-128">**Why**</span></span>

<span data-ttu-id="17529-129">在列类型中指定维度后，不需要使用 HasGeometricDimension，该方法也很多余，因此我们已将它彻底删除。</span><span class="sxs-lookup"><span data-stu-id="17529-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="17529-130">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="17529-130">**Mitigations**</span></span>

<span data-ttu-id="17529-131">使用 `HasColumnType` 指定维度：</span><span class="sxs-lookup"><span data-stu-id="17529-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="17529-132">具有不同语义的从主体到依赖项的导航中必需</span><span class="sxs-lookup"><span data-stu-id="17529-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="17529-133">跟踪问题 #17286</span><span class="sxs-lookup"><span data-stu-id="17529-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="17529-134">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="17529-134">**Old behavior**</span></span>

<span data-ttu-id="17529-135">只有到主体的导航才能配置为“必需”。</span><span class="sxs-lookup"><span data-stu-id="17529-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="17529-136">因此，在对到依赖项（包含外键的实体）的导航使用 `RequiredAttribute` 时，将会改为在定义实体类型上创建外键。</span><span class="sxs-lookup"><span data-stu-id="17529-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="17529-137">**新行为**</span><span class="sxs-lookup"><span data-stu-id="17529-137">**New behavior**</span></span>

<span data-ttu-id="17529-138">借助对所需依赖项新增的支持后，现在可以将任何引用导航标记为“必需”，这意味着在上述情况下，外键将在关系的另一端进行定义，并且这些属性不会标记为“必需”。</span><span class="sxs-lookup"><span data-stu-id="17529-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="17529-139">目前在指定依赖端之前是否调用 `IsRequired` 尚不明确：</span><span class="sxs-lookup"><span data-stu-id="17529-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="17529-140">**为什么**</span><span class="sxs-lookup"><span data-stu-id="17529-140">**Why**</span></span>

<span data-ttu-id="17529-141">为了支持所需的依赖项，需要新行为（[请参阅 #12100](https://github.com/dotnet/efcore/issues/12100)）。</span><span class="sxs-lookup"><span data-stu-id="17529-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="17529-142">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="17529-142">**Mitigations**</span></span>

<span data-ttu-id="17529-143">从到依赖项的导航中删除 `RequiredAttribute`，转而将其放置在到主体的导航中或在 `OnModelCreating` 中配置关系：</span><span class="sxs-lookup"><span data-stu-id="17529-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="17529-144">当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器</span><span class="sxs-lookup"><span data-stu-id="17529-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="17529-145">跟踪问题 #15289</span><span class="sxs-lookup"><span data-stu-id="17529-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="17529-146">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="17529-146">**Old behavior**</span></span>

<span data-ttu-id="17529-147">只有当实体状态更改为“已添加”时，才调用值生成器。</span><span class="sxs-lookup"><span data-stu-id="17529-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="17529-148">**新行为**</span><span class="sxs-lookup"><span data-stu-id="17529-148">**New behavior**</span></span>

<span data-ttu-id="17529-149">目前，当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”，且属性包含默认值时，将调用值生成器。</span><span class="sxs-lookup"><span data-stu-id="17529-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="17529-150">**为什么**</span><span class="sxs-lookup"><span data-stu-id="17529-150">**Why**</span></span>

<span data-ttu-id="17529-151">此更改是必要的，以便改进未保存到数据存储并始终在客户端上生成其值的属性方面的体验。</span><span class="sxs-lookup"><span data-stu-id="17529-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="17529-152">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="17529-152">**Mitigations**</span></span>

<span data-ttu-id="17529-153">若要防止调用值生成器，请在状态更改前向属性分配一个非默认值。</span><span class="sxs-lookup"><span data-stu-id="17529-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="17529-154">IMigrationsModelDiffer 当前使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="17529-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="17529-155">跟踪问题 #20305</span><span class="sxs-lookup"><span data-stu-id="17529-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="17529-156">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="17529-156">**Old behavior**</span></span>

<span data-ttu-id="17529-157">`IMigrationsModelDiffer` API 使用了 `IModel` 进行定义。</span><span class="sxs-lookup"><span data-stu-id="17529-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="17529-158">**新行为**</span><span class="sxs-lookup"><span data-stu-id="17529-158">**New behavior**</span></span>

<span data-ttu-id="17529-159">`IMigrationsModelDiffer` API 当前使用 `IRelationalModel`。</span><span class="sxs-lookup"><span data-stu-id="17529-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="17529-160">不过，模型快照仍只包含 `IModel`，因为此代码是应用程序的一部分，并且实体框架无法在不做出较大中断性变更的情况下对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="17529-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="17529-161">**为什么**</span><span class="sxs-lookup"><span data-stu-id="17529-161">**Why**</span></span>

<span data-ttu-id="17529-162">`IRelationalModel` 是新添加的数据库架构的表示形式。</span><span class="sxs-lookup"><span data-stu-id="17529-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="17529-163">使用它可以更快速、更精确地查找差异。</span><span class="sxs-lookup"><span data-stu-id="17529-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="17529-164">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="17529-164">**Mitigations**</span></span>

<span data-ttu-id="17529-165">使用以下代码将 `context` 中的模型与 `snapshot` 中的模型进行比较：</span><span class="sxs-lookup"><span data-stu-id="17529-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="17529-166">我们计划在 6.0 中改进这种体验（[请参阅 #22031](https://github.com/dotnet/efcore/issues/22031)）</span><span class="sxs-lookup"><span data-stu-id="17529-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="17529-167">鉴别器是只读的</span><span class="sxs-lookup"><span data-stu-id="17529-167">Discriminators are read-only</span></span>

[<span data-ttu-id="17529-168">跟踪问题 #21154</span><span class="sxs-lookup"><span data-stu-id="17529-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="17529-169">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="17529-169">**Old behavior**</span></span>

<span data-ttu-id="17529-170">在调用 `SaveChanges` 之前，可以更改鉴别器值</span><span class="sxs-lookup"><span data-stu-id="17529-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="17529-171">**新行为**</span><span class="sxs-lookup"><span data-stu-id="17529-171">**New behavior**</span></span>

<span data-ttu-id="17529-172">在上述情况下，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="17529-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="17529-173">**为什么**</span><span class="sxs-lookup"><span data-stu-id="17529-173">**Why**</span></span>

<span data-ttu-id="17529-174">EF 不希望实体类型仍在受到跟踪时就发生更改，因此更改鉴别器值会使上下文处于不一致的状态，这可能导致意外行为。</span><span class="sxs-lookup"><span data-stu-id="17529-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="17529-175">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="17529-175">**Mitigations**</span></span>

<span data-ttu-id="17529-176">如果更改鉴别器值是必需的，并且在调用 `SaveChanges` 后将立即处理上下文，则可将鉴别器设置为可变：</span><span class="sxs-lookup"><span data-stu-id="17529-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
