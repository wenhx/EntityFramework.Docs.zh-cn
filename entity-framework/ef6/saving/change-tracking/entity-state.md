---
title: 使用实体状态-EF6
description: 使用实体框架6中的实体状态
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 5373f7157882062e8a73b2bd414c6a8b9accdba4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064453"
---
# <a name="working-with-entity-states"></a>使用实体状态
本主题将介绍如何添加实体并将其附加到上下文，以及实体框架如何在 SaveChanges 期间处理这些实体。
实体框架负责在实体连接到上下文时跟踪实体的状态，但在断开连接或 N 层方案中，你可以让 EF 知道实体应采用的状态。
本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

## <a name="entity-states-and-savechanges"></a>实体状态和 SaveChanges

实体可以是 EntityState 枚举定义的五种状态之一。 这些状态包括：  

- 已添加：上下文正在跟踪实体，但数据库中尚不存在该实体  
- 保持不变：上下文正在跟踪实体，该实体存在于数据库中，并且其属性值未更改为数据库中的值  
- 已修改：实体正在由上下文跟踪，并存在于数据库中，并且其部分或全部属性值已修改  
- 已删除：实体正在由上下文跟踪，并存在于数据库中，但在下次调用 SaveChanges 时已标记为要从数据库中删除  
- 已分离：上下文未跟踪该实体  

对于不同状态中的实体，SaveChanges 执行不同的操作：  

- SaveChanges 不会接触到未更改的实体。 对于处于未更改状态的实体，不会将更新发送到数据库。  
- 添加的实体将插入到数据库中，并在 SaveChanges 返回时变为不变。  
- 修改后的实体将在数据库中更新，并在 SaveChanges 返回时变得不变。  
- 删除的实体将从数据库中删除，然后与上下文分离。  

下面的示例演示了如何更改实体或实体关系图的状态。  

## <a name="adding-a-new-entity-to-the-context"></a>向上下文中添加新实体  

可以通过对 DbSet 调用 Add 方法，将新实体添加到上下文中。
这会使实体处于已添加状态，这意味着它将在下一次调用 SaveChanges 时插入到数据库中。
例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

向上下文中添加新实体的另一种方法是将其状态更改为 "已添加"。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

最后，可以通过将新实体挂钩到已跟踪的另一个实体来向上下文中添加新实体。
这可以是将新实体添加到另一个实体的集合导航属性中，或通过设置另一个实体的引用导航属性来指向新实体。 例如：  

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

请注意，对于所有这些示例，如果添加的实体具有对尚未跟踪的其他实体的引用，则这些新实体也将添加到上下文中，并将在下次调用 SaveChanges 时插入到数据库中。  

## <a name="attaching-an-existing-entity-to-the-context"></a>将现有实体附加到上下文  

如果你的实体已存在于数据库中，但当前未由上下文跟踪，则可以使用 DbSet 上的 Attach 方法告诉上下文跟踪实体。 实体在上下文中将处于未更改状态。 例如：  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

请注意，如果在调用 SaveChanges 时未执行附加实体的任何其他操作，则不会对数据库进行任何更改。 这是因为实体处于未更改状态。  

将现有实体附加到上下文的另一种方法是将其状态更改为 "未更改"。 例如：  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

请注意，对于这两个示例，如果附加的实体已引用尚未跟踪的其他实体，则这些新实体还会附加到处于未更改状态的上下文。  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>将现有的但修改的实体附加到上下文  

如果你的实体已存在于数据库中，但可能已对其进行了更改，则可以通知上下文附加实体并将其状态设置为 "已修改"。
例如：  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

当你将状态更改为 "已修改" 时，实体的所有属性都将标记为已修改，并且在调用 SaveChanges 时，所有属性值都将发送到数据库。  

请注意，如果附加的实体具有对尚未跟踪的其他实体的引用，则这些新实体将以未更改状态附加到上下文中，而不会自动进行修改。
如果需要将多个实体标记为 "已修改"，则应单独设置每个实体的状态。  

## <a name="changing-the-state-of-a-tracked-entity"></a>更改所跟踪实体的状态  

您可以通过对其项设置状态属性来更改已被跟踪的实体的状态。 例如：  

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

请注意，为已跟踪的实体调用 "添加或附加" 也可用于更改实体状态。 例如，为当前处于添加状态的实体调用 Attach 会将其状态更改为 "不更改"。  

## <a name="insert-or-update-pattern"></a>插入或更新模式  

某些应用程序的一种常见模式是将实体添加为新 (导致数据库插入) 或将实体附加为现有，并将其标记为已修改 (导致数据库更新) ，具体取决于主键的值。
例如，在使用数据库生成的整数主键时，通常将包含零键的实体作为新的和具有非零键的实体视为现有的。
可以通过基于主键值的检查设置实体状态来实现此模式。 例如：  

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

请注意，当你将状态更改为 "已修改" 时，实体的所有属性都将标记为已修改，并且在调用 SaveChanges 时，所有属性值都将发送到数据库。  
