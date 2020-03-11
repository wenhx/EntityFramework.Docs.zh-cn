---
title: 加载相关实体-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: c359d8d32a88049213fd5e98e99fe49d7e3121a3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414463"
---
# <a name="loading-related-entities"></a><span data-ttu-id="813b7-102">正在加载相关实体</span><span class="sxs-lookup"><span data-stu-id="813b7-102">Loading Related Entities</span></span>

<span data-ttu-id="813b7-103">实体框架支持三种方法来加载相关数据-预先加载、延迟加载和显式加载。</span><span class="sxs-lookup"><span data-stu-id="813b7-103">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="813b7-104">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="813b7-104">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>

## <a name="eagerly-loading"></a><span data-ttu-id="813b7-105">积极加载</span><span class="sxs-lookup"><span data-stu-id="813b7-105">Eagerly Loading</span></span>

<span data-ttu-id="813b7-106">预先加载是指一种实体类型的查询，它还会在查询中加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-106">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="813b7-107">预先加载是通过使用 Include 方法实现的。</span><span class="sxs-lookup"><span data-stu-id="813b7-107">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="813b7-108">例如，下面的查询将加载博客以及与每个博客相关的所有帖子。</span><span class="sxs-lookup"><span data-stu-id="813b7-108">For example, the queries below will load blogs and all the posts related to each blog.</span></span>

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> <span data-ttu-id="813b7-109">Include 是 System.web 命名空间中的扩展方法，因此请确保正在使用该命名空间。</span><span class="sxs-lookup"><span data-stu-id="813b7-109">Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="813b7-110">积极加载多个级别</span><span class="sxs-lookup"><span data-stu-id="813b7-110">Eagerly loading multiple levels</span></span>

<span data-ttu-id="813b7-111">还可以积极加载多个级别的相关实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-111">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="813b7-112">下面的查询显示如何为收集和引用导航属性执行此操作的示例。</span><span class="sxs-lookup"><span data-stu-id="813b7-112">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> <span data-ttu-id="813b7-113">目前不能筛选要加载的相关实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-113">It is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="813b7-114">Include 将始终引入所有相关实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-114">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="813b7-115">延迟加载</span><span class="sxs-lookup"><span data-stu-id="813b7-115">Lazy Loading</span></span>

<span data-ttu-id="813b7-116">延迟加载是指首次访问引用实体/实体的属性时，从数据库自动加载实体或实体集合的过程。</span><span class="sxs-lookup"><span data-stu-id="813b7-116">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="813b7-117">使用 POCO 实体类型时，延迟加载是通过创建派生代理类型的实例，然后重写虚拟属性来添加加载挂钩来实现的。</span><span class="sxs-lookup"><span data-stu-id="813b7-117">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="813b7-118">例如，在使用下面定义的博客实体类时，将在第一次访问 "发布" 导航属性时加载相关的文章：</span><span class="sxs-lookup"><span data-stu-id="813b7-118">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="813b7-119">为序列化关闭延迟加载</span><span class="sxs-lookup"><span data-stu-id="813b7-119">Turn lazy loading off for serialization</span></span>

<span data-ttu-id="813b7-120">延迟加载和序列化不会很好地混合，如果您不小心，只是因为启用了延迟加载，最终就可以对整个数据库进行查询。</span><span class="sxs-lookup"><span data-stu-id="813b7-120">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="813b7-121">大多数序列化程序通过访问类型实例上的每个属性来工作。</span><span class="sxs-lookup"><span data-stu-id="813b7-121">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="813b7-122">属性访问会触发延迟加载，因此会序列化更多的实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-122">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="813b7-123">在这些实体上，将访问这些实体的属性，甚至还会加载更多实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-123">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="813b7-124">在对实体进行序列化之前，最好关闭延迟加载。</span><span class="sxs-lookup"><span data-stu-id="813b7-124">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="813b7-125">以下部分介绍了如何执行该操作。</span><span class="sxs-lookup"><span data-stu-id="813b7-125">The following sections show how to do this.</span></span>

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="813b7-126">关闭特定导航属性的延迟加载</span><span class="sxs-lookup"><span data-stu-id="813b7-126">Turning off lazy loading for specific navigation properties</span></span>

