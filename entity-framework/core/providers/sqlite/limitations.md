---
title: SQLite 数据库提供程序的限制的 EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: eaa7d5b1496172e4f3821433a1cd098ee7e8b737
ms.sourcegitcommit: 9bd64a1a71b7f7aeb044aeecc7c4785b57db1ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394804"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a><span data-ttu-id="03eb2-102">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="03eb2-102">SQLite EF Core Database Provider Limitations</span></span>

<span data-ttu-id="03eb2-103">SQLite 提供程序具有许多迁移限制。</span><span class="sxs-lookup"><span data-stu-id="03eb2-103">The SQLite provider has a number of migrations limitations.</span></span> <span data-ttu-id="03eb2-104">其中大多数限制是基础的 SQLite 数据库引擎中的限制的结果并不特定于 EF。</span><span class="sxs-lookup"><span data-stu-id="03eb2-104">Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.</span></span>

## <a name="modeling-limitations"></a><span data-ttu-id="03eb2-105">建模限制</span><span class="sxs-lookup"><span data-stu-id="03eb2-105">Modeling limitations</span></span>

<span data-ttu-id="03eb2-106">（由实体框架关系数据库提供程序共享） 的公共关系库定义建模概念所共有的大多数关系数据库引擎的 Api。</span><span class="sxs-lookup"><span data-stu-id="03eb2-106">The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines.</span></span> <span data-ttu-id="03eb2-107">SQLite 提供程序不支持几个这些概念。</span><span class="sxs-lookup"><span data-stu-id="03eb2-107">A couple of these concepts are not supported by the SQLite provider.</span></span>

* <span data-ttu-id="03eb2-108">架构</span><span class="sxs-lookup"><span data-stu-id="03eb2-108">Schemas</span></span>
* <span data-ttu-id="03eb2-109">序列</span><span class="sxs-lookup"><span data-stu-id="03eb2-109">Sequences</span></span>
* <span data-ttu-id="03eb2-110">计算的列</span><span class="sxs-lookup"><span data-stu-id="03eb2-110">Computed columns</span></span>

## <a name="query-limitations"></a><span data-ttu-id="03eb2-111">查询限制</span><span class="sxs-lookup"><span data-stu-id="03eb2-111">Query limitations</span></span>

<span data-ttu-id="03eb2-112">SQLite 不以本机方式支持以下数据类型。</span><span class="sxs-lookup"><span data-stu-id="03eb2-112">SQLite doesn't natively support the following data types.</span></span> <span data-ttu-id="03eb2-113">EF Core 可以读取和写入这些类型和查询是否相等的值 (`where e.Property == value`)，它还支持。</span><span class="sxs-lookup"><span data-stu-id="03eb2-113">EF Core can read and write values of these types, and querying for equality (`where e.Property == value`) is also support.</span></span> <span data-ttu-id="03eb2-114">其他操作，但是，如比较和排序将需要在客户端上的评估。</span><span class="sxs-lookup"><span data-stu-id="03eb2-114">Other operations, however, like comparison and ordering will require evaluation on the client.</span></span>

* <span data-ttu-id="03eb2-115">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="03eb2-115">DateTimeOffset</span></span>
* <span data-ttu-id="03eb2-116">十进制</span><span class="sxs-lookup"><span data-stu-id="03eb2-116">Decimal</span></span>
* <span data-ttu-id="03eb2-117">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="03eb2-117">TimeSpan</span></span>
* <span data-ttu-id="03eb2-118">UInt64</span><span class="sxs-lookup"><span data-stu-id="03eb2-118">UInt64</span></span>

<span data-ttu-id="03eb2-119">而不是`DateTimeOffset`，我们建议使用日期时间值。</span><span class="sxs-lookup"><span data-stu-id="03eb2-119">Instead of `DateTimeOffset`, we recommend using DateTime values.</span></span> <span data-ttu-id="03eb2-120">在处理多个时区，我们建议将值转换为 UTC 之前保存并将转换回相应的时区。</span><span class="sxs-lookup"><span data-stu-id="03eb2-120">When handling multiple time zones, we recommend converting the values to UTC before saving and then converting back to the appropriate time zone.</span></span>

<span data-ttu-id="03eb2-121">`Decimal`类型提供了高的精度。</span><span class="sxs-lookup"><span data-stu-id="03eb2-121">The `Decimal` type provides a high level of precision.</span></span> <span data-ttu-id="03eb2-122">如果不需要该级别的精度，但是，建议改为使用双精度。</span><span class="sxs-lookup"><span data-stu-id="03eb2-122">If you don't need that level of precision, however, we recommend using double instead.</span></span> <span data-ttu-id="03eb2-123">可以使用[值转换器](../../modeling/value-conversions.md)若要继续使用您的类中的小数。</span><span class="sxs-lookup"><span data-stu-id="03eb2-123">You can use a [value converter](../../modeling/value-conversions.md) to continue using decimal in your classes.</span></span>

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a><span data-ttu-id="03eb2-124">迁移限制</span><span class="sxs-lookup"><span data-stu-id="03eb2-124">Migrations limitations</span></span>

