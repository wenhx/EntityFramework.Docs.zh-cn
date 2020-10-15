---
title: SQLite 数据库提供程序-限制-EF Core
description: 与其他提供程序相比 Entity Framework Core SQLite 数据库提供程序的限制
author: bricelam
ms.date: 09/24/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 3d696474d401e8fd6c26a78067d292f0bb97a457
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062732"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="8f243-103">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="8f243-103">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="8f243-104">SQLite 提供程序有很多迁移限制。</span><span class="sxs-lookup"><span data-stu-id="8f243-104">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="8f243-105">其中的大多数限制是由基础 SQLite 数据库引擎中的限制引起的，并不特定于 EF。</span><span class="sxs-lookup"><span data-stu-id="8f243-105">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="8f243-106">建模限制</span><span class="sxs-lookup"><span data-stu-id="8f243-106">Modeling limitations</span></span>

<span data-ttu-id="8f243-107">公共关系库 (实体框架关系数据库提供程序共享，) 定义 Api，用于建模大多数关系数据库引擎所共有的概念。</span><span class="sxs-lookup"><span data-stu-id="8f243-107">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="8f243-108">SQLite 提供程序不支持其中几个概念。</span><span class="sxs-lookup"><span data-stu-id="8f243-108">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="8f243-109">架构</span><span class="sxs-lookup"><span data-stu-id="8f243-109">Schemas</span></span>
* <span data-ttu-id="8f243-110">序列</span><span class="sxs-lookup"><span data-stu-id="8f243-110">Sequences</span></span>

## <a name="query-limitations"></a><span data-ttu-id="8f243-111">查询限制</span><span class="sxs-lookup"><span data-stu-id="8f243-111">Query limitations</span></span>

<span data-ttu-id="8f243-112">SQLite 本身并不支持以下数据类型。</span><span class="sxs-lookup"><span data-stu-id="8f243-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="8f243-113">EF Core 可以读取和写入这些类型的值，并且还支持) 的相等性 (查询 `where e.Property == value` 。</span><span class="sxs-lookup"><span data-stu-id="8f243-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="8f243-114">但其他操作（如比较和排序）将需要对客户端进行评估。</span><span class="sxs-lookup"><span data-stu-id="8f243-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="8f243-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="8f243-115">DateTimeOffset</span></span>
* <span data-ttu-id="8f243-116">小数</span><span class="sxs-lookup"><span data-stu-id="8f243-116">Decimal</span></span>
* <span data-ttu-id="8f243-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="8f243-117">TimeSpan</span></span>
* <span data-ttu-id="8f243-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="8f243-118">UInt64</span></span>

<span data-ttu-id="8f243-119">`DateTimeOffset`建议使用 DateTime 值，而不是。</span><span class="sxs-lookup"><span data-stu-id="8f243-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="8f243-120">处理多个时区时，建议在保存之前将值转换为 UTC，然后将其转换回适当的时区。</span><span class="sxs-lookup"><span data-stu-id="8f243-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="8f243-121">`Decimal`类型提供了较高的精度。</span><span class="sxs-lookup"><span data-stu-id="8f243-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="8f243-122">但是，如果不需要该级别的精度，则建议改为使用 double。</span><span class="sxs-lookup"><span data-stu-id="8f243-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="8f243-123">您可以使用 [值转换器](xref:core/modeling/value-conversions) 在类中继续使用 decimal。</span><span class="sxs-lookup"><span data-stu-id="8f243-123">You can use a [value converter](xref:core/modeling/value-conversions) to continue using decimal in your classes.</span></span>

```csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="8f243-124">迁移限制</span><span class="sxs-lookup"><span data-stu-id="8f243-124">Migrations limitations</span></span>

<span data-ttu-id="8f243-125">SQLite 数据库引擎不支持许多其他关系数据库所支持的架构操作。</span><span class="sxs-lookup"><span data-stu-id="8f243-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="8f243-126">如果尝试将不受支持的操作之一应用于 SQLite 数据库，则 `NotSupportedException` 会引发。</span><span class="sxs-lookup"><span data-stu-id="8f243-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

<span data-ttu-id="8f243-127">将尝试重新生成以执行特定操作。</span><span class="sxs-lookup"><span data-stu-id="8f243-127">A rebuild will be attempted in order to perform certain operations.</span></span> <span data-ttu-id="8f243-128">重新生成仅可用于作为 EF Core 模型一部分的数据库项目。</span><span class="sxs-lookup"><span data-stu-id="8f243-128">Rebuilds are only possible for database artifacts that are part of your EF Core model.</span></span> <span data-ttu-id="8f243-129">如果数据库项目不是模型的一部分，例如，如果它是在迁移内手动创建的，则 `NotSupportedException` 仍会引发。</span><span class="sxs-lookup"><span data-stu-id="8f243-129">If a database artifact isn't part of the model--for example, if it was created manually inside a migration--then a `NotSupportedException` is still thrown.</span></span>

| <span data-ttu-id="8f243-130">操作</span><span class="sxs-lookup"><span data-stu-id="8f243-130">Operation</span></span>            | <span data-ttu-id="8f243-131">是否支持？</span><span class="sxs-lookup"><span data-stu-id="8f243-131">Supported?</span></span>  | <span data-ttu-id="8f243-132">需要版本</span><span class="sxs-lookup"><span data-stu-id="8f243-132">Requires version</span></span> |
|:---------------------|:------------|:-----------------|
| <span data-ttu-id="8f243-133">AddCheckConstraint</span><span class="sxs-lookup"><span data-stu-id="8f243-133">AddCheckConstraint</span></span>   | <span data-ttu-id="8f243-134">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-134">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-135">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-135">5.0</span></span>              |
| <span data-ttu-id="8f243-136">AddColumn</span><span class="sxs-lookup"><span data-stu-id="8f243-136">AddColumn</span></span>            | <span data-ttu-id="8f243-137">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-137">✔</span></span>           |                  |
| <span data-ttu-id="8f243-138">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="8f243-138">AddForeignKey</span></span>        | <span data-ttu-id="8f243-139">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-139">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-140">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-140">5.0</span></span>              |
| <span data-ttu-id="8f243-141">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="8f243-141">AddPrimaryKey</span></span>        | <span data-ttu-id="8f243-142">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-142">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-143">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-143">5.0</span></span>              |
| <span data-ttu-id="8f243-144">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="8f243-144">AddUniqueConstraint</span></span>  | <span data-ttu-id="8f243-145">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-145">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-146">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-146">5.0</span></span>              |
| <span data-ttu-id="8f243-147">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="8f243-147">AlterColumn</span></span>          | <span data-ttu-id="8f243-148">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-148">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-149">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-149">5.0</span></span>              |
| <span data-ttu-id="8f243-150">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="8f243-150">CreateIndex</span></span>          | <span data-ttu-id="8f243-151">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-151">✔</span></span>           |                  |
| <span data-ttu-id="8f243-152">CreateTable</span><span class="sxs-lookup"><span data-stu-id="8f243-152">CreateTable</span></span>          | <span data-ttu-id="8f243-153">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-153">✔</span></span>           |                  |
| <span data-ttu-id="8f243-154">DropCheckConstraint</span><span class="sxs-lookup"><span data-stu-id="8f243-154">DropCheckConstraint</span></span>  | <span data-ttu-id="8f243-155">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-155">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-156">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-156">5.0</span></span>              |
| <span data-ttu-id="8f243-157">DropColumn</span><span class="sxs-lookup"><span data-stu-id="8f243-157">DropColumn</span></span>           | <span data-ttu-id="8f243-158">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-158">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-159">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-159">5.0</span></span>              |
| <span data-ttu-id="8f243-160">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="8f243-160">DropForeignKey</span></span>       | <span data-ttu-id="8f243-161">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-161">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-162">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-162">5.0</span></span>              |
| <span data-ttu-id="8f243-163">DropIndex</span><span class="sxs-lookup"><span data-stu-id="8f243-163">DropIndex</span></span>            | <span data-ttu-id="8f243-164">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-164">✔</span></span>           |                  |
| <span data-ttu-id="8f243-165">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="8f243-165">DropPrimaryKey</span></span>       | <span data-ttu-id="8f243-166">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-166">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-167">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-167">5.0</span></span>              |
| <span data-ttu-id="8f243-168">DropTable</span><span class="sxs-lookup"><span data-stu-id="8f243-168">DropTable</span></span>            | <span data-ttu-id="8f243-169">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-169">✔</span></span>           |                  |
| <span data-ttu-id="8f243-170">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="8f243-170">DropUniqueConstraint</span></span> | <span data-ttu-id="8f243-171">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-171">✔ (rebuild)</span></span> | <span data-ttu-id="8f243-172">5.0</span><span class="sxs-lookup"><span data-stu-id="8f243-172">5.0</span></span>              |
| <span data-ttu-id="8f243-173">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="8f243-173">RenameColumn</span></span>         | <span data-ttu-id="8f243-174">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-174">✔</span></span>           | <span data-ttu-id="8f243-175">2.2</span><span class="sxs-lookup"><span data-stu-id="8f243-175">2.2</span></span>              |
| <span data-ttu-id="8f243-176">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="8f243-176">RenameIndex</span></span>          | <span data-ttu-id="8f243-177">重新生成 (✔) </span><span class="sxs-lookup"><span data-stu-id="8f243-177">✔ (rebuild)</span></span> |                  |
| <span data-ttu-id="8f243-178">RenameTable</span><span class="sxs-lookup"><span data-stu-id="8f243-178">RenameTable</span></span>          | <span data-ttu-id="8f243-179">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-179">✔</span></span>           |                  |
| <span data-ttu-id="8f243-180">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="8f243-180">EnsureSchema</span></span>         | <span data-ttu-id="8f243-181">✔ (的) </span><span class="sxs-lookup"><span data-stu-id="8f243-181">✔ (no-op)</span></span>   |                  |
| <span data-ttu-id="8f243-182">DropSchema</span><span class="sxs-lookup"><span data-stu-id="8f243-182">DropSchema</span></span>           | <span data-ttu-id="8f243-183">✔ (的) </span><span class="sxs-lookup"><span data-stu-id="8f243-183">✔ (no-op)</span></span>   |                  |
| <span data-ttu-id="8f243-184">插入</span><span class="sxs-lookup"><span data-stu-id="8f243-184">Insert</span></span>               | <span data-ttu-id="8f243-185">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-185">✔</span></span>           |                  |
| <span data-ttu-id="8f243-186">更新</span><span class="sxs-lookup"><span data-stu-id="8f243-186">Update</span></span>               | <span data-ttu-id="8f243-187">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-187">✔</span></span>           |                  |
| <span data-ttu-id="8f243-188">删除</span><span class="sxs-lookup"><span data-stu-id="8f243-188">Delete</span></span>               | <span data-ttu-id="8f243-189">✔</span><span class="sxs-lookup"><span data-stu-id="8f243-189">✔</span></span>           |                  |

### <a name="migrations-limitations-workaround"></a><span data-ttu-id="8f243-190">迁移限制解决方法</span><span class="sxs-lookup"><span data-stu-id="8f243-190">Migrations limitations workaround</span></span>

<span data-ttu-id="8f243-191">通过在迁移中手动编写代码来执行重新生成，可以解决其中一些限制。</span><span class="sxs-lookup"><span data-stu-id="8f243-191">You can workaround some of these limitations by manually writing code in your migrations to perform a rebuild.</span></span> <span data-ttu-id="8f243-192">表重建涉及创建新表，将数据复制到新表，删除旧表，重命名新表。</span><span class="sxs-lookup"><span data-stu-id="8f243-192">Table rebuilds involve creating a new table, copying data to the new table, dropping the old table, renaming the new table.</span></span> <span data-ttu-id="8f243-193">你将需要使用 `Sql(string)` 方法来执行其中一些步骤。</span><span class="sxs-lookup"><span data-stu-id="8f243-193">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="8f243-194">有关更多详细信息，请参阅在 SQLite 文档中 [进行其他类型的表架构更改](https://sqlite.org/lang_altertable.html#otheralter) 。</span><span class="sxs-lookup"><span data-stu-id="8f243-194">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

### <a name="idempotent-script-limitations"></a><span data-ttu-id="8f243-195">幂等脚本限制</span><span class="sxs-lookup"><span data-stu-id="8f243-195">Idempotent script limitations</span></span>

<span data-ttu-id="8f243-196">与其他数据库不同，SQLite 不包含过程语言。</span><span class="sxs-lookup"><span data-stu-id="8f243-196">Unlike other databases, SQLite doesn't include a procedural language.</span></span> <span data-ttu-id="8f243-197">因此，无法生成幂等迁移脚本所需的 if-then 逻辑。</span><span class="sxs-lookup"><span data-stu-id="8f243-197">Because of this, there is no way to generate the if-then logic required by the idempotent migration scripts.</span></span>

<span data-ttu-id="8f243-198">如果知道最后一个应用到数据库的迁移，则可从迁移到最新迁移的脚本生成一个脚本。</span><span class="sxs-lookup"><span data-stu-id="8f243-198">If you know the last migration applied to a database, you can generate a script from that migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script CurrentMigration
```

