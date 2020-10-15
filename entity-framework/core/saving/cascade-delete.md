---
title: 级联删除 - EF Core
description: 在删除主体实体时为相关实体配置删除行为
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/cascade-delete
ms.openlocfilehash: 037b018c669da76a70f134e3991ad22b36917920
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063603"
---
# <a name="cascade-delete"></a><span data-ttu-id="2eecc-103">级联删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-103">Cascade Delete</span></span>

<span data-ttu-id="2eecc-104">级联删除通常在数据库术语中使用，用于描述允许删除某行以自动触发删除相关行的特性。</span><span class="sxs-lookup"><span data-stu-id="2eecc-104">Cascade delete is commonly used in database terminology to describe a characteristic that allows the deletion of a row to automatically trigger the deletion of related rows.</span></span> <span data-ttu-id="2eecc-105">EF Core 删除行为还介绍了一个密切相关的概念，即子实体与父实体的关系已断开时自动删除该子实体，这通常称为“删除孤立项”。</span><span class="sxs-lookup"><span data-stu-id="2eecc-105">A closely related concept also covered by EF Core delete behaviors is the automatic deletion of a child entity when it's relationship to a parent has been severed--this is commonly known as "deleting orphans".</span></span>

<span data-ttu-id="2eecc-106">EF Core 实现多种不同的删除行为，并允许配置各个关系的删除行为。</span><span class="sxs-lookup"><span data-stu-id="2eecc-106">EF Core implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships.</span></span> <span data-ttu-id="2eecc-107">EF Core 还实现基于[关系的需求](xref:core/modeling/relationships#required-and-optional-relationships)为每个关系自动配置有用的默认删除行为的约定。</span><span class="sxs-lookup"><span data-stu-id="2eecc-107">EF Core also implements conventions that automatically configure useful default delete behaviors for each relationship based on the [requiredness of the relationship](xref:core/modeling/relationships#required-and-optional-relationships).</span></span>

## <a name="delete-behaviors"></a><span data-ttu-id="2eecc-108">删除行为</span><span class="sxs-lookup"><span data-stu-id="2eecc-108">Delete behaviors</span></span>

<span data-ttu-id="2eecc-109">删除行为在 *DeleteBehavior* 枚举器类型中定义，并且可以传递到 *OnDelete* Fluent API 来控制是主体/父实体的删除还是依赖实体/子实体关系的断开会对依赖实体/子实体产生副作用。</span><span class="sxs-lookup"><span data-stu-id="2eecc-109">Delete behaviors are defined in the *DeleteBehavior* enumerator type and can be passed to the *OnDelete* fluent API to control whether the deletion of a principal/parent entity or the severing of the relationship to dependent/child entities should have a side effect on the dependent/child entities.</span></span>

<span data-ttu-id="2eecc-110">删除主体/父实体或断开与子实体的关系时有三个 EF 可执行的操作：</span><span class="sxs-lookup"><span data-stu-id="2eecc-110">There are three actions EF can take when a principal/parent entity is deleted or the relationship to the child is severed:</span></span>

* <span data-ttu-id="2eecc-111">可以删除子项/依赖项</span><span class="sxs-lookup"><span data-stu-id="2eecc-111">The child/dependent can be deleted</span></span>
* <span data-ttu-id="2eecc-112">子项的外键值可以设置为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-112">The child's foreign key values can be set to null</span></span>
* <span data-ttu-id="2eecc-113">子项保持不变</span><span class="sxs-lookup"><span data-stu-id="2eecc-113">The child remains unchanged</span></span>

> [!NOTE]
> <span data-ttu-id="2eecc-114">仅当使用 EF Core 删除主体且将依赖实体加载到内存中（即对于跟踪的依赖项）时才应用 EF Core 模型中配置的删除行为。</span><span class="sxs-lookup"><span data-stu-id="2eecc-114">The delete behavior configured in the EF Core model is only applied when the principal entity is deleted using EF Core and the dependent entities are loaded in memory (that is, for tracked dependents).</span></span> <span data-ttu-id="2eecc-115">需要在数据库中设置相应的级联行为以确保未由上下文跟踪的数据已应用必要的操作。</span><span class="sxs-lookup"><span data-stu-id="2eecc-115">A corresponding cascade behavior needs to be setup in the database to ensure data that is not being tracked by the context has the necessary action applied.</span></span> <span data-ttu-id="2eecc-116">如果使用 EF Core 创建数据库，将为你设置此级联行为。</span><span class="sxs-lookup"><span data-stu-id="2eecc-116">If you use EF Core to create the database, this cascade behavior will be setup for you.</span></span>

<span data-ttu-id="2eecc-117">对于上述第二个操作，如果外键不可以为 null，则将外键值设置为 null 是无效的。</span><span class="sxs-lookup"><span data-stu-id="2eecc-117">For the second action above, setting a foreign key value to null is not valid if foreign key is not nullable.</span></span> <span data-ttu-id="2eecc-118">（不可为 null 的外键相当于必需关系。）在这些情况下，EF Core 会跟踪外键属性是否已被标记为 null，直到调用 SaveChanges，此时会引发异常，因为无法将更改永久保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="2eecc-118">(A non-nullable foreign key is equivalent to a required relationship.) In these cases, EF Core tracks that the foreign key property has been marked as null until SaveChanges is called, at which time an exception is thrown because the change cannot be persisted to the database.</span></span> <span data-ttu-id="2eecc-119">这类似于从数据库中获取约束冲突。</span><span class="sxs-lookup"><span data-stu-id="2eecc-119">This is similar to getting a constraint violation from the database.</span></span>

<span data-ttu-id="2eecc-120">有四个删除行为，如下表中列出。</span><span class="sxs-lookup"><span data-stu-id="2eecc-120">There are four delete behaviors, as listed in the tables below.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="2eecc-121">可选关系</span><span class="sxs-lookup"><span data-stu-id="2eecc-121">Optional relationships</span></span>

<span data-ttu-id="2eecc-122">对于可选关系（可以为 null 的外键），_可以_保存 null 外键值，从而产生以下影响：</span><span class="sxs-lookup"><span data-stu-id="2eecc-122">For optional relationships (nullable foreign key) it _is_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="2eecc-123">行为名称</span><span class="sxs-lookup"><span data-stu-id="2eecc-123">Behavior Name</span></span>               | <span data-ttu-id="2eecc-124">对内存中的依赖项/子项的影响</span><span class="sxs-lookup"><span data-stu-id="2eecc-124">Effect on dependent/child in memory</span></span>    | <span data-ttu-id="2eecc-125">对数据库中的依赖项/子项的影响</span><span class="sxs-lookup"><span data-stu-id="2eecc-125">Effect on dependent/child in database</span></span>  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| <span data-ttu-id="2eecc-126">**Cascade**</span><span class="sxs-lookup"><span data-stu-id="2eecc-126">**Cascade**</span></span>                 | <span data-ttu-id="2eecc-127">删除实体</span><span class="sxs-lookup"><span data-stu-id="2eecc-127">Entities are deleted</span></span>                   | <span data-ttu-id="2eecc-128">删除实体</span><span class="sxs-lookup"><span data-stu-id="2eecc-128">Entities are deleted</span></span>                   |
| <span data-ttu-id="2eecc-129">**ClientSetNull**（默认）</span><span class="sxs-lookup"><span data-stu-id="2eecc-129">**ClientSetNull** (Default)</span></span> | <span data-ttu-id="2eecc-130">外键属性设置为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-130">Foreign key properties are set to null</span></span> | <span data-ttu-id="2eecc-131">无</span><span class="sxs-lookup"><span data-stu-id="2eecc-131">None</span></span>                                   |
| <span data-ttu-id="2eecc-132">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="2eecc-132">**SetNull**</span></span>                 | <span data-ttu-id="2eecc-133">外键属性设置为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-133">Foreign key properties are set to null</span></span> | <span data-ttu-id="2eecc-134">外键属性设置为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-134">Foreign key properties are set to null</span></span> |
| <span data-ttu-id="2eecc-135">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="2eecc-135">**Restrict**</span></span>                | <span data-ttu-id="2eecc-136">None</span><span class="sxs-lookup"><span data-stu-id="2eecc-136">None</span></span>                                   | <span data-ttu-id="2eecc-137">无</span><span class="sxs-lookup"><span data-stu-id="2eecc-137">None</span></span>                                   |

### <a name="required-relationships"></a><span data-ttu-id="2eecc-138">必选关系</span><span class="sxs-lookup"><span data-stu-id="2eecc-138">Required relationships</span></span>

<span data-ttu-id="2eecc-139">对于必选关系（不可为 null 的外键），_不可以_保存 null 外键值，从而产生以下影响：</span><span class="sxs-lookup"><span data-stu-id="2eecc-139">For required relationships (non-nullable foreign key) it is _not_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="2eecc-140">行为名称</span><span class="sxs-lookup"><span data-stu-id="2eecc-140">Behavior Name</span></span>         | <span data-ttu-id="2eecc-141">对内存中的依赖项/子项的影响</span><span class="sxs-lookup"><span data-stu-id="2eecc-141">Effect on dependent/child in memory</span></span> | <span data-ttu-id="2eecc-142">对数据库中的依赖项/子项的影响</span><span class="sxs-lookup"><span data-stu-id="2eecc-142">Effect on dependent/child in database</span></span> |
|:----------------------|:------------------------------------|:--------------------------------------|
| <span data-ttu-id="2eecc-143">**Cascade**（默认）</span><span class="sxs-lookup"><span data-stu-id="2eecc-143">**Cascade** (Default)</span></span> | <span data-ttu-id="2eecc-144">删除实体</span><span class="sxs-lookup"><span data-stu-id="2eecc-144">Entities are deleted</span></span>                | <span data-ttu-id="2eecc-145">删除实体</span><span class="sxs-lookup"><span data-stu-id="2eecc-145">Entities are deleted</span></span>                  |
| <span data-ttu-id="2eecc-146">**ClientSetNull**</span><span class="sxs-lookup"><span data-stu-id="2eecc-146">**ClientSetNull**</span></span>     | <span data-ttu-id="2eecc-147">引发 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="2eecc-147">SaveChanges throws</span></span>                  | <span data-ttu-id="2eecc-148">无</span><span class="sxs-lookup"><span data-stu-id="2eecc-148">None</span></span>                                  |
| <span data-ttu-id="2eecc-149">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="2eecc-149">**SetNull**</span></span>           | <span data-ttu-id="2eecc-150">SaveChanges 引发异常</span><span class="sxs-lookup"><span data-stu-id="2eecc-150">SaveChanges throws</span></span>                  | <span data-ttu-id="2eecc-151">引发 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="2eecc-151">SaveChanges throws</span></span>                    |
| <span data-ttu-id="2eecc-152">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="2eecc-152">**Restrict**</span></span>          | <span data-ttu-id="2eecc-153">None</span><span class="sxs-lookup"><span data-stu-id="2eecc-153">None</span></span>                                | <span data-ttu-id="2eecc-154">无</span><span class="sxs-lookup"><span data-stu-id="2eecc-154">None</span></span>                                  |

<span data-ttu-id="2eecc-155">在上表中，“无”\*\* 可能会造成约束冲突。</span><span class="sxs-lookup"><span data-stu-id="2eecc-155">In the tables above, *None* can result in a constraint violation.</span></span> <span data-ttu-id="2eecc-156">例如，如果已删除主体/子实体，但不执行任何操作来更改依赖项/子项的外键，则由于发生外键约束冲突，数据库将可能会引发 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="2eecc-156">For example, if a principal/child entity is deleted but no action is taken to change the foreign key of a dependent/child, then the database will likely throw on SaveChanges due to a foreign constraint violation.</span></span>

<span data-ttu-id="2eecc-157">在高级别中：</span><span class="sxs-lookup"><span data-stu-id="2eecc-157">At a high level:</span></span>

* <span data-ttu-id="2eecc-158">如果实体在没有父项时不能存在，且希望 EF 负责自动删除子项，则使用“Cascade”\*\*。</span><span class="sxs-lookup"><span data-stu-id="2eecc-158">If you have entities that cannot exist without a parent, and you want EF to take care for deleting the children automatically, then use *Cascade*.</span></span>
  * <span data-ttu-id="2eecc-159">在没有父项时不能存在的实体通常使用必选关系，其中“Cascade”\*\* 是默认值。</span><span class="sxs-lookup"><span data-stu-id="2eecc-159">Entities that cannot exist without a parent usually make use of required relationships, for which *Cascade* is the default.</span></span>
* <span data-ttu-id="2eecc-160">如果实体可能有或可能没有父项，且希望 EF 负责为你将外键变为 null，则使用“ClientSetNull”\*\*</span><span class="sxs-lookup"><span data-stu-id="2eecc-160">If you have entities that may or may not have a parent, and you want EF to take care of nulling out the foreign key for you, then use *ClientSetNull*</span></span>
  * <span data-ttu-id="2eecc-161">在没有父项时可以存在的实体通常使用可选关系，其中“ClientSetNull”\*\* 是默认值。</span><span class="sxs-lookup"><span data-stu-id="2eecc-161">Entities that can exist without a parent usually make use of optional relationships, for which *ClientSetNull* is the default.</span></span>
  * <span data-ttu-id="2eecc-162">如果希望数据库即使在未加载子实体时也尝试将 null 值传播到子外键，则使用“SetNull”\*\*。</span><span class="sxs-lookup"><span data-stu-id="2eecc-162">If you want the database to also try to propagate null values to child foreign keys even when the child entity is not loaded, then use *SetNull*.</span></span> <span data-ttu-id="2eecc-163">但是，请注意，数据库必须支持此操作，并且如此配置数据库可能会导致其他限制，实际上这通常会使此选项不适用。</span><span class="sxs-lookup"><span data-stu-id="2eecc-163">However, note that the database must support this, and configuring the database like this can result in other restrictions, which in practice often makes this option impractical.</span></span> <span data-ttu-id="2eecc-164">这就是*SetNull*不是默认值的原因。</span><span class="sxs-lookup"><span data-stu-id="2eecc-164">This is why *SetNull* is not the default.</span></span>
* <span data-ttu-id="2eecc-165">如果不希望 EF Core 始终自动删除实体或自动将外键变为 null，则使用“Restrict”\*\*。</span><span class="sxs-lookup"><span data-stu-id="2eecc-165">If you don't want EF Core to ever delete an entity automatically or null out the foreign key automatically, then use *Restrict*.</span></span> <span data-ttu-id="2eecc-166">请注意，这要求使用代码手动同步子实体及其外键值，否则将引发约束异常。</span><span class="sxs-lookup"><span data-stu-id="2eecc-166">Note that this requires that your code keep child entities and their foreign key values in sync manually otherwise constraint exceptions will be thrown.</span></span>

> [!NOTE]
> <span data-ttu-id="2eecc-167">在 EF Core（与 EF6 不同）中，不会立即产生级联影响，而是仅在调用 SaveChanges 时产生。</span><span class="sxs-lookup"><span data-stu-id="2eecc-167">In EF Core, unlike EF6, cascading effects do not happen immediately, but instead only when SaveChanges is called.</span></span>

## <a name="entity-deletion-examples"></a><span data-ttu-id="2eecc-168">实体删除示例</span><span class="sxs-lookup"><span data-stu-id="2eecc-168">Entity deletion examples</span></span>

<span data-ttu-id="2eecc-169">以下代码是可下载并运行的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)的一部分。</span><span class="sxs-lookup"><span data-stu-id="2eecc-169">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="2eecc-170">此示例显示了，当删除父实体时，可选关系和必选关系的每个删除行为会发生的情况。</span><span class="sxs-lookup"><span data-stu-id="2eecc-170">The sample shows what happens for each delete behavior for both optional and required relationships when a parent entity is deleted.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

