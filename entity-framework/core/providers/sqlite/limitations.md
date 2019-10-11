---
title: SQLite 数据库提供程序的限制的 EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 2f80dc195265787318ac4925dd937da45ffad011
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179773"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="0f026-102">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="0f026-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="0f026-103">SQLite 提供程序有很多迁移限制。</span><span class="sxs-lookup"><span data-stu-id="0f026-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="0f026-104">其中的大多数限制是由基础 SQLite 数据库引擎中的限制引起的，并不特定于 EF。</span><span class="sxs-lookup"><span data-stu-id="0f026-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="0f026-105">建模限制</span><span class="sxs-lookup"><span data-stu-id="0f026-105">Modeling limitations</span></span>

<span data-ttu-id="0f026-106">公共关系库（由实体框架关系数据库提供程序共享）定义了用于建模大多数关系数据库引擎所共有的概念的 Api。</span><span class="sxs-lookup"><span data-stu-id="0f026-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="0f026-107">SQLite 提供程序不支持其中几个概念。</span><span class="sxs-lookup"><span data-stu-id="0f026-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="0f026-108">架构</span><span class="sxs-lookup"><span data-stu-id="0f026-108">Schemas</span></span>
* <span data-ttu-id="0f026-109">序列</span><span class="sxs-lookup"><span data-stu-id="0f026-109">Sequences</span></span>
* <span data-ttu-id="0f026-110">计算列</span><span class="sxs-lookup"><span data-stu-id="0f026-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="0f026-111">查询限制</span><span class="sxs-lookup"><span data-stu-id="0f026-111">Query limitations</span></span>

<span data-ttu-id="0f026-112">SQLite 本身并不支持以下数据类型。</span><span class="sxs-lookup"><span data-stu-id="0f026-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="0f026-113">EF Core 可以读取和写入这些类型的值，也支持查询是否相等（`where e.Property == value`）。</span><span class="sxs-lookup"><span data-stu-id="0f026-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="0f026-114">但其他操作（如比较和排序）将需要对客户端进行评估。</span><span class="sxs-lookup"><span data-stu-id="0f026-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="0f026-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="0f026-115">DateTimeOffset</span></span>
* <span data-ttu-id="0f026-116">Decimal</span><span class="sxs-lookup"><span data-stu-id="0f026-116">Decimal</span></span>
* <span data-ttu-id="0f026-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="0f026-117">TimeSpan</span></span>
* <span data-ttu-id="0f026-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="0f026-118">UInt64</span></span>

<span data-ttu-id="0f026-119">建议使用 DateTime 值，而不是 `DateTimeOffset`。</span><span class="sxs-lookup"><span data-stu-id="0f026-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="0f026-120">处理多个时区时，建议在保存之前将值转换为 UTC，然后将其转换回适当的时区。</span><span class="sxs-lookup"><span data-stu-id="0f026-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="0f026-121">@No__t 0 类型提供了高级别的精度。</span><span class="sxs-lookup"><span data-stu-id="0f026-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="0f026-122">但是，如果不需要该级别的精度，则建议改为使用 double。</span><span class="sxs-lookup"><span data-stu-id="0f026-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="0f026-123">您可以使用[值转换器](../../modeling/value-conversions.md)在类中继续使用 decimal。</span><span class="sxs-lookup"><span data-stu-id="0f026-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="0f026-124">迁移限制</span><span class="sxs-lookup"><span data-stu-id="0f026-124">Migrations limitations</span></span>

