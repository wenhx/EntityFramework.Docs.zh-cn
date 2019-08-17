---
title: 加载相关实体-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: f40034475ed6659b60ab4317605fd1d802218d69
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565308"
---
# <a name="loading-related-entities"></a>正在加载相关实体
实体框架支持三种方法来加载相关数据-预先加载、延迟加载和显式加载。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

## <a name="eagerly-loading"></a>积极加载  

预先加载是指一种实体类型的查询, 它还会在查询中加载相关实体。 预先加载是通过使用 Include 方法实现的。 例如, 下面的查询将加载博客以及与每个博客相关的所有帖子。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```  

请注意, Include 是 System.web 命名空间中的扩展方法, 因此请确保正在使用该命名空间。  

### <a name="eagerly-loading-multiple-levels"></a>积极加载多个级别  

还可以积极加载多个级别的相关实体。 下面的查询显示如何为收集和引用导航属性执行此操作的示例。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

请注意, 目前不能筛选要加载的相关实体。 Include 将始终引入所有相关实体。  

## <a name="lazy-loading"></a>延迟加载  

延迟加载是指首次访问引用实体/实体的属性时, 从数据库自动加载实体或实体集合的过程。 使用 POCO 实体类型时, 延迟加载是通过创建派生代理类型的实例, 然后重写虚拟属性来添加加载挂钩来实现的。 例如, 在使用下面定义的博客实体类时, 将在第一次访问 "发布" 导航属性时加载相关的文章:  

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

### <a name="turn-lazy-loading-off-for-serialization"></a>为序列化关闭延迟加载  

延迟加载和序列化不会很好地混合, 如果您不小心, 只是因为启用了延迟加载, 最终就可以对整个数据库进行查询。 大多数序列化程序通过访问类型实例上的每个属性来工作。 属性访问会触发延迟加载, 因此会序列化更多的实体。 在这些实体上, 将访问这些实体的属性, 甚至还会加载更多实体。 在对实体进行序列化之前, 最好关闭延迟加载。 以下部分介绍了如何执行此操作。  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>关闭特定导航属性的延迟加载  

可以通过将 "Post" 属性设为 "非虚拟" 来关闭 Post 集合的延迟加载:  

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

仍可使用预先加载 (请参阅上面的*积极加载*) 或 Load 方法 (请参阅下面的*显式加载*) 来加载发布集合。  

### <a name="turn-off-lazy-loading-for-all-entities"></a>关闭所有实体的延迟加载  

通过在配置属性上设置标志, 可以为上下文中的所有实体关闭延迟加载。 例如:  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

仍可使用预先加载 (请参阅上面的*积极加载*) 或 Load 方法 (请参阅下面的*显式加载*) 来加载相关实体。  

## <a name="explicitly-loading"></a>显式加载  

即使已禁用延迟加载, 仍可能会延迟加载相关实体, 但必须通过显式调用来完成此操作。 为此, 请对相关实体的条目使用 Load 方法。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

请注意, 当实体具有指向另一个实体的导航属性时, 应使用 Reference 方法。 另一方面, 如果实体具有指向其他实体的集合的导航属性, 则应使用集合方法。  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>显式加载相关实体时应用筛选器  

查询方法提供对实体框架在加载相关实体时将使用的基础查询的访问权限。 然后, 你可以使用 LINQ 将筛选器应用于查询, 然后通过调用 LINQ 扩展方法 (例如 System.linq.enumerable.tolist、Load 等) 执行这些筛选器。查询方法既可以与引用导航属性一起使用, 也可以用于集合导航属性, 但对于可用于仅加载部分集合的集合最有用。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```  

使用查询方法时, 通常最好关闭导航属性的延迟加载。 这是因为在执行筛选的查询之前或之后, 延迟加载机制可能会自动加载整个集合。  

请注意, 虽然可以将关系指定为字符串而不是 lambda 表达式, 但在使用字符串时, 返回的 IQueryable 不是泛型的, 因此通常需要强制转换方法, 然后才能对其执行任何有用的操作。  

## <a name="using-query-to-count-related-entities-without-loading-them"></a>使用 Query 计算相关实体而不加载它们  

有时, 了解数据库中与另一个实体相关的实体的数量并不实际产生加载所有这些实体的成本是非常有用的。 带有 LINQ Count 方法的 Query 方法可用于执行此操作。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```  
