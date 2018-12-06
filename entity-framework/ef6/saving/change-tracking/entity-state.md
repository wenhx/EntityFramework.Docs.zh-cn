---
title: 使用实体状态的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: ef0e8d5a5a9d66adab7046088c49d8cd472edc8a
ms.sourcegitcommit: e5f9ca4aa41e64141fa63a1e5fcf4d4775d67d24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899647"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="78d58-102">使用实体状态</span><span class="sxs-lookup"><span data-stu-id="78d58-102">Working with entity states</span></span>
<span data-ttu-id="78d58-103">本主题将介绍如何添加和将实体附加到上下文和实体框架如何处理这些在 SaveChanges 期间。</span><span class="sxs-lookup"><span data-stu-id="78d58-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="78d58-104">实体框架负责的跟踪的实体时它们已连接到一个上下文，但在断开连接或 N 层方案中，您可以让 EF 知道你的实体的何种状态的状态应为。</span><span class="sxs-lookup"><span data-stu-id="78d58-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="78d58-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="78d58-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="78d58-106">实体状态和 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="78d58-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="78d58-107">实体可以是一个五种状态中定义的 EntityState 枚举。</span><span class="sxs-lookup"><span data-stu-id="78d58-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="78d58-108">这些状态包括：</span><span class="sxs-lookup"><span data-stu-id="78d58-108">These states are:</span></span>  

- <span data-ttu-id="78d58-109">添加了： 实体正在由上下文跟踪，但在数据库中尚不存在</span><span class="sxs-lookup"><span data-stu-id="78d58-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="78d58-110">未更改： 实体正在由上下文跟踪和在数据库中存在并从数据库中的值未更改其属性值</span><span class="sxs-lookup"><span data-stu-id="78d58-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="78d58-111">修改： 实体正在由上下文跟踪和在数据库中存在并已修改某些或所有其属性值</span><span class="sxs-lookup"><span data-stu-id="78d58-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="78d58-112">已删除： 实体正在由上下文跟踪和在数据库中存在但已标记为删除从数据库下次调用 SaveChanges 时</span><span class="sxs-lookup"><span data-stu-id="78d58-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="78d58-113">已分离： 实体未被跟踪的上下文</span><span class="sxs-lookup"><span data-stu-id="78d58-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="78d58-114">SaveChanges 将执行不同操作的实体中的不同状态：</span><span class="sxs-lookup"><span data-stu-id="78d58-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="78d58-115">未更改的实体不涉及通过 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="78d58-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="78d58-116">更新会向数据库中未更改状态的实体。</span><span class="sxs-lookup"><span data-stu-id="78d58-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="78d58-117">添加实体插入到数据库，然后会保持不变时 SaveChanges 返回。</span><span class="sxs-lookup"><span data-stu-id="78d58-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="78d58-118">已修改的实体在数据库中更新，然后会保持不变时 SaveChanges 返回。</span><span class="sxs-lookup"><span data-stu-id="78d58-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="78d58-119">已删除的实体从数据库中删除，然后从上下文分离。</span><span class="sxs-lookup"><span data-stu-id="78d58-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="78d58-120">以下示例显示实体或实体关系图状态可以更改的方法。</span><span class="sxs-lookup"><span data-stu-id="78d58-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="78d58-121">将新实体添加到上下文</span><span class="sxs-lookup"><span data-stu-id="78d58-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="78d58-122">新的实体可以通过在 DbSet 上调用 Add 方法添加到上下文。</span><span class="sxs-lookup"><span data-stu-id="78d58-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="78d58-123">这将实体的已添加状态，这意味着，它将插入到数据库调用 SaveChanges 的下一个时间。</span><span class="sxs-lookup"><span data-stu-id="78d58-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="78d58-124">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="78d58-125">若要将新实体添加到上下文的另一种方法是将其状态更改为 Added。</span><span class="sxs-lookup"><span data-stu-id="78d58-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="78d58-126">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="78d58-127">最后，可以挂接到已被跟踪的另一个实体的上下文中添加新实体。</span><span class="sxs-lookup"><span data-stu-id="78d58-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="78d58-128">这可能是通过将新实体添加到另一个实体的集合导航属性或通过设置另一个实体以指向新的实体的引用导航属性。</span><span class="sxs-lookup"><span data-stu-id="78d58-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="78d58-129">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-129">For example:</span></span>  

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