<span data-ttu-id="8f243-199">否则，建议使用 `dotnet ef database update` 来应用迁移。</span><span class="sxs-lookup"><span data-stu-id="8f243-199">Otherwise, we recommend using `dotnet ef database update` to apply migrations.</span></span> <span data-ttu-id="8f243-200">从 EF Core 5.0 开始，在运行命令时，可以指定数据库文件。</span><span class="sxs-lookup"><span data-stu-id="8f243-200">Starting in EF Core 5.0, you can specify the database file when running the command.</span></span>

```dotnetcli
dotnet ef database update --connection "Data Source=My.db"
```

## <a name="see-also"></a><span data-ttu-id="8f243-201">另请参阅</span><span class="sxs-lookup"><span data-stu-id="8f243-201">See also</span></span>

* [<span data-ttu-id="8f243-202">Node.js 异步限制</span><span class="sxs-lookup"><span data-stu-id="8f243-202">Microsoft.Data.Sqlite Async Limitations</span></span>](/dotnet/standard/data/sqlite/async)
* [<span data-ttu-id="8f243-203">ADO.NET 限制</span><span class="sxs-lookup"><span data-stu-id="8f243-203">Microsoft.Data.Sqlite ADO.NET Limitations</span></span>](/dotnet/standard/data/sqlite/adonet-limitations)