<span data-ttu-id="2eecc-171">我们来看一看每个变化以了解所发生的情况。</span><span class="sxs-lookup"><span data-stu-id="2eecc-171">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="2eecc-172">具有必选或可选关系的 DeleteBehavior.Cascade</span><span class="sxs-lookup"><span data-stu-id="2eecc-172">DeleteBehavior.Cascade with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="2eecc-173">博客标记为已删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-173">Blog is marked as Deleted</span></span>
* <span data-ttu-id="2eecc-174">文章最初保持不变，因为在调用 SaveChanges 之前不会发生级联</span><span class="sxs-lookup"><span data-stu-id="2eecc-174">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="2eecc-175">SaveChanges 发送对依赖项/子项（文章）和主体/父项（博客）的删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-175">SaveChanges sends deletes for both dependents/children (posts) and then the principal/parent (blog)</span></span>
* <span data-ttu-id="2eecc-176">保存后，所有实体都会分离，因为它们现在已从数据库中删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-176">After saving, all entities are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="2eecc-177">具有必选关系的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="2eecc-177">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="2eecc-178">博客标记为已删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-178">Blog is marked as Deleted</span></span>
* <span data-ttu-id="2eecc-179">文章最初保持不变，因为在调用 SaveChanges 之前不会发生级联</span><span class="sxs-lookup"><span data-stu-id="2eecc-179">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="2eecc-180">SaveChanges 尝试将文章外键设置为 null，但会失败，因为外键不可以为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-180">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="2eecc-181">具有可选关系的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="2eecc-181">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="2eecc-182">博客标记为已删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-182">Blog is marked as Deleted</span></span>
* <span data-ttu-id="2eecc-183">文章最初保持不变，因为在调用 SaveChanges 之前不会发生级联</span><span class="sxs-lookup"><span data-stu-id="2eecc-183">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="2eecc-184">SaveChanges 尝试在删除主体/父项（博客）之前将依赖项/子项（文章）的外键设置为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-184">SaveChanges attempts sets the FK of both dependents/children (posts) to null before deleting the principal/parent (blog)</span></span>
* <span data-ttu-id="2eecc-185">保存后，将删除主体/父项（博客），但仍会跟踪依赖项/子项（文章）</span><span class="sxs-lookup"><span data-stu-id="2eecc-185">After saving, the principal/parent (blog) is deleted, but the dependents/children (posts) are still tracked</span></span>
* <span data-ttu-id="2eecc-186">跟踪的依赖项/子项（文章）现在具有 null 外键值，并且对删除的主体/父项（博客）的引用已删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-186">The tracked dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="2eecc-187">具有必选或可选关系的 DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="2eecc-187">DeleteBehavior.Restrict with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="2eecc-188">博客标记为已删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-188">Blog is marked as Deleted</span></span>
* <span data-ttu-id="2eecc-189">文章最初保持不变，因为在调用 SaveChanges 之前不会发生级联</span><span class="sxs-lookup"><span data-stu-id="2eecc-189">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="2eecc-190">由于*Restrict*指告知 EF 不要自动将外键设置为 null，因此它保持为非 null，而 SaveChanges 将引发异常但不进行保存</span><span class="sxs-lookup"><span data-stu-id="2eecc-190">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="delete-orphans-examples"></a><span data-ttu-id="2eecc-191">删除孤立项示例</span><span class="sxs-lookup"><span data-stu-id="2eecc-191">Delete orphans examples</span></span>