<span data-ttu-id="03eb2-125">SQLite 数据库引擎不支持多个架构操作支持的其他关系数据库的大多数。</span><span class="sxs-lookup"><span data-stu-id="03eb2-125">The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases.</span></span> <span data-ttu-id="03eb2-126">如果你尝试将其中一个不受支持的操作应用到 SQLite 数据库则`NotSupportedException`将引发。</span><span class="sxs-lookup"><span data-stu-id="03eb2-126">If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.</span></span>

| <span data-ttu-id="03eb2-127">操作</span><span class="sxs-lookup"><span data-stu-id="03eb2-127">Operation</span></span>            | <span data-ttu-id="03eb2-128">支持？</span><span class="sxs-lookup"><span data-stu-id="03eb2-128">Supported?</span></span> | <span data-ttu-id="03eb2-129">需要版本</span><span class="sxs-lookup"><span data-stu-id="03eb2-129">Requires version</span></span> |
|:---------------------|:-----------|:-----------------|
| <span data-ttu-id="03eb2-130">AddColumn</span><span class="sxs-lookup"><span data-stu-id="03eb2-130">AddColumn</span></span>            | <span data-ttu-id="03eb2-131">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-131">✔</span></span>          | <span data-ttu-id="03eb2-132">1.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-132">1.0</span></span>              |
| <span data-ttu-id="03eb2-133">AddForeignKey</span><span class="sxs-lookup"><span data-stu-id="03eb2-133">AddForeignKey</span></span>        | <span data-ttu-id="03eb2-134">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-134">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-135">AddPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="03eb2-135">AddPrimaryKey</span></span>        | <span data-ttu-id="03eb2-136">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-136">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-137">AddUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="03eb2-137">AddUniqueConstraint</span></span>  | <span data-ttu-id="03eb2-138">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-138">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-139">AlterColumn</span><span class="sxs-lookup"><span data-stu-id="03eb2-139">AlterColumn</span></span>          | <span data-ttu-id="03eb2-140">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-140">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-141">CreateIndex</span><span class="sxs-lookup"><span data-stu-id="03eb2-141">CreateIndex</span></span>          | <span data-ttu-id="03eb2-142">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-142">✔</span></span>          | <span data-ttu-id="03eb2-143">1.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-143">1.0</span></span>              |
| <span data-ttu-id="03eb2-144">CreateTable</span><span class="sxs-lookup"><span data-stu-id="03eb2-144">CreateTable</span></span>          | <span data-ttu-id="03eb2-145">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-145">✔</span></span>          | <span data-ttu-id="03eb2-146">1.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-146">1.0</span></span>              |
| <span data-ttu-id="03eb2-147">DropColumn</span><span class="sxs-lookup"><span data-stu-id="03eb2-147">DropColumn</span></span>           | <span data-ttu-id="03eb2-148">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-148">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-149">DropForeignKey</span><span class="sxs-lookup"><span data-stu-id="03eb2-149">DropForeignKey</span></span>       | <span data-ttu-id="03eb2-150">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-150">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-151">DropIndex</span><span class="sxs-lookup"><span data-stu-id="03eb2-151">DropIndex</span></span>            | <span data-ttu-id="03eb2-152">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-152">✔</span></span>          | <span data-ttu-id="03eb2-153">1.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-153">1.0</span></span>              |
| <span data-ttu-id="03eb2-154">DropPrimaryKey</span><span class="sxs-lookup"><span data-stu-id="03eb2-154">DropPrimaryKey</span></span>       | <span data-ttu-id="03eb2-155">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-155">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-156">DropTable</span><span class="sxs-lookup"><span data-stu-id="03eb2-156">DropTable</span></span>            | <span data-ttu-id="03eb2-157">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-157">✔</span></span>          | <span data-ttu-id="03eb2-158">1.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-158">1.0</span></span>              |
| <span data-ttu-id="03eb2-159">DropUniqueConstraint</span><span class="sxs-lookup"><span data-stu-id="03eb2-159">DropUniqueConstraint</span></span> | <span data-ttu-id="03eb2-160">✗</span><span class="sxs-lookup"><span data-stu-id="03eb2-160">✗</span></span>          |                  |
| <span data-ttu-id="03eb2-161">RenameColumn</span><span class="sxs-lookup"><span data-stu-id="03eb2-161">RenameColumn</span></span>         | <span data-ttu-id="03eb2-162">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-162">✔</span></span>          | <span data-ttu-id="03eb2-163">2.2.2</span><span class="sxs-lookup"><span data-stu-id="03eb2-163">2.2.2</span></span>            |
| <span data-ttu-id="03eb2-164">RenameIndex</span><span class="sxs-lookup"><span data-stu-id="03eb2-164">RenameIndex</span></span>          | <span data-ttu-id="03eb2-165">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-165">✔</span></span>          | <span data-ttu-id="03eb2-166">2.1</span><span class="sxs-lookup"><span data-stu-id="03eb2-166">2.1</span></span>              |
| <span data-ttu-id="03eb2-167">RenameTable</span><span class="sxs-lookup"><span data-stu-id="03eb2-167">RenameTable</span></span>          | <span data-ttu-id="03eb2-168">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-168">✔</span></span>          | <span data-ttu-id="03eb2-169">1.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-169">1.0</span></span>              |
| <span data-ttu-id="03eb2-170">EnsureSchema</span><span class="sxs-lookup"><span data-stu-id="03eb2-170">EnsureSchema</span></span>         | <span data-ttu-id="03eb2-171">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="03eb2-171">✔ (no-op)</span></span>  | <span data-ttu-id="03eb2-172">2.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-172">2.0</span></span>              |
| <span data-ttu-id="03eb2-173">DropSchema</span><span class="sxs-lookup"><span data-stu-id="03eb2-173">DropSchema</span></span>           | <span data-ttu-id="03eb2-174">✔ (no-op)</span><span class="sxs-lookup"><span data-stu-id="03eb2-174">✔ (no-op)</span></span>  | <span data-ttu-id="03eb2-175">2.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-175">2.0</span></span>              |
| <span data-ttu-id="03eb2-176">Insert</span><span class="sxs-lookup"><span data-stu-id="03eb2-176">Insert</span></span>               | <span data-ttu-id="03eb2-177">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-177">✔</span></span>          | <span data-ttu-id="03eb2-178">2.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-178">2.0</span></span>              |
| <span data-ttu-id="03eb2-179">更新</span><span class="sxs-lookup"><span data-stu-id="03eb2-179">Update</span></span>               | <span data-ttu-id="03eb2-180">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-180">✔</span></span>          | <span data-ttu-id="03eb2-181">2.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-181">2.0</span></span>              |
| <span data-ttu-id="03eb2-182">删除</span><span class="sxs-lookup"><span data-stu-id="03eb2-182">Delete</span></span>               | <span data-ttu-id="03eb2-183">✔</span><span class="sxs-lookup"><span data-stu-id="03eb2-183">✔</span></span>          | <span data-ttu-id="03eb2-184">2.0</span><span class="sxs-lookup"><span data-stu-id="03eb2-184">2.0</span></span>              |