<span data-ttu-id="813b7-127">可以通过将 "Post" 属性设为 "非虚拟" 来关闭 Post 集合的延迟加载：</span><span class="sxs-lookup"><span data-stu-id="813b7-127">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

<span data-ttu-id="813b7-128">仍可使用预先加载（请参阅上面的*积极加载*）或 Load 方法（请参阅下面的*显式加载*）来加载发布集合。</span><span class="sxs-lookup"><span data-stu-id="813b7-128">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="813b7-129">关闭所有实体的延迟加载</span><span class="sxs-lookup"><span data-stu-id="813b7-129">Turn off lazy loading for all entities</span></span>

<span data-ttu-id="813b7-130">通过在配置属性上设置标志，可以为上下文中的所有实体关闭延迟加载。</span><span class="sxs-lookup"><span data-stu-id="813b7-130">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="813b7-131">例如：</span><span class="sxs-lookup"><span data-stu-id="813b7-131">For example:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

<span data-ttu-id="813b7-132">仍可使用预先加载（请参阅上面的*积极加载*）或 Load 方法（请参阅下面的*显式加载*）来加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="813b7-132">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

## <a name="explicitly-loading"></a><span data-ttu-id="813b7-133">显式加载</span><span class="sxs-lookup"><span data-stu-id="813b7-133">Explicitly Loading</span></span>

<span data-ttu-id="813b7-134">即使已禁用延迟加载，仍可能会延迟加载相关实体，但必须通过显式调用来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="813b7-134">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="813b7-135">为此，请对相关实体的条目使用 Load 方法。</span><span class="sxs-lookup"><span data-stu-id="813b7-135">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="813b7-136">例如：</span><span class="sxs-lookup"><span data-stu-id="813b7-136">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> <span data-ttu-id="813b7-137">如果实体具有指向另一个实体的导航属性，则应使用 Reference 方法。</span><span class="sxs-lookup"><span data-stu-id="813b7-137">The Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="813b7-138">另一方面，如果实体具有指向其他实体的集合的导航属性，则应使用集合方法。</span><span class="sxs-lookup"><span data-stu-id="813b7-138">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="813b7-139">显式加载相关实体时应用筛选器</span><span class="sxs-lookup"><span data-stu-id="813b7-139">Applying filters when explicitly loading related entities</span></span>

<span data-ttu-id="813b7-140">查询方法提供对实体框架在加载相关实体时将使用的基础查询的访问权限。</span><span class="sxs-lookup"><span data-stu-id="813b7-140">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="813b7-141">然后，你可以使用 LINQ 将筛选器应用于查询，然后通过调用 LINQ 扩展方法（例如 System.linq.enumerable.tolist、Load 等）执行这些筛选器。查询方法既可以与引用导航属性一起使用，也可以用于集合导航属性，但对于可用于仅加载部分集合的集合最有用。</span><span class="sxs-lookup"><span data-stu-id="813b7-141">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="813b7-142">例如：</span><span class="sxs-lookup"><span data-stu-id="813b7-142">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

<span data-ttu-id="813b7-143">使用查询方法时，通常最好关闭导航属性的延迟加载。</span><span class="sxs-lookup"><span data-stu-id="813b7-143">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="813b7-144">这是因为在执行筛选的查询之前或之后，延迟加载机制可能会自动加载整个集合。</span><span class="sxs-lookup"><span data-stu-id="813b7-144">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>

> [!NOTE]
> <span data-ttu-id="813b7-145">虽然可将关系指定为字符串而不是 lambda 表达式，但在使用字符串时返回的 IQueryable 并不是泛型的，因此通常需要强制转换方法，然后才能对其执行任何有用的操作。</span><span class="sxs-lookup"><span data-stu-id="813b7-145">While the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="813b7-146">使用 Query 计算相关实体而不加载它们</span><span class="sxs-lookup"><span data-stu-id="813b7-146">Using Query to count related entities without loading them</span></span>

<span data-ttu-id="813b7-147">有时，了解数据库中与另一个实体相关的实体的数量并不实际产生加载所有这些实体的成本是非常有用的。</span><span class="sxs-lookup"><span data-stu-id="813b7-147">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="813b7-148">带有 LINQ Count 方法的 Query 方法可用于执行此操作。</span><span class="sxs-lookup"><span data-stu-id="813b7-148">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="813b7-149">例如：</span><span class="sxs-lookup"><span data-stu-id="813b7-149">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
