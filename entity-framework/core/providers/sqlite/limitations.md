---
title: SQLite 数据库提供程序-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672776"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="19bc6-102">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="19bc6-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="19bc6-103">SQLite 提供程序有很多迁移限制。</span><span class="sxs-lookup"><span data-stu-id="19bc6-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="19bc6-104">其中的大多数限制是由基础 SQLite 数据库引擎中的限制引起的，并不特定于 EF。</span><span class="sxs-lookup"><span data-stu-id="19bc6-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="19bc6-105">建模限制</span><span class="sxs-lookup"><span data-stu-id="19bc6-105">Modeling limitations</span></span>

<span data-ttu-id="19bc6-106">公共关系库（由实体框架关系数据库提供程序共享）定义了用于建模大多数关系数据库引擎所共有的概念的 Api。</span><span class="sxs-lookup"><span data-stu-id="19bc6-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="19bc6-107">SQLite 提供程序不支持其中几个概念。</span><span class="sxs-lookup"><span data-stu-id="19bc6-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="19bc6-108">架构</span><span class="sxs-lookup"><span data-stu-id="19bc6-108">Schemas</span></span>
* <span data-ttu-id="19bc6-109">序列</span><span class="sxs-lookup"><span data-stu-id="19bc6-109">Sequences</span></span>

## <a name="query-limitations"></a><span data-ttu-id="19bc6-110">查询限制</span><span class="sxs-lookup"><span data-stu-id="19bc6-110">Query limitations</span></span>

<span data-ttu-id="19bc6-111">SQLite 本身并不支持以下数据类型。</span><span class="sxs-lookup"><span data-stu-id="19bc6-111">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="19bc6-112">EF Core 可以读取和写入这些类型的值，也支持查询是否相等（ `where e.Property == value` ）。</span><span class="sxs-lookup"><span data-stu-id="19bc6-112">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also supported.</span></span> <span data-ttu-id="19bc6-113">但其他操作（如比较和排序）将需要对客户端进行评估。</span><span class="sxs-lookup"><span data-stu-id="19bc6-113">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="19bc6-114">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="19bc6-114">DateTimeOffset</span></span>
* <span data-ttu-id="19bc6-115">小数</span><span class="sxs-lookup"><span data-stu-id="19bc6-115">Decimal</span></span>
* <span data-ttu-id="19bc6-116">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="19bc6-116">TimeSpan</span></span>
* <span data-ttu-id="19bc6-117">UInt64</span><span class="sxs-lookup"><span data-stu-id="19bc6-117">UInt64</span></span>

<span data-ttu-id="19bc6-118">`DateTimeOffset`建议使用 DateTime 值，而不是。</span><span class="sxs-lookup"><span data-stu-id="19bc6-118">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="19bc6-119">处理多个时区时，建议在保存之前将值转换为 UTC，然后将其转换回适当的时区。</span><span class="sxs-lookup"><span data-stu-id="19bc6-119">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="19bc6-120">`Decimal`类型提供了较高的精度。</span><span class="sxs-lookup"><span data-stu-id="19bc6-120">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="19bc6-121">但是，如果不需要该级别的精度，则建议改为使用 double。</span><span class="sxs-lookup"><span data-stu-id="19bc6-121">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="19bc6-122">您可以使用[值转换器](../../modeling/value-conversions.md)在类中继续使用 decimal。</span><span class="sxs-lookup"><span data-stu-id="19bc6-122">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="19bc6-123">迁移限制</span><span class="sxs-lookup"><span data-stu-id="19bc6-123">Migrations limitations</span></span>