<span data-ttu-id="2eecc-192">以下代码是可下载并运行的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/)的一部分。</span><span class="sxs-lookup"><span data-stu-id="2eecc-192">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="2eecc-193">此示例显示了，当断开父项/主体及其子项/依赖项之间的关系时，可选关系和必选关系的每个删除行为会发生的情况。</span><span class="sxs-lookup"><span data-stu-id="2eecc-193">The sample shows what happens for each delete behavior for both optional and required relationships when the relationship between a parent/principal and its children/dependents is severed.</span></span> <span data-ttu-id="2eecc-194">在此示例中，通过从主体/父项（博客）上的集合导航属性中删除依赖项/子项（文章）来断开关系。</span><span class="sxs-lookup"><span data-stu-id="2eecc-194">In this example, the relationship is severed by removing the dependents/children (posts) from the collection navigation property on the principal/parent (blog).</span></span> <span data-ttu-id="2eecc-195">但是，如果将从依赖项/子项到主体/父项的引用变为 null，则行为相同。</span><span class="sxs-lookup"><span data-stu-id="2eecc-195">However, the behavior is the same if the reference from dependent/child to principal/parent is instead nulled out.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

<span data-ttu-id="2eecc-196">我们来看一看每个变化以了解所发生的情况。</span><span class="sxs-lookup"><span data-stu-id="2eecc-196">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="2eecc-197">具有必选或可选关系的 DeleteBehavior.Cascade</span><span class="sxs-lookup"><span data-stu-id="2eecc-197">DeleteBehavior.Cascade with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="2eecc-198">文章标记为已修改，因为断开关系导致外键标记为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-198">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="2eecc-199">如果外键不可以为 null，则即使实际值标记为 null 也不会更改</span><span class="sxs-lookup"><span data-stu-id="2eecc-199">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="2eecc-200">SaveChanges 发送对依赖项/子项（文章）的删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-200">SaveChanges sends deletes for dependents/children (posts)</span></span>
* <span data-ttu-id="2eecc-201">保存后，依赖项/子项（文章）会分离，因为它们现在已从数据库中删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-201">After saving, the dependents/children (posts) are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="2eecc-202">具有必选关系的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="2eecc-202">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="2eecc-203">文章标记为已修改，因为断开关系导致外键标记为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-203">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="2eecc-204">如果外键不可以为 null，则即使实际值标记为 null 也不会更改</span><span class="sxs-lookup"><span data-stu-id="2eecc-204">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="2eecc-205">SaveChanges 尝试将文章外键设置为 null，但会失败，因为外键不可以为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-205">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="2eecc-206">具有可选关系的 DeleteBehavior.ClientSetNull 或 DeleteBehavior.SetNull</span><span class="sxs-lookup"><span data-stu-id="2eecc-206">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="2eecc-207">文章标记为已修改，因为断开关系导致外键标记为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-207">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="2eecc-208">如果外键不可以为 null，则即使实际值标记为 null 也不会更改</span><span class="sxs-lookup"><span data-stu-id="2eecc-208">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="2eecc-209">SaveChanges 将依赖项/子项（文章）的外键设置为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-209">SaveChanges sets the FK of both dependents/children (posts) to null</span></span>
* <span data-ttu-id="2eecc-210">保存后，依赖项/子项（文章）现在具有 null 外键值，并且对删除的主体/父项（博客）的引用已删除</span><span class="sxs-lookup"><span data-stu-id="2eecc-210">After saving, the dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="2eecc-211">具有必选或可选关系的 DeleteBehavior.Restrict</span><span class="sxs-lookup"><span data-stu-id="2eecc-211">DeleteBehavior.Restrict with required or optional relationship</span></span>

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="2eecc-212">文章标记为已修改，因为断开关系导致外键标记为 null</span><span class="sxs-lookup"><span data-stu-id="2eecc-212">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="2eecc-213">如果外键不可以为 null，则即使实际值标记为 null 也不会更改</span><span class="sxs-lookup"><span data-stu-id="2eecc-213">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="2eecc-214">由于*Restrict*指告知 EF 不要自动将外键设置为 null，因此它保持为非 null，而 SaveChanges 将引发异常但不进行保存</span><span class="sxs-lookup"><span data-stu-id="2eecc-214">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="cascading-to-untracked-entities"></a><span data-ttu-id="2eecc-215">级联到未跟踪的实体</span><span class="sxs-lookup"><span data-stu-id="2eecc-215">Cascading to untracked entities</span></span>

