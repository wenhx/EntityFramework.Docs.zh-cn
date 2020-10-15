---
title: 查询和查找实体 - EF6
description: 查询和查找 Entity Framework 6 中的实体
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: ec0239ed54099226d009031af79388f5ae00cdc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065885"
---
# <a name="querying-and-finding-entities"></a><span data-ttu-id="2681a-103">查询和查找实体</span><span class="sxs-lookup"><span data-stu-id="2681a-103">Querying and Finding Entities</span></span>
<span data-ttu-id="2681a-104">本主题介绍使用实体框架查询数据的各种方法，包括 LINQ 和 Find 方法。</span><span class="sxs-lookup"><span data-stu-id="2681a-104">This topic covers the various ways you can query for data using Entity Framework, including LINQ and the Find method.</span></span> <span data-ttu-id="2681a-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="2681a-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="finding-entities-using-a-query"></a><span data-ttu-id="2681a-106">使用查询查找实体</span><span class="sxs-lookup"><span data-stu-id="2681a-106">Finding entities using a query</span></span>  

<span data-ttu-id="2681a-107">DbSet 和 IDbSet 可实现 IQueryable，因此可用作针对数据库编写 LINQ 查询的起点。</span><span class="sxs-lookup"><span data-stu-id="2681a-107">DbSet and IDbSet implement IQueryable and so can be used as the starting point for writing a LINQ query against the database.</span></span> <span data-ttu-id="2681a-108">在此不适合深入讨论 LINQ，但以下提供了几个简单示例：</span><span class="sxs-lookup"><span data-stu-id="2681a-108">This is not the appropriate place for an in-depth discussion of LINQ, but here are a couple of simple examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

<span data-ttu-id="2681a-109">请注意，DbSet 和 IDbSet 始终针对数据库创建查询，并且始终会涉及数据库往返，即使返回的实体已存在于上下文中。</span><span class="sxs-lookup"><span data-stu-id="2681a-109">Note that DbSet and IDbSet always create queries against the database and will always involve a round trip to the database even if the entities returned already exist in the context.</span></span> <span data-ttu-id="2681a-110">出现以下情况时，会针对数据库执行查询：</span><span class="sxs-lookup"><span data-stu-id="2681a-110">A query is executed against the database when:</span></span>  