## <a name="migrations-limitations-workaround"></a><span data-ttu-id="03eb2-185">迁移限制的解决方法</span><span class="sxs-lookup"><span data-stu-id="03eb2-185">Migrations limitations workaround</span></span>

<span data-ttu-id="03eb2-186">可以解决一些通过手动编写代码在你迁移执行表中的这些限制的重新生成。</span><span class="sxs-lookup"><span data-stu-id="03eb2-186">You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild.</span></span> <span data-ttu-id="03eb2-187">表重新生成包括重命名现有表、创建新表、将数据复制到新表和删除旧表。</span><span class="sxs-lookup"><span data-stu-id="03eb2-187">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="03eb2-188">将需要使用`Sql(string)`方法来执行这些步骤中的一部分。</span><span class="sxs-lookup"><span data-stu-id="03eb2-188">You will need to use the `Sql(string)` method to perform some of these steps.</span></span>

<span data-ttu-id="03eb2-189">请参阅[进行其他类型的表架构更改](http://sqlite.org/lang_altertable.html#otheralter)SQLite 文档了解详细信息中。</span><span class="sxs-lookup"><span data-stu-id="03eb2-189">See [Making Other Kinds Of Table Schema Changes](http://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.</span></span>

<span data-ttu-id="03eb2-190">将来，EF 可能支持某些操作使用在后台的表重新生成方法。</span><span class="sxs-lookup"><span data-stu-id="03eb2-190">In the future, EF may support some of these operations by using the table rebuild approach under the covers.</span></span> <span data-ttu-id="03eb2-191">你可以[跟踪此功能在我们的 GitHub 项目](https://github.com/aspnet/EntityFrameworkCore/issues/329)。</span><span class="sxs-lookup"><span data-stu-id="03eb2-191">You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFrameworkCore/issues/329).</span></span>
