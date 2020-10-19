---
title: EF Core 5.0 中的中断性变更 - EF Core
description: Entity Framework Core 5.0 中引入的中断性变更的完整列表
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065636"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="89fd6-103">EF Core 5.0 中的中断性变更</span><span class="sxs-lookup"><span data-stu-id="89fd6-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="89fd6-104">API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。</span><span class="sxs-lookup"><span data-stu-id="89fd6-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="89fd6-105">摘要</span><span class="sxs-lookup"><span data-stu-id="89fd6-105">Summary</span></span>

| <span data-ttu-id="89fd6-106">**中断性变更**</span><span class="sxs-lookup"><span data-stu-id="89fd6-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="89fd6-107">**影响**</span><span class="sxs-lookup"><span data-stu-id="89fd6-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="89fd6-108">具有不同语义的从主体到依赖项的导航中必需</span><span class="sxs-lookup"><span data-stu-id="89fd6-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="89fd6-109">中</span><span class="sxs-lookup"><span data-stu-id="89fd6-109">Medium</span></span>     |
| [<span data-ttu-id="89fd6-110">定义查询替换为特定于提供程序的方法</span><span class="sxs-lookup"><span data-stu-id="89fd6-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="89fd6-111">中</span><span class="sxs-lookup"><span data-stu-id="89fd6-111">Medium</span></span>     |
| [<span data-ttu-id="89fd6-112">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="89fd6-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="89fd6-113">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-113">Low</span></span>        |
| [<span data-ttu-id="89fd6-114">Cosmos：分区键现已添加到主键</span><span class="sxs-lookup"><span data-stu-id="89fd6-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="89fd6-115">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-115">Low</span></span>        |
| [<span data-ttu-id="89fd6-116">Cosmos：`id` 属性重命名为 `__id`</span><span class="sxs-lookup"><span data-stu-id="89fd6-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="89fd6-117">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-117">Low</span></span>        |
| <span data-ttu-id="89fd6-118">[Cosmos：byte[] 现在存储为 base64 字符串而不是数字数组](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="89fd6-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="89fd6-119">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-119">Low</span></span>        |
| [<span data-ttu-id="89fd6-120">Cosmos：GetPropertyName 和 SetPropertyName 已重命名</span><span class="sxs-lookup"><span data-stu-id="89fd6-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="89fd6-121">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-121">Low</span></span>        |
| [<span data-ttu-id="89fd6-122">当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器</span><span class="sxs-lookup"><span data-stu-id="89fd6-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="89fd6-123">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-123">Low</span></span>        |
| [<span data-ttu-id="89fd6-124">IMigrationsModelDiffer 当前使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="89fd6-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="89fd6-125">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-125">Low</span></span>        |
| [<span data-ttu-id="89fd6-126">鉴别器是只读的</span><span class="sxs-lookup"><span data-stu-id="89fd6-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="89fd6-127">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-127">Low</span></span>        |
| [<span data-ttu-id="89fd6-128">特定于提供程序的 EF.Functions 方法针对 InMemory 提供程序引发</span><span class="sxs-lookup"><span data-stu-id="89fd6-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="89fd6-129">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-129">Low</span></span>        |
| [<span data-ttu-id="89fd6-130">IndexBuilder.HasName 现已过时</span><span class="sxs-lookup"><span data-stu-id="89fd6-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="89fd6-131">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-131">Low</span></span>        |
| [<span data-ttu-id="89fd6-132">现已包括用于搭建实施了反向工程的模型的复数化程序</span><span class="sxs-lookup"><span data-stu-id="89fd6-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="89fd6-133">低</span><span class="sxs-lookup"><span data-stu-id="89fd6-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="89fd6-134">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="89fd6-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="89fd6-135">跟踪问题 #14257</span><span class="sxs-lookup"><span data-stu-id="89fd6-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="89fd6-136">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-136">**Old behavior**</span></span>

<span data-ttu-id="89fd6-137">HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="89fd6-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="89fd6-138">但是，之前它只影响数据库创建。</span><span class="sxs-lookup"><span data-stu-id="89fd6-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="89fd6-139">不需要指定它来查询具有其他维度的值。</span><span class="sxs-lookup"><span data-stu-id="89fd6-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="89fd6-140">之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。</span><span class="sxs-lookup"><span data-stu-id="89fd6-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="89fd6-141">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-141">**New behavior**</span></span>

<span data-ttu-id="89fd6-142">要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="89fd6-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="89fd6-143">该 API 与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。</span><span class="sxs-lookup"><span data-stu-id="89fd6-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="89fd6-144">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-144">**Why**</span></span>

<span data-ttu-id="89fd6-145">在列类型中指定维度后，不需要使用 HasGeometricDimension，该方法也很多余，因此我们已将它彻底删除。</span><span class="sxs-lookup"><span data-stu-id="89fd6-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="89fd6-146">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-146">**Mitigations**</span></span>

<span data-ttu-id="89fd6-147">使用 `HasColumnType` 指定维度：</span><span class="sxs-lookup"><span data-stu-id="89fd6-147">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="89fd6-148">具有不同语义的从主体到依赖项的导航中必需</span><span class="sxs-lookup"><span data-stu-id="89fd6-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="89fd6-149">跟踪问题 #17286</span><span class="sxs-lookup"><span data-stu-id="89fd6-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="89fd6-150">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-150">**Old behavior**</span></span>

<span data-ttu-id="89fd6-151">只有到主体的导航才能配置为“必需”。</span><span class="sxs-lookup"><span data-stu-id="89fd6-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="89fd6-152">因此，在对到依赖项（包含外键的实体）的导航使用 `RequiredAttribute` 时，将会改为在定义实体类型上创建外键。</span><span class="sxs-lookup"><span data-stu-id="89fd6-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="89fd6-153">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-153">**New behavior**</span></span>

<span data-ttu-id="89fd6-154">借助对所需依赖项新增的支持后，现在可以将任何引用导航标记为“必需”，这意味着在上述情况下，外键将在关系的另一端进行定义，并且这些属性不会标记为“必需”。</span><span class="sxs-lookup"><span data-stu-id="89fd6-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="89fd6-155">目前在指定依赖端之前是否调用 `IsRequired` 尚不明确：</span><span class="sxs-lookup"><span data-stu-id="89fd6-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="89fd6-156">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-156">**Why**</span></span>

<span data-ttu-id="89fd6-157">为了支持所需的依赖项，需要新行为（[请参阅 #12100](https://github.com/dotnet/efcore/issues/12100)）。</span><span class="sxs-lookup"><span data-stu-id="89fd6-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="89fd6-158">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-158">**Mitigations**</span></span>

<span data-ttu-id="89fd6-159">从到依赖项的导航中删除 `RequiredAttribute`，转而将其放置在到主体的导航中或在 `OnModelCreating` 中配置关系：</span><span class="sxs-lookup"><span data-stu-id="89fd6-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="89fd6-160">Cosmos：分区键现已添加到主键</span><span class="sxs-lookup"><span data-stu-id="89fd6-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="89fd6-161">跟踪问题 #15289</span><span class="sxs-lookup"><span data-stu-id="89fd6-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="89fd6-162">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-162">**Old behavior**</span></span>

<span data-ttu-id="89fd6-163">分区键仅添加到包含 `id` 的备用键。</span><span class="sxs-lookup"><span data-stu-id="89fd6-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="89fd6-164">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-164">**New behavior**</span></span>

<span data-ttu-id="89fd6-165">分区键现在还按约定添加到主键。</span><span class="sxs-lookup"><span data-stu-id="89fd6-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="89fd6-166">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-166">**Why**</span></span>

<span data-ttu-id="89fd6-167">此更改使模型更好地与 Azure Cosmos DB 语义对齐，并改进 `Find` 和某些查询的性能。</span><span class="sxs-lookup"><span data-stu-id="89fd6-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="89fd6-168">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-168">**Mitigations**</span></span>

<span data-ttu-id="89fd6-169">若要防止将分区键属性添加到主键，请在 `OnModelCreating` 中配置它。</span><span class="sxs-lookup"><span data-stu-id="89fd6-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="89fd6-170">Cosmos：`id` 属性重命名为 `__id`</span><span class="sxs-lookup"><span data-stu-id="89fd6-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="89fd6-171">跟踪问题 #17751</span><span class="sxs-lookup"><span data-stu-id="89fd6-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="89fd6-172">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-172">**Old behavior**</span></span>

<span data-ttu-id="89fd6-173">映射到 `id` JSON 属性的阴影属性也称为 `id`。</span><span class="sxs-lookup"><span data-stu-id="89fd6-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="89fd6-174">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-174">**New behavior**</span></span>

<span data-ttu-id="89fd6-175">按照约定创建的阴影属性现在命名为 `__id`。</span><span class="sxs-lookup"><span data-stu-id="89fd6-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="89fd6-176">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-176">**Why**</span></span>

<span data-ttu-id="89fd6-177">此更改使 `id` 属性与实体类型上的现有属性冲突的可能性更小。</span><span class="sxs-lookup"><span data-stu-id="89fd6-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="89fd6-178">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-178">**Mitigations**</span></span>

<span data-ttu-id="89fd6-179">若要返回到 3.x 行为，请在 `OnModelCreating` 中配置 `id` 属性。</span><span class="sxs-lookup"><span data-stu-id="89fd6-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="89fd6-180">Cosmos：byte[] 现在存储为 base64 字符串而不是数字数组</span><span class="sxs-lookup"><span data-stu-id="89fd6-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="89fd6-181">跟踪问题 #17306</span><span class="sxs-lookup"><span data-stu-id="89fd6-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="89fd6-182">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-182">**Old behavior**</span></span>

<span data-ttu-id="89fd6-183">类型 byte[] 的属性存储为数字数组。</span><span class="sxs-lookup"><span data-stu-id="89fd6-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="89fd6-184">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-184">**New behavior**</span></span>

<span data-ttu-id="89fd6-185">类型 byte[] 的属性现存储为 base64 字符串。</span><span class="sxs-lookup"><span data-stu-id="89fd6-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="89fd6-186">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-186">**Why**</span></span>

<span data-ttu-id="89fd6-187">byte[] 的这种表示形式与预期更好地对齐，并且是主要 JSON 序列化库的默认行为。</span><span class="sxs-lookup"><span data-stu-id="89fd6-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="89fd6-188">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-188">**Mitigations**</span></span>

<span data-ttu-id="89fd6-189">仍将正确查询作为数字数组存储的现有数据，但目前没有支持更改插入行为的方法。</span><span class="sxs-lookup"><span data-stu-id="89fd6-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="89fd6-190">如果此限制对于你的方案是一种阻碍，请对[此问题](https://github.com/aspnet/EntityFrameworkCore/issues/17306)发表评论</span><span class="sxs-lookup"><span data-stu-id="89fd6-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="89fd6-191">Cosmos：GetPropertyName 和 SetPropertyName 已重命名</span><span class="sxs-lookup"><span data-stu-id="89fd6-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="89fd6-192">跟踪问题 #17874</span><span class="sxs-lookup"><span data-stu-id="89fd6-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="89fd6-193">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-193">**Old behavior**</span></span>

<span data-ttu-id="89fd6-194">扩展方法此前称为 `GetPropertyName` 和 `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="89fd6-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="89fd6-195">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-195">**New behavior**</span></span>

<span data-ttu-id="89fd6-196">旧 API 已删除，添加了以下新方法：`GetJsonPropertyName`、`SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="89fd6-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="89fd6-197">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-197">**Why**</span></span>

<span data-ttu-id="89fd6-198">此更改消除了有关这些方法配置方式的歧义。</span><span class="sxs-lookup"><span data-stu-id="89fd6-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="89fd6-199">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-199">**Mitigations**</span></span>

<span data-ttu-id="89fd6-200">使用新 API。</span><span class="sxs-lookup"><span data-stu-id="89fd6-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="89fd6-201">当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器</span><span class="sxs-lookup"><span data-stu-id="89fd6-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="89fd6-202">跟踪问题 #15289</span><span class="sxs-lookup"><span data-stu-id="89fd6-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="89fd6-203">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-203">**Old behavior**</span></span>

<span data-ttu-id="89fd6-204">只有当实体状态更改为“已添加”时，才调用值生成器。</span><span class="sxs-lookup"><span data-stu-id="89fd6-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="89fd6-205">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-205">**New behavior**</span></span>

<span data-ttu-id="89fd6-206">目前，当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”，且属性包含默认值时，将调用值生成器。</span><span class="sxs-lookup"><span data-stu-id="89fd6-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="89fd6-207">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-207">**Why**</span></span>

<span data-ttu-id="89fd6-208">此更改是必要的，以便改进未保存到数据存储并始终在客户端上生成其值的属性方面的体验。</span><span class="sxs-lookup"><span data-stu-id="89fd6-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="89fd6-209">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-209">**Mitigations**</span></span>

<span data-ttu-id="89fd6-210">若要防止调用值生成器，请在状态更改前向属性分配一个非默认值。</span><span class="sxs-lookup"><span data-stu-id="89fd6-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="89fd6-211">IMigrationsModelDiffer 当前使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="89fd6-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="89fd6-212">跟踪问题 #20305</span><span class="sxs-lookup"><span data-stu-id="89fd6-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="89fd6-213">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-213">**Old behavior**</span></span>

<span data-ttu-id="89fd6-214">`IMigrationsModelDiffer` API 使用了 `IModel` 进行定义。</span><span class="sxs-lookup"><span data-stu-id="89fd6-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="89fd6-215">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-215">**New behavior**</span></span>

<span data-ttu-id="89fd6-216">`IMigrationsModelDiffer` API 当前使用 `IRelationalModel`。</span><span class="sxs-lookup"><span data-stu-id="89fd6-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="89fd6-217">不过，模型快照仍只包含 `IModel`，因为此代码是应用程序的一部分，并且实体框架无法在不做出较大中断性变更的情况下对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="89fd6-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="89fd6-218">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-218">**Why**</span></span>

<span data-ttu-id="89fd6-219">`IRelationalModel` 是新添加的数据库架构的表示形式。</span><span class="sxs-lookup"><span data-stu-id="89fd6-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="89fd6-220">使用它可以更快速、更精确地查找差异。</span><span class="sxs-lookup"><span data-stu-id="89fd6-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="89fd6-221">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-221">**Mitigations**</span></span>

<span data-ttu-id="89fd6-222">使用以下代码将 `context` 中的模型与 `snapshot` 中的模型进行比较：</span><span class="sxs-lookup"><span data-stu-id="89fd6-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
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

<span data-ttu-id="89fd6-223">我们计划在 6.0 中改进这种体验（[请参阅 #22031](https://github.com/dotnet/efcore/issues/22031)）</span><span class="sxs-lookup"><span data-stu-id="89fd6-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="89fd6-224">鉴别器是只读的</span><span class="sxs-lookup"><span data-stu-id="89fd6-224">Discriminators are read-only</span></span>

[<span data-ttu-id="89fd6-225">跟踪问题 #21154</span><span class="sxs-lookup"><span data-stu-id="89fd6-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="89fd6-226">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-226">**Old behavior**</span></span>

<span data-ttu-id="89fd6-227">在调用 `SaveChanges` 之前，可以更改鉴别器值</span><span class="sxs-lookup"><span data-stu-id="89fd6-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="89fd6-228">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-228">**New behavior**</span></span>

<span data-ttu-id="89fd6-229">在上述情况下，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="89fd6-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="89fd6-230">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-230">**Why**</span></span>

<span data-ttu-id="89fd6-231">EF 不希望实体类型仍在受到跟踪时就发生更改，因此更改鉴别器值会使上下文处于不一致的状态，这可能导致意外行为。</span><span class="sxs-lookup"><span data-stu-id="89fd6-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="89fd6-232">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-232">**Mitigations**</span></span>

<span data-ttu-id="89fd6-233">如果更改鉴别器值是必需的，并且在调用 `SaveChanges` 后将立即处理上下文，则可将鉴别器设置为可变：</span><span class="sxs-lookup"><span data-stu-id="89fd6-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="89fd6-234">定义查询替换为特定于提供程序的方法</span><span class="sxs-lookup"><span data-stu-id="89fd6-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="89fd6-235">跟踪问题 #18903</span><span class="sxs-lookup"><span data-stu-id="89fd6-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="89fd6-236">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-236">**Old behavior**</span></span>

<span data-ttu-id="89fd6-237">实体类型映射到定义核心级别的查询。</span><span class="sxs-lookup"><span data-stu-id="89fd6-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="89fd6-238">每当在查询中使用实体类型时，实体类型的根将被替换为任何提供程序的定义查询。</span><span class="sxs-lookup"><span data-stu-id="89fd6-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="89fd6-239">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-239">**New behavior**</span></span>

<span data-ttu-id="89fd6-240">已弃用用于定义查询的 API。</span><span class="sxs-lookup"><span data-stu-id="89fd6-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="89fd6-241">引入了特定于提供程序的新 API。</span><span class="sxs-lookup"><span data-stu-id="89fd6-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="89fd6-242">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-242">**Why**</span></span>

<span data-ttu-id="89fd6-243">在查询中使用查询根时，定义查询作为替换查询实现，但它有一些问题：</span><span class="sxs-lookup"><span data-stu-id="89fd6-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="89fd6-244">如果定义查询使用 `Select` 方法中的 `new { ... }` 预测实体类型，则将查询标识为实体需要额外的工作，并且与 EF Core 在查询中处理名义类型的方式不一致。</span><span class="sxs-lookup"><span data-stu-id="89fd6-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="89fd6-245">对于关系提供程序 `FromSql`，仍然需要以 LINQ 表达式格式传递 SQL 字符串。</span><span class="sxs-lookup"><span data-stu-id="89fd6-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="89fd6-246">定义查询最初是作为客户端视图引入的，用于无键实体的内存中提供程序（类似于关系数据库中的数据库视图）。</span><span class="sxs-lookup"><span data-stu-id="89fd6-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="89fd6-247">这种定义使得对内存中数据库测试应用程序变得容易。</span><span class="sxs-lookup"><span data-stu-id="89fd6-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="89fd6-248">之后，它们变得广泛适用，这很有用，但带来了不一致和难以理解的行为。</span><span class="sxs-lookup"><span data-stu-id="89fd6-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="89fd6-249">因此，我们决定简化概念。</span><span class="sxs-lookup"><span data-stu-id="89fd6-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="89fd6-250">我们使基于 LINQ 的定义查询独占内存中提供程序，并以不同的方式对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="89fd6-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="89fd6-251">有关详细信息，[请参阅此问题](https://github.com/dotnet/efcore/issues/20023)。</span><span class="sxs-lookup"><span data-stu-id="89fd6-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="89fd6-252">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-252">**Mitigations**</span></span>

<span data-ttu-id="89fd6-253">对于关系提供程序，在 `OnModelCreating` 中使用 `ToSqlQuery` 方法，然后传递 SQL 字符串以用于实体类型。</span><span class="sxs-lookup"><span data-stu-id="89fd6-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="89fd6-254">对于内存中提供程序，在 `OnModelCreating` 中使用 `ToInMemoryQuery` 方法，然后传递要用于实体类型 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="89fd6-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="89fd6-255">特定于提供程序的 EF.Functions 方法针对 InMemory 提供程序引发</span><span class="sxs-lookup"><span data-stu-id="89fd6-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="89fd6-256">跟踪问题 #20294</span><span class="sxs-lookup"><span data-stu-id="89fd6-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="89fd6-257">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-257">**Old behavior**</span></span>

<span data-ttu-id="89fd6-258">特定于提供程序的 EF.Functions 方法包含对客户端执行的实现，从而使这些方法可以在 InMemory 提供程序上执行。</span><span class="sxs-lookup"><span data-stu-id="89fd6-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="89fd6-259">例如，`EF.Functions.DateDiffDay` 是特定于 SQL Server 的方法，它在 InMemory 提供程序上运行。</span><span class="sxs-lookup"><span data-stu-id="89fd6-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="89fd6-260">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-260">**New behavior**</span></span>

<span data-ttu-id="89fd6-261">特定于提供程序的方法已更新为在其方法主体中引发异常，以阻止在客户端上对它们进行评估。</span><span class="sxs-lookup"><span data-stu-id="89fd6-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="89fd6-262">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-262">**Why**</span></span>

<span data-ttu-id="89fd6-263">特定于提供程序的方法映射到数据库函数。</span><span class="sxs-lookup"><span data-stu-id="89fd6-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="89fd6-264">在 LINQ 中，映射的数据库函数执行的计算无法每次都在客户端上进行复制。</span><span class="sxs-lookup"><span data-stu-id="89fd6-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="89fd6-265">当在客户端执行相同的方法时，这可能会导致来自服务器的结果有所不同。</span><span class="sxs-lookup"><span data-stu-id="89fd6-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="89fd6-266">由于这些方法会用于 LINQ，来转换到特定数据库函数，因此无需在客户端上评估这些方法。</span><span class="sxs-lookup"><span data-stu-id="89fd6-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="89fd6-267">由于 InMemory 提供程序是另一种数据库，因此这些方法不能用于此提供程序。</span><span class="sxs-lookup"><span data-stu-id="89fd6-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="89fd6-268">如果尝试为 InMemory 提供程序或任何其他无法转换这些方法的提供程序执行它们时，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="89fd6-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="89fd6-269">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-269">**Mitigations**</span></span>

<span data-ttu-id="89fd6-270">由于无法准确模拟数据库函数的行为，因此应根据生产中的同一种数据库测试包含这些函数的查询。</span><span class="sxs-lookup"><span data-stu-id="89fd6-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="89fd6-271">IndexBuilder.HasName 现已过时</span><span class="sxs-lookup"><span data-stu-id="89fd6-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="89fd6-272">跟踪问题 #21089</span><span class="sxs-lookup"><span data-stu-id="89fd6-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="89fd6-273">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-273">**Old behavior**</span></span>

<span data-ttu-id="89fd6-274">以前，只能在给定的一组属性上定义一个索引。</span><span class="sxs-lookup"><span data-stu-id="89fd6-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="89fd6-275">索引的数据库名称是使用 IndexBuilder.HasName 配置的。</span><span class="sxs-lookup"><span data-stu-id="89fd6-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="89fd6-276">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-276">**New behavior**</span></span>

<span data-ttu-id="89fd6-277">现在，允许在同一组属性上使用多个索引。</span><span class="sxs-lookup"><span data-stu-id="89fd6-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="89fd6-278">这些索引现在可以通过模型中的名称来区分。</span><span class="sxs-lookup"><span data-stu-id="89fd6-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="89fd6-279">按照约定，模型名称将用作数据库名称；不过，也可以使用 HasDatabaseName 单独进行配置。</span><span class="sxs-lookup"><span data-stu-id="89fd6-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="89fd6-280">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-280">**Why**</span></span>

<span data-ttu-id="89fd6-281">将来，我们希望使用同一组属性上的不同排序规则对索引启用升序和降序。</span><span class="sxs-lookup"><span data-stu-id="89fd6-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="89fd6-282">此更改将沿该方向转至其他步骤。</span><span class="sxs-lookup"><span data-stu-id="89fd6-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="89fd6-283">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-283">**Mitigations**</span></span>

<span data-ttu-id="89fd6-284">之前调用 IndexBuilder.HasName 的任何代码都应更新为调用 HasDatabaseName。</span><span class="sxs-lookup"><span data-stu-id="89fd6-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="89fd6-285">如果你的项目包含 EF Core 版本 2.0.0 之前生成的迁移，则可以安全地忽略这些文件中的警告，并通过添加 `#pragma warning disable 612, 618` 将其取消。</span><span class="sxs-lookup"><span data-stu-id="89fd6-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="89fd6-286">现已包括用于搭建实施了反向工程的模型的复数化程序</span><span class="sxs-lookup"><span data-stu-id="89fd6-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="89fd6-287">跟踪问题 #11160</span><span class="sxs-lookup"><span data-stu-id="89fd6-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="89fd6-288">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-288">**Old behavior**</span></span>

<span data-ttu-id="89fd6-289">以前，必须安装单独的复数化程序包，才能在通过对数据库架构实施反向工程来搭建 DbContext 和实体类型时设置 DbSet 和集合导航名称的复数形式并设置表名称的单数形式。</span><span class="sxs-lookup"><span data-stu-id="89fd6-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="89fd6-290">**新行为**</span><span class="sxs-lookup"><span data-stu-id="89fd6-290">**New behavior**</span></span>

<span data-ttu-id="89fd6-291">EF Core 现在包括使用 [Humanizer](https://github.com/Humanizr/Humanizer) 库的复数化程序。</span><span class="sxs-lookup"><span data-stu-id="89fd6-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="89fd6-292">这是 Visual Studio 用来推荐变量名称的同一个库。</span><span class="sxs-lookup"><span data-stu-id="89fd6-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="89fd6-293">**为什么**</span><span class="sxs-lookup"><span data-stu-id="89fd6-293">**Why**</span></span>

<span data-ttu-id="89fd6-294">对集合属性的单词使用复数形式并对类型和引用属性的单词使用单数形式在 .NET 中是惯用的。</span><span class="sxs-lookup"><span data-stu-id="89fd6-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="89fd6-295">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="89fd6-295">**Mitigations**</span></span>

<span data-ttu-id="89fd6-296">若要禁用复数化程序，请使用 `dotnet ef dbcontext scaffold` 上的 `--no-pluralize` 选项或 `Scaffold-DbContext` 上的 `-NoPluralize` 开关。</span><span class="sxs-lookup"><span data-stu-id="89fd6-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