<span data-ttu-id="19bc6-124">SQLite 数据库引擎不支持许多其他关系数据库所支持的架构操作。</span><span class="sxs-lookup"><span data-stu-id="19bc6-124">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="19bc6-125">如果尝试将不受支持的操作之一应用于 SQLite 数据库，则 `NotSupportedException` 会引发。</span><span class="sxs-lookup"><span data-stu-id="19bc6-125">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="19bc6-126">操作</span><span class="sxs-lookup"><span data-stu-id="19bc6-126">Operation</span></span>            | <span data-ttu-id="19bc6-127">支持？</span><span class="sxs-lookup"><span data-stu-id="19bc6-127">Supported?</span></span> | <span data-ttu-id="19bc6-128">需要版本</span><span class="sxs-lookup"><span data-stu-id="19bc6-128">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="19bc6-129">AddColumn</span><span class="sxs-lookup"><span data-stu-id="19bc6-129">AddColumn</span></span>            | <span data-ttu-id="19bc6-130">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-130">✔</span></span>          | <span data-ttu-id="19bc6-131">1.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-131">1.0</span></span>              |
| <span data-ttu-id="19bc6-132">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="19bc6-132">AddForeignKey</span></span>        | <span data-ttu-id="19bc6-133">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-133">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-134">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="19bc6-134">AddPrimaryKey</span></span>        | <span data-ttu-id="19bc6-135">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-135">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-136">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="19bc6-136">AddUniqueConstraint</span></span>  | <span data-ttu-id="19bc6-137">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-137">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-138">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="19bc6-138">AlterColumn</span></span>          | <span data-ttu-id="19bc6-139">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-139">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-140">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="19bc6-140">CreateIndex</span></span>          | <span data-ttu-id="19bc6-141">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-141">✔</span></span>          | <span data-ttu-id="19bc6-142">1.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-142">1.0</span></span>              |
| <span data-ttu-id="19bc6-143">CreateTable</span><span class="sxs-lookup"><span data-stu-id="19bc6-143">CreateTable</span></span>          | <span data-ttu-id="19bc6-144">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-144">✔</span></span>          | <span data-ttu-id="19bc6-145">1.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-145">1.0</span></span>              |
| <span data-ttu-id="19bc6-146">DropColumn</span><span class="sxs-lookup"><span data-stu-id="19bc6-146">DropColumn</span></span>           | <span data-ttu-id="19bc6-147">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-147">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-148">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="19bc6-148">DropForeignKey</span></span>       | <span data-ttu-id="19bc6-149">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-149">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-150">DropIndex</span><span class="sxs-lookup"><span data-stu-id="19bc6-150">DropIndex</span></span>            | <span data-ttu-id="19bc6-151">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-151">✔</span></span>          | <span data-ttu-id="19bc6-152">1.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-152">1.0</span></span>              |
| <span data-ttu-id="19bc6-153">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="19bc6-153">DropPrimaryKey</span></span>       | <span data-ttu-id="19bc6-154">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-154">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-155">DropTable</span><span class="sxs-lookup"><span data-stu-id="19bc6-155">DropTable</span></span>            | <span data-ttu-id="19bc6-156">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-156">✔</span></span>          | <span data-ttu-id="19bc6-157">1.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-157">1.0</span></span>              |
| <span data-ttu-id="19bc6-158">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="19bc6-158">DropUniqueConstraint</span></span> | <span data-ttu-id="19bc6-159">✗</span><span class="sxs-lookup"><span data-stu-id="19bc6-159">✗</span></span>          |                  |
| <span data-ttu-id="19bc6-160">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="19bc6-160">RenameColumn</span></span>         | <span data-ttu-id="19bc6-161">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-161">✔</span></span>          | <span data-ttu-id="19bc6-162">2.2.2</span><span class="sxs-lookup"><span data-stu-id="19bc6-162">2.2.2</span></span>            |
| <span data-ttu-id="19bc6-163">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="19bc6-163">RenameIndex</span></span>          | <span data-ttu-id="19bc6-164">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-164">✔</span></span>          | <span data-ttu-id="19bc6-165">2.1</span><span class="sxs-lookup"><span data-stu-id="19bc6-165">2.1</span></span>              |
| <span data-ttu-id="19bc6-166">RenameTable</span><span class="sxs-lookup"><span data-stu-id="19bc6-166">RenameTable</span></span>          | <span data-ttu-id="19bc6-167">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-167">✔</span></span>          | <span data-ttu-id="19bc6-168">1.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-168">1.0</span></span>              |
| <span data-ttu-id="19bc6-169">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="19bc6-169">EnsureSchema</span></span>         | <span data-ttu-id="19bc6-170">✔（无操作）</span><span class="sxs-lookup"><span data-stu-id="19bc6-170">✔ (no-op)</span></span>  | <span data-ttu-id="19bc6-171">2.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-171">2.0</span></span>              |
| <span data-ttu-id="19bc6-172">DropSchema</span><span class="sxs-lookup"><span data-stu-id="19bc6-172">DropSchema</span></span>           | <span data-ttu-id="19bc6-173">✔（无操作）</span><span class="sxs-lookup"><span data-stu-id="19bc6-173">✔ (no-op)</span></span>  | <span data-ttu-id="19bc6-174">2.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-174">2.0</span></span>              |
| <span data-ttu-id="19bc6-175">插入</span><span class="sxs-lookup"><span data-stu-id="19bc6-175">Insert</span></span>               | <span data-ttu-id="19bc6-176">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-176">✔</span></span>          | <span data-ttu-id="19bc6-177">2.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-177">2.0</span></span>              |
| <span data-ttu-id="19bc6-178">更新</span><span class="sxs-lookup"><span data-stu-id="19bc6-178">Update</span></span>               | <span data-ttu-id="19bc6-179">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-179">✔</span></span>          | <span data-ttu-id="19bc6-180">2.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-180">2.0</span></span>              |
| <span data-ttu-id="19bc6-181">删除</span><span class="sxs-lookup"><span data-stu-id="19bc6-181">Delete</span></span>               | <span data-ttu-id="19bc6-182">✔</span><span class="sxs-lookup"><span data-stu-id="19bc6-182">✔</span></span>          | <span data-ttu-id="19bc6-183">2.0</span><span class="sxs-lookup"><span data-stu-id="19bc6-183">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="19bc6-184">迁移限制解决方法</span><span class="sxs-lookup"><span data-stu-id="19bc6-184">Migrations limitations workaround</span></span>

<span data-ttu-id="19bc6-185">通过在迁移中手动编写代码来执行表重新生成，可以解决其中一些限制。</span><span class="sxs-lookup"><span data-stu-id="19bc6-185">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="19bc6-186">表重新生成包括重命名现有表、创建新表、将数据复制到新表和删除旧表。</span><span class="sxs-lookup"><span data-stu-id="19bc6-186">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="19bc6-187">你将需要使用 `Sql(string)` 方法来执行其中一些步骤。</span><span class="sxs-lookup"><span data-stu-id="19bc6-187">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="19bc6-188">有关更多详细信息，请参阅在 SQLite 文档中[进行其他类型的表架构更改](https://sqlite.org/lang_altertable.html#otheralter)。</span><span class="sxs-lookup"><span data-stu-id="19bc6-188">See [Making Other Kinds Of Table Schema Changes](https://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="19bc6-189">将来，EF 可以通过使用表中的 "重新生成" 方法来支持这些操作。</span><span class="sxs-lookup"><span data-stu-id="19bc6-189">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="19bc6-190">你可以[在 GitHub 项目上跟踪此功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="19bc6-190">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