<span data-ttu-id="0f026-125">SQLite 数据库引擎不支持许多其他关系数据库所支持的架构操作。</span><span class="sxs-lookup"><span data-stu-id="0f026-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="0f026-126">如果尝试将不受支持的操作之一应用于 SQLite 数据库，则会引发 `NotSupportedException`。</span><span class="sxs-lookup"><span data-stu-id="0f026-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="0f026-127">操作</span><span class="sxs-lookup"><span data-stu-id="0f026-127">Operation</span></span>            | <span data-ttu-id="0f026-128">受?</span><span class="sxs-lookup"><span data-stu-id="0f026-128">Supported?</span></span> | <span data-ttu-id="0f026-129">需要版本</span><span class="sxs-lookup"><span data-stu-id="0f026-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="0f026-130">AddColumn</span><span class="sxs-lookup"><span data-stu-id="0f026-130">AddColumn</span></span>            | <span data-ttu-id="0f026-131">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-131">✔</span></span>          | <span data-ttu-id="0f026-132">1.0</span><span class="sxs-lookup"><span data-stu-id="0f026-132">1.0</span></span>              |
| <span data-ttu-id="0f026-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="0f026-133">AddForeignKey</span></span>        | <span data-ttu-id="0f026-134">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-134">✗</span></span>          |                  |
| <span data-ttu-id="0f026-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="0f026-135">AddPrimaryKey</span></span>        | <span data-ttu-id="0f026-136">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-136">✗</span></span>          |                  |
| <span data-ttu-id="0f026-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="0f026-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="0f026-138">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-138">✗</span></span>          |                  |
| <span data-ttu-id="0f026-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="0f026-139">AlterColumn</span></span>          | <span data-ttu-id="0f026-140">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-140">✗</span></span>          |                  |
| <span data-ttu-id="0f026-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="0f026-141">CreateIndex</span></span>          | <span data-ttu-id="0f026-142">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-142">✔</span></span>          | <span data-ttu-id="0f026-143">1.0</span><span class="sxs-lookup"><span data-stu-id="0f026-143">1.0</span></span>              |
| <span data-ttu-id="0f026-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="0f026-144">CreateTable</span></span>          | <span data-ttu-id="0f026-145">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-145">✔</span></span>          | <span data-ttu-id="0f026-146">1.0</span><span class="sxs-lookup"><span data-stu-id="0f026-146">1.0</span></span>              |
| <span data-ttu-id="0f026-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="0f026-147">DropColumn</span></span>           | <span data-ttu-id="0f026-148">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-148">✗</span></span>          |                  |
| <span data-ttu-id="0f026-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="0f026-149">DropForeignKey</span></span>       | <span data-ttu-id="0f026-150">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-150">✗</span></span>          |                  |
| <span data-ttu-id="0f026-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="0f026-151">DropIndex</span></span>            | <span data-ttu-id="0f026-152">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-152">✔</span></span>          | <span data-ttu-id="0f026-153">1.0</span><span class="sxs-lookup"><span data-stu-id="0f026-153">1.0</span></span>              |
| <span data-ttu-id="0f026-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="0f026-154">DropPrimaryKey</span></span>       | <span data-ttu-id="0f026-155">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-155">✗</span></span>          |                  |
| <span data-ttu-id="0f026-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="0f026-156">DropTable</span></span>            | <span data-ttu-id="0f026-157">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-157">✔</span></span>          | <span data-ttu-id="0f026-158">1.0</span><span class="sxs-lookup"><span data-stu-id="0f026-158">1.0</span></span>              |
| <span data-ttu-id="0f026-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="0f026-159">DropUniqueConstraint</span></span> | <span data-ttu-id="0f026-160">✗</span><span class="sxs-lookup"><span data-stu-id="0f026-160">✗</span></span>          |                  |
| <span data-ttu-id="0f026-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="0f026-161">RenameColumn</span></span>         | <span data-ttu-id="0f026-162">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-162">✔</span></span>          | <span data-ttu-id="0f026-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="0f026-163">2.2.2</span></span>            |
| <span data-ttu-id="0f026-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="0f026-164">RenameIndex</span></span>          | <span data-ttu-id="0f026-165">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-165">✔</span></span>          | <span data-ttu-id="0f026-166">2.1</span><span class="sxs-lookup"><span data-stu-id="0f026-166">2.1</span></span>              |
| <span data-ttu-id="0f026-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="0f026-167">RenameTable</span></span>          | <span data-ttu-id="0f026-168">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-168">✔</span></span>          | <span data-ttu-id="0f026-169">1.0</span><span class="sxs-lookup"><span data-stu-id="0f026-169">1.0</span></span>              |
| <span data-ttu-id="0f026-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="0f026-170">EnsureSchema</span></span>         | <span data-ttu-id="0f026-171">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="0f026-171">✔ (no-op)</span></span>  | <span data-ttu-id="0f026-172">2.0</span><span class="sxs-lookup"><span data-stu-id="0f026-172">2.0</span></span>              |
| <span data-ttu-id="0f026-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="0f026-173">DropSchema</span></span>           | <span data-ttu-id="0f026-174">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="0f026-174">✔ (no-op)</span></span>  | <span data-ttu-id="0f026-175">2.0</span><span class="sxs-lookup"><span data-stu-id="0f026-175">2.0</span></span>              |
| <span data-ttu-id="0f026-176">Insert</span><span class="sxs-lookup"><span data-stu-id="0f026-176">Insert</span></span>               | <span data-ttu-id="0f026-177">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-177">✔</span></span>          | <span data-ttu-id="0f026-178">2.0</span><span class="sxs-lookup"><span data-stu-id="0f026-178">2.0</span></span>              |
| <span data-ttu-id="0f026-179">Update</span><span class="sxs-lookup"><span data-stu-id="0f026-179">Update</span></span>               | <span data-ttu-id="0f026-180">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-180">✔</span></span>          | <span data-ttu-id="0f026-181">2.0</span><span class="sxs-lookup"><span data-stu-id="0f026-181">2.0</span></span>              |
| <span data-ttu-id="0f026-182">DELETE</span><span class="sxs-lookup"><span data-stu-id="0f026-182">Delete</span></span>               | <span data-ttu-id="0f026-183">✔</span><span class="sxs-lookup"><span data-stu-id="0f026-183">✔</span></span>          | <span data-ttu-id="0f026-184">2.0</span><span class="sxs-lookup"><span data-stu-id="0f026-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="0f026-185">迁移限制解决方法</span><span class="sxs-lookup"><span data-stu-id="0f026-185">Migrations limitations workaround</span></span>

<span data-ttu-id="0f026-186">通过在迁移中手动编写代码来执行表重新生成，可以解决其中一些限制。</span><span class="sxs-lookup"><span data-stu-id="0f026-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="0f026-187">表重新生成包括重命名现有表、创建新表、将数据复制到新表和删除旧表。</span><span class="sxs-lookup"><span data-stu-id="0f026-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="0f026-188">需要使用 `Sql(string)` 方法执行其中一些步骤。</span><span class="sxs-lookup"><span data-stu-id="0f026-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="0f026-189">有关更多详细信息，请参阅在 SQLite 文档中[进行其他类型的表架构更改](https://sqlite.org/lang_altertable.html#otheralter)。</span><span class="sxs-lookup"><span data-stu-id="0f026-189">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="0f026-190">将来，EF 可以通过使用表中的 "重新生成" 方法来支持这些操作。</span><span class="sxs-lookup"><span data-stu-id="0f026-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="0f026-191">你可以[在 GitHub 项目上跟踪此功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="0f026-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
