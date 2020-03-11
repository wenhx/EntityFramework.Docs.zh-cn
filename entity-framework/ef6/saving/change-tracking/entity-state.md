---
title: 使用实体状态-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: ef0e8d5a5a9d66adab7046088c49d8cd472edc8a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416251"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="1d696-102">使用实体状态</span><span class="sxs-lookup"><span data-stu-id="1d696-102">Working with entity states</span></span>
<span data-ttu-id="1d696-103">本主题将介绍如何添加实体并将其附加到上下文，以及实体框架如何在 SaveChanges 期间处理这些实体。</span><span class="sxs-lookup"><span data-stu-id="1d696-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="1d696-104">实体框架负责在实体连接到上下文时跟踪实体的状态，但在断开连接或 N 层方案中，你可以让 EF 知道实体应采用的状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="1d696-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="1d696-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="1d696-106">实体状态和 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="1d696-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="1d696-107">实体可以是 EntityState 枚举定义的五种状态之一。</span><span class="sxs-lookup"><span data-stu-id="1d696-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="1d696-108">这些状态是：</span><span class="sxs-lookup"><span data-stu-id="1d696-108">These states are:</span></span>  

- <span data-ttu-id="1d696-109">已添加：上下文正在跟踪实体，但数据库中尚不存在该实体</span><span class="sxs-lookup"><span data-stu-id="1d696-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="1d696-110">保持不变：上下文正在跟踪实体，该实体存在于数据库中，并且其属性值未更改为数据库中的值</span><span class="sxs-lookup"><span data-stu-id="1d696-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="1d696-111">已修改：实体正在由上下文跟踪，并存在于数据库中，并且其部分或全部属性值已修改</span><span class="sxs-lookup"><span data-stu-id="1d696-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="1d696-112">已删除：实体正在由上下文跟踪，并存在于数据库中，但在下次调用 SaveChanges 时已标记为要从数据库中删除</span><span class="sxs-lookup"><span data-stu-id="1d696-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="1d696-113">已分离：上下文未跟踪该实体</span><span class="sxs-lookup"><span data-stu-id="1d696-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="1d696-114">对于不同状态中的实体，SaveChanges 执行不同的操作：</span><span class="sxs-lookup"><span data-stu-id="1d696-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="1d696-115">SaveChanges 不会接触到未更改的实体。</span><span class="sxs-lookup"><span data-stu-id="1d696-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="1d696-116">对于处于未更改状态的实体，不会将更新发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="1d696-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="1d696-117">添加的实体将插入到数据库中，并在 SaveChanges 返回时变为不变。</span><span class="sxs-lookup"><span data-stu-id="1d696-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="1d696-118">修改后的实体将在数据库中更新，并在 SaveChanges 返回时变得不变。</span><span class="sxs-lookup"><span data-stu-id="1d696-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="1d696-119">删除的实体将从数据库中删除，然后与上下文分离。</span><span class="sxs-lookup"><span data-stu-id="1d696-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="1d696-120">下面的示例演示了如何更改实体或实体关系图的状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="1d696-121">向上下文中添加新实体</span><span class="sxs-lookup"><span data-stu-id="1d696-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="1d696-122">可以通过对 DbSet 调用 Add 方法，将新实体添加到上下文中。</span><span class="sxs-lookup"><span data-stu-id="1d696-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="1d696-123">这会使实体处于已添加状态，这意味着它将在下一次调用 SaveChanges 时插入到数据库中。</span><span class="sxs-lookup"><span data-stu-id="1d696-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="1d696-124">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-125">向上下文中添加新实体的另一种方法是将其状态更改为 "已添加"。</span><span class="sxs-lookup"><span data-stu-id="1d696-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="1d696-126">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-127">最后，可以通过将新实体挂钩到已跟踪的另一个实体来向上下文中添加新实体。</span><span class="sxs-lookup"><span data-stu-id="1d696-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="1d696-128">这可以是将新实体添加到另一个实体的集合导航属性中，或通过设置另一个实体的引用导航属性来指向新实体。</span><span class="sxs-lookup"><span data-stu-id="1d696-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="1d696-129">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-130">请注意，对于所有这些示例，如果添加的实体具有对尚未跟踪的其他实体的引用，则这些新实体也将添加到上下文中，并将在下次调用 SaveChanges 时插入到数据库中。</span><span class="sxs-lookup"><span data-stu-id="1d696-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="1d696-131">将现有实体附加到上下文</span><span class="sxs-lookup"><span data-stu-id="1d696-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="1d696-132">如果你的实体已存在于数据库中，但当前未由上下文跟踪，则可以使用 DbSet 上的 Attach 方法告诉上下文跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="1d696-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="1d696-133">实体在上下文中将处于未更改状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="1d696-134">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-135">请注意，如果在调用 SaveChanges 时未执行附加实体的任何其他操作，则不会对数据库进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="1d696-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="1d696-136">这是因为实体处于未更改状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="1d696-137">将现有实体附加到上下文的另一种方法是将其状态更改为 "未更改"。</span><span class="sxs-lookup"><span data-stu-id="1d696-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="1d696-138">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-139">请注意，对于这两个示例，如果附加的实体已引用尚未跟踪的其他实体，则这些新实体还会附加到处于未更改状态的上下文。</span><span class="sxs-lookup"><span data-stu-id="1d696-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="1d696-140">将现有的但修改的实体附加到上下文</span><span class="sxs-lookup"><span data-stu-id="1d696-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="1d696-141">如果你的实体已存在于数据库中，但可能已对其进行了更改，则可以通知上下文附加实体并将其状态设置为 "已修改"。</span><span class="sxs-lookup"><span data-stu-id="1d696-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="1d696-142">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-143">当你将状态更改为 "已修改" 时，实体的所有属性都将标记为已修改，并且在调用 SaveChanges 时，所有属性值都将发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="1d696-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="1d696-144">请注意，如果附加的实体具有对尚未跟踪的其他实体的引用，则这些新实体将以未更改状态附加到上下文中，而不会自动进行修改。</span><span class="sxs-lookup"><span data-stu-id="1d696-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="1d696-145">如果需要将多个实体标记为 "已修改"，则应单独设置每个实体的状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="1d696-146">更改所跟踪实体的状态</span><span class="sxs-lookup"><span data-stu-id="1d696-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="1d696-147">您可以通过对其项设置状态属性来更改已被跟踪的实体的状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="1d696-148">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-148">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="1d696-149">请注意，为已跟踪的实体调用 "添加或附加" 也可用于更改实体状态。</span><span class="sxs-lookup"><span data-stu-id="1d696-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="1d696-150">例如，为当前处于添加状态的实体调用 Attach 会将其状态更改为 "不更改"。</span><span class="sxs-lookup"><span data-stu-id="1d696-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="1d696-151">插入或更新模式</span><span class="sxs-lookup"><span data-stu-id="1d696-151">Insert or update pattern</span></span>  

<span data-ttu-id="1d696-152">某些应用程序的一种常见模式是将实体添加为新实体（生成数据库插入），或将实体附加到现有实体并将其标记为已修改（生成数据库更新），具体取决于主键的值。</span><span class="sxs-lookup"><span data-stu-id="1d696-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="1d696-153">例如，在使用数据库生成的整数主键时，通常将包含零键的实体作为新的和具有非零键的实体视为现有的。</span><span class="sxs-lookup"><span data-stu-id="1d696-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="1d696-154">可以通过基于主键值的检查设置实体状态来实现此模式。</span><span class="sxs-lookup"><span data-stu-id="1d696-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="1d696-155">例如：</span><span class="sxs-lookup"><span data-stu-id="1d696-155">For example:</span></span>  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

<span data-ttu-id="1d696-156">请注意，当你将状态更改为 "已修改" 时，实体的所有属性都将标记为已修改，并且在调用 SaveChanges 时，所有属性值都将发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="1d696-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