<span data-ttu-id="2eecc-216">调用“SaveChanges”\*\* 时，级联删除规则将应用于由上下文跟踪的所有实体。</span><span class="sxs-lookup"><span data-stu-id="2eecc-216">When you call *SaveChanges*, the cascade delete rules will be applied to any entities that are being tracked by the context.</span></span> <span data-ttu-id="2eecc-217">这是上述所有示例的情况，即生成用于删除主体/父项（博客）和所有依赖项/子项（文章）的 SQL 的原因：</span><span class="sxs-lookup"><span data-stu-id="2eecc-217">This is the situation in all the examples shown above, which is why SQL was generated to delete both the principal/parent (blog) and all the dependents/children (posts):</span></span>

```sql
DELETE FROM [Posts] WHERE [PostId] = 1
DELETE FROM [Posts] WHERE [PostId] = 2
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="2eecc-218">如果仅加载主体（例如，当为不含 `Include(b => b.Posts)` 的博客创建查询以包含文章时），则 SaveChanges 只会生成用于删除主体/父项的 SQL：</span><span class="sxs-lookup"><span data-stu-id="2eecc-218">If only the principal is loaded--for example, when a query is made for a blog without an `Include(b => b.Posts)` to also include posts--then SaveChanges will only generate SQL to delete the principal/parent:</span></span>

```sql
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="2eecc-219">如果数据库已配置相应的级联行为，则只会删除依赖项/子项（文章）。</span><span class="sxs-lookup"><span data-stu-id="2eecc-219">The dependents/children (posts) will only be deleted if the database has a corresponding cascade behavior configured.</span></span> <span data-ttu-id="2eecc-220">如果使用 EF 创建数据库，则会为你设置此级联行为。</span><span class="sxs-lookup"><span data-stu-id="2eecc-220">If you use EF to create the database, this cascade behavior will be setup for you.</span></span>