- <span data-ttu-id="2681a-111">查询由 foreach (C#) 或 For Each (Visual Basic) 语句枚举\*\*\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2681a-111">It is enumerated by a **foreach** (C#) or **For Each** (Visual Basic) statement.</span></span>  
- <span data-ttu-id="2681a-112">查询由集合操作（如 [ToArray](https://msdn.microsoft.com/library/bb298736)、[ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) 或 [ToList](https://msdn.microsoft.com/library/bb342261)）枚举。</span><span class="sxs-lookup"><span data-stu-id="2681a-112">It is enumerated by a collection operation such as [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary), or [ToList](https://msdn.microsoft.com/library/bb342261).</span></span>  
- <span data-ttu-id="2681a-113">在查询最外部指定了 LINQ 运算符，例如 [First](https://msdn.microsoft.com/library/bb291976) 或 [Any](https://msdn.microsoft.com/library/bb337697)。</span><span class="sxs-lookup"><span data-stu-id="2681a-113">LINQ operators such as [First](https://msdn.microsoft.com/library/bb291976) or [Any](https://msdn.microsoft.com/library/bb337697) are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="2681a-114">调用了以下方法：DbSet 上的 [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) 扩展方法、[DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) 和 Database.ExecuteSqlCommand。</span><span class="sxs-lookup"><span data-stu-id="2681a-114">The following methods are called: the [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) extension method on a DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx), and Database.ExecuteSqlCommand.</span></span>  

<span data-ttu-id="2681a-115">从数据库返回结果时，上下文中不存在的对象会附加到上下文。</span><span class="sxs-lookup"><span data-stu-id="2681a-115">When results are returned from the database, objects that do not exist in the context are attached to the context.</span></span> <span data-ttu-id="2681a-116">如果某个对象已存在于上下文中，则不返回现有对象（不会使用数据库值覆盖该对象的属性在对应项中的当前值和原始值）\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2681a-116">If an object is already in the context, the existing object is returned (the current and original values of the object's properties in the entry are **not** overwritten with database values).</span></span>  

<span data-ttu-id="2681a-117">执行查询时，结果集中不回返回已添加到上下文但尚未保存到数据库的实体。</span><span class="sxs-lookup"><span data-stu-id="2681a-117">When you perform a query, entities that have been added to the context but have not yet been saved to the database are not returned as part of the result set.</span></span> <span data-ttu-id="2681a-118">若要获取上下文中的数据，请参阅[本地数据](xref:ef6/querying/local-data)。</span><span class="sxs-lookup"><span data-stu-id="2681a-118">To get the data that is in the context, see [Local Data](xref:ef6/querying/local-data).</span></span>  

<span data-ttu-id="2681a-119">如果查询未从数据库返回任何行，则结果将是空集合，而不是 NULL\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2681a-119">If a query returns no rows from the database, the result will be an empty collection, rather than **null**.</span></span>  

## <a name="finding-entities-using-primary-keys"></a><span data-ttu-id="2681a-120">使用主键查找实体</span><span class="sxs-lookup"><span data-stu-id="2681a-120">Finding entities using primary keys</span></span>  

<span data-ttu-id="2681a-121">DbSet 上的 Find 方法使用主键值来尝试查找由上下文跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="2681a-121">The Find method on DbSet uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="2681a-122">如果在上下文中未找到实体，则会向数据库发送查询以在其中查找实体。</span><span class="sxs-lookup"><span data-stu-id="2681a-122">If the entity is not found in the context then a query will be sent to the database to find the entity there.</span></span> <span data-ttu-id="2681a-123">如果未在上下文中或数据库中找到实体，则返回 NULL。</span><span class="sxs-lookup"><span data-stu-id="2681a-123">Null is returned if the entity is not found in the context or in the database.</span></span>  

<span data-ttu-id="2681a-124">Find 与使用查询有两个重要区别：</span><span class="sxs-lookup"><span data-stu-id="2681a-124">Find is different from using a query in two significant ways:</span></span>  

- <span data-ttu-id="2681a-125">只有未在上下文中找到具有给定键的实体时，才会往返数据库。</span><span class="sxs-lookup"><span data-stu-id="2681a-125">A round-trip to the database will only be made if the entity with the given key is not found in the context.</span></span>  
- <span data-ttu-id="2681a-126">Find 会返回处于“已添加”状态的实体。</span><span class="sxs-lookup"><span data-stu-id="2681a-126">Find will return entities that are in the Added state.</span></span> <span data-ttu-id="2681a-127">即 Find 会返回已添加到上下文但尚未保存到数据库的实体。</span><span class="sxs-lookup"><span data-stu-id="2681a-127">That is, Find will return entities that have been added to the context but have not yet been saved to the database.</span></span>  
### <a name="finding-an-entity-by-primary-key"></a><span data-ttu-id="2681a-128">通过主键查找实体</span><span class="sxs-lookup"><span data-stu-id="2681a-128">Finding an entity by primary key</span></span>  

<span data-ttu-id="2681a-129">以下代码演示了 Find 的部分用法：</span><span class="sxs-lookup"><span data-stu-id="2681a-129">The following code shows some uses of Find:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a><span data-ttu-id="2681a-130">通过组合主键查找实体</span><span class="sxs-lookup"><span data-stu-id="2681a-130">Finding an entity by composite primary key</span></span>  

<span data-ttu-id="2681a-131">实体框架允许实体具有组合键，即由多个属性组成的键。</span><span class="sxs-lookup"><span data-stu-id="2681a-131">Entity Framework allows your entities to have composite keys - that's a key that is made up of more than one property.</span></span> <span data-ttu-id="2681a-132">例如，用户可具有表示特定博客的用户设置的 BlogSettings 实体。</span><span class="sxs-lookup"><span data-stu-id="2681a-132">For example, you could have a BlogSettings entity that represents a users settings for a particular blog.</span></span> <span data-ttu-id="2681a-133">因为用户的每个博客只能拥有一个 BlogSettings，所以可以选择将 BlogId 和用户名结合作为 BlogSettings 的主键。</span><span class="sxs-lookup"><span data-stu-id="2681a-133">Because a user would only ever have one BlogSettings for each blog you could chose to make the primary key of BlogSettings a combination of BlogId and Username.</span></span> <span data-ttu-id="2681a-134">以下代码尝试查找 BlogId = 3，用户名 =“johndoe1987”的 BlogSettings：</span><span class="sxs-lookup"><span data-stu-id="2681a-134">The following code attempts to find the BlogSettings with BlogId = 3 and Username = "johndoe1987":</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

<span data-ttu-id="2681a-135">请注意，具有组合键时，需使用 ColumnAttribute 或 Fluent API 来指定组合键属性的顺序。</span><span class="sxs-lookup"><span data-stu-id="2681a-135">Note that when you have composite keys you need to use ColumnAttribute or the fluent API to specify an ordering for the properties of the composite key.</span></span> <span data-ttu-id="2681a-136">指定构成键的值时，对 Find 的调用必须使用此顺序。</span><span class="sxs-lookup"><span data-stu-id="2681a-136">The call to Find must use this order when specifying the values that form the key.</span></span>  
