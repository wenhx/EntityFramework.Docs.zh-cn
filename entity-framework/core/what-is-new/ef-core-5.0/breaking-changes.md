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
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="84dfa-103">EF Core 5.0 中的中断性变更</span><span class="sxs-lookup"><span data-stu-id="84dfa-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="84dfa-104">API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。</span><span class="sxs-lookup"><span data-stu-id="84dfa-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="84dfa-105">摘要</span><span class="sxs-lookup"><span data-stu-id="84dfa-105">Summary</span></span>

| <span data-ttu-id="84dfa-106">**中断性变更**</span><span class="sxs-lookup"><span data-stu-id="84dfa-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="84dfa-107">**影响**</span><span class="sxs-lookup"><span data-stu-id="84dfa-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="84dfa-108">具有不同语义的从主体到依赖项的导航中必需</span><span class="sxs-lookup"><span data-stu-id="84dfa-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="84dfa-109">中</span><span class="sxs-lookup"><span data-stu-id="84dfa-109">Medium</span></span>     |
| [<span data-ttu-id="84dfa-110">定义查询替换为特定于提供程序的方法</span><span class="sxs-lookup"><span data-stu-id="84dfa-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="84dfa-111">中</span><span class="sxs-lookup"><span data-stu-id="84dfa-111">Medium</span></span>     |
| [<span data-ttu-id="84dfa-112">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="84dfa-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="84dfa-113">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-113">Low</span></span>        |
| [<span data-ttu-id="84dfa-114">Cosmos：分区键现已添加到主键</span><span class="sxs-lookup"><span data-stu-id="84dfa-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="84dfa-115">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-115">Low</span></span>        |
| [<span data-ttu-id="84dfa-116">Cosmos：`id` 属性重命名为 `__id`</span><span class="sxs-lookup"><span data-stu-id="84dfa-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="84dfa-117">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-117">Low</span></span>        |
| <span data-ttu-id="84dfa-118">[Cosmos：byte[] 现在存储为 base64 字符串而不是数字数组](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="84dfa-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="84dfa-119">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-119">Low</span></span>        |
| [<span data-ttu-id="84dfa-120">Cosmos：GetPropertyName 和 SetPropertyName 已重命名</span><span class="sxs-lookup"><span data-stu-id="84dfa-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="84dfa-121">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-121">Low</span></span>        |
| [<span data-ttu-id="84dfa-122">当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器</span><span class="sxs-lookup"><span data-stu-id="84dfa-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="84dfa-123">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-123">Low</span></span>        |
| [<span data-ttu-id="84dfa-124">IMigrationsModelDiffer 当前使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="84dfa-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="84dfa-125">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-125">Low</span></span>        |
| [<span data-ttu-id="84dfa-126">鉴别器是只读的</span><span class="sxs-lookup"><span data-stu-id="84dfa-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="84dfa-127">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-127">Low</span></span>        |
| [<span data-ttu-id="84dfa-128">特定于提供程序的 EF.Functions 方法针对 InMemory 提供程序引发</span><span class="sxs-lookup"><span data-stu-id="84dfa-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="84dfa-129">低</span><span class="sxs-lookup"><span data-stu-id="84dfa-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="84dfa-130">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="84dfa-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="84dfa-131">跟踪问题 #14257</span><span class="sxs-lookup"><span data-stu-id="84dfa-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="84dfa-132">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-132">**Old behavior**</span></span>

<span data-ttu-id="84dfa-133">HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="84dfa-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="84dfa-134">但是，之前它只影响数据库创建。</span><span class="sxs-lookup"><span data-stu-id="84dfa-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="84dfa-135">不需要指定它来查询具有其他维度的值。</span><span class="sxs-lookup"><span data-stu-id="84dfa-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="84dfa-136">之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。</span><span class="sxs-lookup"><span data-stu-id="84dfa-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="84dfa-137">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-137">**New behavior**</span></span>

<span data-ttu-id="84dfa-138">要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="84dfa-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="84dfa-139">该 API 与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。</span><span class="sxs-lookup"><span data-stu-id="84dfa-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="84dfa-140">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-140">**Why**</span></span>

<span data-ttu-id="84dfa-141">在列类型中指定维度后，不需要使用 HasGeometricDimension，该方法也很多余，因此我们已将它彻底删除。</span><span class="sxs-lookup"><span data-stu-id="84dfa-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="84dfa-142">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-142">**Mitigations**</span></span>

<span data-ttu-id="84dfa-143">使用 `HasColumnType` 指定维度：</span><span class="sxs-lookup"><span data-stu-id="84dfa-143">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="84dfa-144">具有不同语义的从主体到依赖项的导航中必需</span><span class="sxs-lookup"><span data-stu-id="84dfa-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="84dfa-145">跟踪问题 #17286</span><span class="sxs-lookup"><span data-stu-id="84dfa-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="84dfa-146">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-146">**Old behavior**</span></span>

<span data-ttu-id="84dfa-147">只有到主体的导航才能配置为“必需”。</span><span class="sxs-lookup"><span data-stu-id="84dfa-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="84dfa-148">因此，在对到依赖项（包含外键的实体）的导航使用 `RequiredAttribute` 时，将会改为在定义实体类型上创建外键。</span><span class="sxs-lookup"><span data-stu-id="84dfa-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="84dfa-149">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-149">**New behavior**</span></span>

<span data-ttu-id="84dfa-150">借助对所需依赖项新增的支持后，现在可以将任何引用导航标记为“必需”，这意味着在上述情况下，外键将在关系的另一端进行定义，并且这些属性不会标记为“必需”。</span><span class="sxs-lookup"><span data-stu-id="84dfa-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="84dfa-151">目前在指定依赖端之前是否调用 `IsRequired` 尚不明确：</span><span class="sxs-lookup"><span data-stu-id="84dfa-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="84dfa-152">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-152">**Why**</span></span>

<span data-ttu-id="84dfa-153">为了支持所需的依赖项，需要新行为（[请参阅 #12100](https://github.com/dotnet/efcore/issues/12100)）。</span><span class="sxs-lookup"><span data-stu-id="84dfa-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="84dfa-154">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-154">**Mitigations**</span></span>

<span data-ttu-id="84dfa-155">从到依赖项的导航中删除 `RequiredAttribute`，转而将其放置在到主体的导航中或在 `OnModelCreating` 中配置关系：</span><span class="sxs-lookup"><span data-stu-id="84dfa-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="84dfa-156">Cosmos：分区键现已添加到主键</span><span class="sxs-lookup"><span data-stu-id="84dfa-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="84dfa-157">跟踪问题 #15289</span><span class="sxs-lookup"><span data-stu-id="84dfa-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="84dfa-158">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-158">**Old behavior**</span></span>

<span data-ttu-id="84dfa-159">分区键仅添加到包含 `id` 的备用键。</span><span class="sxs-lookup"><span data-stu-id="84dfa-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="84dfa-160">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-160">**New behavior**</span></span>

<span data-ttu-id="84dfa-161">分区键现在还按约定添加到主键。</span><span class="sxs-lookup"><span data-stu-id="84dfa-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="84dfa-162">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-162">**Why**</span></span>

<span data-ttu-id="84dfa-163">此更改使模型更好地与 Azure Cosmos DB 语义对齐，并改进 `Find` 和某些查询的性能。</span><span class="sxs-lookup"><span data-stu-id="84dfa-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="84dfa-164">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-164">**Mitigations**</span></span>

<span data-ttu-id="84dfa-165">若要防止将分区键属性添加到主键，请在 `OnModelCreating` 中配置它。</span><span class="sxs-lookup"><span data-stu-id="84dfa-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="84dfa-166">Cosmos：`id` 属性重命名为 `__id`</span><span class="sxs-lookup"><span data-stu-id="84dfa-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="84dfa-167">跟踪问题 #17751</span><span class="sxs-lookup"><span data-stu-id="84dfa-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="84dfa-168">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-168">**Old behavior**</span></span>

<span data-ttu-id="84dfa-169">映射到 `id` JSON 属性的阴影属性也称为 `id`。</span><span class="sxs-lookup"><span data-stu-id="84dfa-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="84dfa-170">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-170">**New behavior**</span></span>

<span data-ttu-id="84dfa-171">按照约定创建的阴影属性现在命名为 `__id`。</span><span class="sxs-lookup"><span data-stu-id="84dfa-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="84dfa-172">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-172">**Why**</span></span>

<span data-ttu-id="84dfa-173">此更改使 `id` 属性与实体类型上的现有属性冲突的可能性更小。</span><span class="sxs-lookup"><span data-stu-id="84dfa-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="84dfa-174">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-174">**Mitigations**</span></span>

<span data-ttu-id="84dfa-175">若要返回到 3.x 行为，请在 `OnModelCreating` 中配置 `id` 属性。</span><span class="sxs-lookup"><span data-stu-id="84dfa-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="84dfa-176">Cosmos：byte[] 现在存储为 base64 字符串而不是数字数组</span><span class="sxs-lookup"><span data-stu-id="84dfa-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="84dfa-177">跟踪问题 #17306</span><span class="sxs-lookup"><span data-stu-id="84dfa-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="84dfa-178">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-178">**Old behavior**</span></span>

<span data-ttu-id="84dfa-179">类型 byte[] 的属性存储为数字数组。</span><span class="sxs-lookup"><span data-stu-id="84dfa-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="84dfa-180">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-180">**New behavior**</span></span>

<span data-ttu-id="84dfa-181">类型 byte[] 的属性现存储为 base64 字符串。</span><span class="sxs-lookup"><span data-stu-id="84dfa-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="84dfa-182">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-182">**Why**</span></span>

<span data-ttu-id="84dfa-183">byte[] 的这种表示形式与预期更好地对齐，并且是主要 JSON 序列化库的默认行为。</span><span class="sxs-lookup"><span data-stu-id="84dfa-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="84dfa-184">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-184">**Mitigations**</span></span>

<span data-ttu-id="84dfa-185">仍将正确查询作为数字数组存储的现有数据，但目前没有支持更改插入行为的方法。</span><span class="sxs-lookup"><span data-stu-id="84dfa-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="84dfa-186">如果此限制对于你的方案是一种阻碍，请对[此问题](https://github.com/aspnet/EntityFrameworkCore/issues/17306)发表评论</span><span class="sxs-lookup"><span data-stu-id="84dfa-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="84dfa-187">Cosmos：GetPropertyName 和 SetPropertyName 已重命名</span><span class="sxs-lookup"><span data-stu-id="84dfa-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="84dfa-188">跟踪问题 #17874</span><span class="sxs-lookup"><span data-stu-id="84dfa-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="84dfa-189">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-189">**Old behavior**</span></span>

<span data-ttu-id="84dfa-190">扩展方法此前称为 `GetPropertyName` 和 `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="84dfa-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="84dfa-191">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-191">**New behavior**</span></span>

<span data-ttu-id="84dfa-192">旧 API 已删除，添加了以下新方法：`GetJsonPropertyName`、`SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="84dfa-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="84dfa-193">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-193">**Why**</span></span>

<span data-ttu-id="84dfa-194">此更改消除了有关这些方法配置方式的歧义。</span><span class="sxs-lookup"><span data-stu-id="84dfa-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="84dfa-195">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-195">**Mitigations**</span></span>

<span data-ttu-id="84dfa-196">使用新 API。</span><span class="sxs-lookup"><span data-stu-id="84dfa-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="84dfa-197">当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”时，将调用值生成器</span><span class="sxs-lookup"><span data-stu-id="84dfa-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="84dfa-198">跟踪问题 #15289</span><span class="sxs-lookup"><span data-stu-id="84dfa-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="84dfa-199">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-199">**Old behavior**</span></span>

<span data-ttu-id="84dfa-200">只有当实体状态更改为“已添加”时，才调用值生成器。</span><span class="sxs-lookup"><span data-stu-id="84dfa-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="84dfa-201">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-201">**New behavior**</span></span>

<span data-ttu-id="84dfa-202">目前，当实体状态从“已分离”更改为“未更改”、“已更新”或“已删除”，且属性包含默认值时，将调用值生成器。</span><span class="sxs-lookup"><span data-stu-id="84dfa-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="84dfa-203">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-203">**Why**</span></span>

<span data-ttu-id="84dfa-204">此更改是必要的，以便改进未保存到数据存储并始终在客户端上生成其值的属性方面的体验。</span><span class="sxs-lookup"><span data-stu-id="84dfa-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="84dfa-205">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-205">**Mitigations**</span></span>

<span data-ttu-id="84dfa-206">若要防止调用值生成器，请在状态更改前向属性分配一个非默认值。</span><span class="sxs-lookup"><span data-stu-id="84dfa-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="84dfa-207">IMigrationsModelDiffer 当前使用 IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="84dfa-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="84dfa-208">跟踪问题 #20305</span><span class="sxs-lookup"><span data-stu-id="84dfa-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="84dfa-209">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-209">**Old behavior**</span></span>

<span data-ttu-id="84dfa-210">`IMigrationsModelDiffer` API 使用了 `IModel` 进行定义。</span><span class="sxs-lookup"><span data-stu-id="84dfa-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="84dfa-211">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-211">**New behavior**</span></span>

<span data-ttu-id="84dfa-212">`IMigrationsModelDiffer` API 当前使用 `IRelationalModel`。</span><span class="sxs-lookup"><span data-stu-id="84dfa-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="84dfa-213">不过，模型快照仍只包含 `IModel`，因为此代码是应用程序的一部分，并且实体框架无法在不做出较大中断性变更的情况下对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="84dfa-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="84dfa-214">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-214">**Why**</span></span>

<span data-ttu-id="84dfa-215">`IRelationalModel` 是新添加的数据库架构的表示形式。</span><span class="sxs-lookup"><span data-stu-id="84dfa-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="84dfa-216">使用它可以更快速、更精确地查找差异。</span><span class="sxs-lookup"><span data-stu-id="84dfa-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="84dfa-217">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-217">**Mitigations**</span></span>

<span data-ttu-id="84dfa-218">使用以下代码将 `context` 中的模型与 `snapshot` 中的模型进行比较：</span><span class="sxs-lookup"><span data-stu-id="84dfa-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="84dfa-219">我们计划在 6.0 中改进这种体验（[请参阅 #22031](https://github.com/dotnet/efcore/issues/22031)）</span><span class="sxs-lookup"><span data-stu-id="84dfa-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="84dfa-220">鉴别器是只读的</span><span class="sxs-lookup"><span data-stu-id="84dfa-220">Discriminators are read-only</span></span>

[<span data-ttu-id="84dfa-221">跟踪问题 #21154</span><span class="sxs-lookup"><span data-stu-id="84dfa-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="84dfa-222">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-222">**Old behavior**</span></span>

<span data-ttu-id="84dfa-223">在调用 `SaveChanges` 之前，可以更改鉴别器值</span><span class="sxs-lookup"><span data-stu-id="84dfa-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="84dfa-224">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-224">**New behavior**</span></span>

<span data-ttu-id="84dfa-225">在上述情况下，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="84dfa-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="84dfa-226">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-226">**Why**</span></span>

<span data-ttu-id="84dfa-227">EF 不希望实体类型仍在受到跟踪时就发生更改，因此更改鉴别器值会使上下文处于不一致的状态，这可能导致意外行为。</span><span class="sxs-lookup"><span data-stu-id="84dfa-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="84dfa-228">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-228">**Mitigations**</span></span>

<span data-ttu-id="84dfa-229">如果更改鉴别器值是必需的，并且在调用 `SaveChanges` 后将立即处理上下文，则可将鉴别器设置为可变：</span><span class="sxs-lookup"><span data-stu-id="84dfa-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="84dfa-230">定义查询替换为特定于提供程序的方法</span><span class="sxs-lookup"><span data-stu-id="84dfa-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="84dfa-231">跟踪问题 #18903</span><span class="sxs-lookup"><span data-stu-id="84dfa-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="84dfa-232">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-232">**Old behavior**</span></span>

<span data-ttu-id="84dfa-233">实体类型映射到定义核心级别的查询。</span><span class="sxs-lookup"><span data-stu-id="84dfa-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="84dfa-234">每当在查询中使用实体类型时，实体类型的根将被替换为任何提供程序的定义查询。</span><span class="sxs-lookup"><span data-stu-id="84dfa-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="84dfa-235">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-235">**New behavior**</span></span>

<span data-ttu-id="84dfa-236">已弃用用于定义查询的 API。</span><span class="sxs-lookup"><span data-stu-id="84dfa-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="84dfa-237">引入了特定于提供程序的新 API。</span><span class="sxs-lookup"><span data-stu-id="84dfa-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="84dfa-238">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-238">**Why**</span></span>

<span data-ttu-id="84dfa-239">在查询中使用查询根时，定义查询作为替换查询实现，但它有一些问题：</span><span class="sxs-lookup"><span data-stu-id="84dfa-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="84dfa-240">如果定义查询使用 `Select` 方法中的 `new { ... }` 预测实体类型，则将查询标识为实体需要额外的工作，并且与 EF Core 在查询中处理名义类型的方式不一致。</span><span class="sxs-lookup"><span data-stu-id="84dfa-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="84dfa-241">对于关系提供程序 `FromSql`，仍然需要以 LINQ 表达式格式传递 SQL 字符串。</span><span class="sxs-lookup"><span data-stu-id="84dfa-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="84dfa-242">定义查询最初是作为客户端视图引入的，用于无键实体的内存中提供程序（类似于关系数据库中的数据库视图）。</span><span class="sxs-lookup"><span data-stu-id="84dfa-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="84dfa-243">这种定义使得对内存中数据库测试应用程序变得容易。</span><span class="sxs-lookup"><span data-stu-id="84dfa-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="84dfa-244">之后，它们变得广泛适用，这很有用，但带来了不一致和难以理解的行为。</span><span class="sxs-lookup"><span data-stu-id="84dfa-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="84dfa-245">因此，我们决定简化概念。</span><span class="sxs-lookup"><span data-stu-id="84dfa-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="84dfa-246">我们使基于 LINQ 的定义查询独占内存中提供程序，并以不同的方式对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="84dfa-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="84dfa-247">有关详细信息，[请参阅此问题](https://github.com/dotnet/efcore/issues/20023)。</span><span class="sxs-lookup"><span data-stu-id="84dfa-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="84dfa-248">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-248">**Mitigations**</span></span>

<span data-ttu-id="84dfa-249">对于关系提供程序，在 `OnModelCreating` 中使用 `ToSqlQuery` 方法，然后传递 SQL 字符串以用于实体类型。</span><span class="sxs-lookup"><span data-stu-id="84dfa-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="84dfa-250">对于内存中提供程序，在 `OnModelCreating` 中使用 `ToInMemoryQuery` 方法，然后传递要用于实体类型 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="84dfa-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="84dfa-251">特定于提供程序的 EF.Functions 方法针对 InMemory 提供程序引发</span><span class="sxs-lookup"><span data-stu-id="84dfa-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="84dfa-252">跟踪问题 #20294</span><span class="sxs-lookup"><span data-stu-id="84dfa-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="84dfa-253">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-253">**Old behavior**</span></span>

<span data-ttu-id="84dfa-254">特定于提供程序的 EF.Functions 方法包含对客户端执行的实现，从而使这些方法可以在 InMemory 提供程序上执行。</span><span class="sxs-lookup"><span data-stu-id="84dfa-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="84dfa-255">例如，`EF.Functions.DateDiffDay` 是特定于 SQL Server 的方法，它在 InMemory 提供程序上运行。</span><span class="sxs-lookup"><span data-stu-id="84dfa-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="84dfa-256">**新行为**</span><span class="sxs-lookup"><span data-stu-id="84dfa-256">**New behavior**</span></span>

<span data-ttu-id="84dfa-257">特定于提供程序的方法已更新为在其方法主体中引发异常，以阻止在客户端上对它们进行评估。</span><span class="sxs-lookup"><span data-stu-id="84dfa-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="84dfa-258">**为什么**</span><span class="sxs-lookup"><span data-stu-id="84dfa-258">**Why**</span></span>

<span data-ttu-id="84dfa-259">特定于提供程序的方法映射到数据库函数。</span><span class="sxs-lookup"><span data-stu-id="84dfa-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="84dfa-260">在 LINQ 中，映射的数据库函数执行的计算无法每次都在客户端上进行复制。</span><span class="sxs-lookup"><span data-stu-id="84dfa-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="84dfa-261">当在客户端执行相同的方法时，这可能会导致来自服务器的结果有所不同。</span><span class="sxs-lookup"><span data-stu-id="84dfa-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="84dfa-262">由于这些方法会用于 LINQ，来转换到特定数据库函数，因此无需在客户端上评估这些方法。</span><span class="sxs-lookup"><span data-stu-id="84dfa-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="84dfa-263">由于 InMemory 提供程序是另一种数据库，因此这些方法不能用于此提供程序。</span><span class="sxs-lookup"><span data-stu-id="84dfa-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="84dfa-264">如果尝试为 InMemory 提供程序或任何其他无法转换这些方法的提供程序执行它们时，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="84dfa-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="84dfa-265">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="84dfa-265">**Mitigations**</span></span>

<span data-ttu-id="84dfa-266">由于无法准确模拟数据库函数的行为，因此应根据生产中的同一种数据库测试包含这些函数的查询。</span><span class="sxs-lookup"><span data-stu-id="84dfa-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