<span data-ttu-id="78d58-130">请注意，对于所有这些示例，如果要添加的实体具有对不是其他实体的引用尚未然后跟踪这些新实体也将添加到上下文，并将插入到该数据库下次调用 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="78d58-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="78d58-131">将现有实体附加到上下文</span><span class="sxs-lookup"><span data-stu-id="78d58-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="78d58-132">如果你有已经知道的实体存在于数据库，但这当前未跟踪上下文则可指示要跟踪对 DbSet 使用附加方法的实体的上下文。</span><span class="sxs-lookup"><span data-stu-id="78d58-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="78d58-133">实体将处于 Unchanged 状态的上下文中。</span><span class="sxs-lookup"><span data-stu-id="78d58-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="78d58-134">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="78d58-135">请注意是否 SaveChanges 调用而无需执行附加的任何的实体其他操作将对数据库进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="78d58-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="78d58-136">这是因为实体处于 Unchanged 状态。</span><span class="sxs-lookup"><span data-stu-id="78d58-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="78d58-137">若要将现有实体附加到上下文的另一种方法是将其状态更改为 Unchanged。</span><span class="sxs-lookup"><span data-stu-id="78d58-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="78d58-138">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="78d58-139">请注意，为这两个示例如果要附加的实体具有对其他实体未被跟踪的引用然后这些新的实体将还附加到上下文中未更改状态。</span><span class="sxs-lookup"><span data-stu-id="78d58-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="78d58-140">附加现有的修改后的实体的上下文</span><span class="sxs-lookup"><span data-stu-id="78d58-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="78d58-141">如果你有已经知道的实体存在在数据库中，但到哪个可能进行了更改则可指示要将附加该实体并将其状态设置为已修改的上下文。</span><span class="sxs-lookup"><span data-stu-id="78d58-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="78d58-142">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="78d58-143">时将状态更改为已修改的实体的所有属性将都标记为已修改，并调用 SaveChanges 时的所有属性值将都发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="78d58-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="78d58-144">请注意是否要附加的实体具有对其他实体未被跟踪的引用，这些新的实体将附加到上下文中未更改状态，它们将不被指定为已修改。</span><span class="sxs-lookup"><span data-stu-id="78d58-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="78d58-145">如果有多个需要标记为已修改的实体应在单独的每个实体设置状态。</span><span class="sxs-lookup"><span data-stu-id="78d58-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="78d58-146">更改跟踪的实体的状态</span><span class="sxs-lookup"><span data-stu-id="78d58-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="78d58-147">你可以通过在其条目上设置的状态属性已被跟踪实体的状态。</span><span class="sxs-lookup"><span data-stu-id="78d58-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="78d58-148">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-148">For example:</span></span>  

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

<span data-ttu-id="78d58-149">请注意，已跟踪的实体调用添加或附加还可用来将实体状态更改。</span><span class="sxs-lookup"><span data-stu-id="78d58-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="78d58-150">例如，调用附加的实体，当前处于已添加状态将更改其状态为 Unchanged。</span><span class="sxs-lookup"><span data-stu-id="78d58-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="78d58-151">插入或更新模式</span><span class="sxs-lookup"><span data-stu-id="78d58-151">Insert or update pattern</span></span>  

<span data-ttu-id="78d58-152">某些应用程序的常见模式是将实体添加为新的 （导致数据库插入） 或附加与现有实体并将其标记为修改 （导致数据库更新） 根据为主键的值。</span><span class="sxs-lookup"><span data-stu-id="78d58-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="78d58-153">例如，使用数据库生成整数主键时，通常会将具有零个密钥作为新的实体和具有非零值密钥作为现有的实体。</span><span class="sxs-lookup"><span data-stu-id="78d58-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="78d58-154">此模式可以通过设置基于主键值的检查的实体状态来实现。</span><span class="sxs-lookup"><span data-stu-id="78d58-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="78d58-155">例如：</span><span class="sxs-lookup"><span data-stu-id="78d58-155">For example:</span></span>  

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

<span data-ttu-id="78d58-156">请注意时将状态更改为已修改的实体的所有属性将都标记为已修改的所有属性值将在调用 SaveChanges 时被都发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="78d58-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
