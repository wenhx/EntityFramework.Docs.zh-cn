---
title: 本地数据-EF6
description: 实体框架6中的本地数据
author: divega
ms.date: 10/23/2016
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
uid: ef6/querying/local-data
ms.openlocfilehash: f7c4c8904a2985901491e423f655d4aea79f666d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620308"
---
# <a name="local-data"></a>本地数据
直接对 DbSet 运行 LINQ 查询将始终向数据库发送查询，但你可以使用 DbSet 属性访问当前在内存中的数据。 使用 DbContext 和 DbContext 方法，还可以访问额外的信息 EF 正在跟踪实体。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

## <a name="using-local-to-look-at-local-data"></a>使用本地查看本地数据  

DbSet 的 Local 属性提供对当前正在由上下文跟踪并且未标记为已删除的集的实体的简单访问。 访问本地属性绝不会使查询发送到数据库。 这意味着它通常在查询已执行后使用。 负载扩展方法可用于执行查询，以便上下文可以跟踪结果。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

如果数据库中有两个博客-"ADO.NET Blog" 的 BlogId 为1，"Visual Studio 博客" 的 BlogId 为2，则可能会收到以下输出：  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

这说明了三个要点：  

- 新博客的 "我的新博客" 包含在本地集合中，即使尚未将其保存到数据库中。 此博客的主键为零，因为数据库尚未生成实体的实际密钥。  
- 即使上下文仍在跟踪 "ADO.NET Blog"，也不会将其包含在本地集合中。 这是因为我们从 DbSet 中删除了它，从而将其标记为已删除。  
- 当使用 DbSet 来执行查询时，将在结果中包含标记为删除 (ADO.NET 博客) 的博客，而新的博客 (尚未保存到数据库中的新博客) 将不包含在结果中。 这是因为，DbSet 正在对数据库执行查询，并且返回的结果始终反映了数据库中的内容。  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>使用本地来添加和删除上下文中的实体  

DbSet 上的本地属性返回一个 [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) ，其中包含与该上下文的内容保持同步的事件。 这意味着可以在本地集合或 DbSet 中添加或移除实体。 这也意味着，将新实体引入上下文的查询将导致用这些实体更新本地集合。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

假设我们有几个标记有 "entity-框架" 和 "asp.net" 的文章，输出可能如下所示：  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

这说明了三个要点：  

- 添加到本地集合中的新 post "EF 新增功能" 将由上下文在已添加状态中进行跟踪。 因此，当调用 SaveChanges 时，它将被插入到数据库中。  
- 从本地集合中删除的 post (EF 初学者指南) 现在在上下文中标记为已删除。 因此，当调用 SaveChanges 时，将从数据库中删除该方法。  
- 额外的 post (ASP.NET 初学者指南) 加载到上下文中，第二个查询会自动添加到本地集合中。  

关于本地需要注意的最后一点是，因为这是一个 ObservableCollection 的性能，不适用于大量实体。 因此，如果你在上下文中处理数千个实体，则可能不建议使用本地。  

## <a name="using-local-for-wpf-data-binding"></a>使用本地进行 WPF 数据绑定  

DbSet 上的本地属性可以直接用于 WPF 应用程序中的数据绑定，因为它是 ObservableCollection 的实例。 如前面部分所述，这意味着它将与上下文内容自动保持同步，并且上下文的内容将自动与它保持同步。 请注意，您需要预先填充本地集合，其中包含的数据可以绑定到任何内容，因为本地从不会导致数据库查询。  

这不是完整的 WPF 数据绑定示例的合适位置，但关键元素是：  

- 设置绑定源  
- 将其绑定到集的本地属性  
- 使用数据库的查询填充本地。  

## <a name="wpf-binding-to-navigation-properties"></a>WPF 绑定到导航属性  

如果要执行主/详细数据绑定，则可能需要将详细信息视图绑定到某个实体的导航属性。 若要执行此操作，一种简单的方法是将 ObservableCollection 用于导航属性。 例如：  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>使用 Local 在 SaveChanges 中清理实体  

在大多数情况下，从导航属性中删除的实体在上下文中不会自动标记为 "已删除"。 例如，如果从博客发布了一个 Post 对象，则在调用 SaveChanges 后，将不会自动删除此 post。 如果需要删除此实体，则可能需要在调用 SaveChanges 之前，或将其标记为已删除，然后再将其标记为已删除。 例如：  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

上面的代码使用本地集合查找所有帖子，并将没有博客引用的任何文章标记为已删除。 System.linq.enumerable.tolist 调用是必需的，因为在枚举时，删除调用会对集合进行修改。 在大多数其他情况下，您可以直接对本地属性进行查询，而不先使用 System.linq.enumerable.tolist。  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>为 Windows 窗体数据绑定使用本地和对 tobindinglist 获得  

Windows 窗体不支持直接使用 ObservableCollection 的完全保真数据绑定。 不过，你仍然可以使用 DbSet 本地属性进行数据绑定，以获得前面几节中所述的全部权益。 这是通过对 tobindinglist 获得扩展方法实现的，该方法创建由本地 ObservableCollection 支持的 [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) 实现。  

此位置不适合完全 Windows 窗体的数据绑定示例，但关键元素是：  

- 设置对象绑定源  
- 使用对 tobindinglist 获得将其绑定到集的本地属性 ( # A1  
- 使用数据库的查询填充本地  

## <a name="getting-detailed-information-about-tracked-entities"></a>获取有关被跟踪实体的详细信息  

此系列中的许多示例使用 Entry 方法返回实体的 DbEntityEntry 实例。 然后，此 entry 对象充当用于收集有关实体的信息（如当前状态）的起始点，以及用于对实体执行操作（如显式加载相关实体）的起始点。  

条目方法为上下文跟踪的多个或所有实体返回 DbEntityEntry 对象。 这允许您收集信息或对多个实体执行操作，而不是只收集单个条目。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

你会注意到，我们在示例中引入了一个作者和读者类，这两个类都实现 IPerson 接口。  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

假设数据库中包含以下数据：

BlogId = 1 和 Name = ' ADO.NET Blog ' 的博客  
BlogId = 2 的博客和名称 = ' Visual Studio 博客 '  
BlogId = 3 且名称为 ".NET Framework 博客" 的博客  
作者为 AuthorId = 1，名称 = "Joe Bloggs"  
ReaderId = 1 且名称 = "John Doe" 的读取器  

运行代码的输出为：  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

这些示例阐释了几个要点：  

- 条目方法返回所有状态中实体的条目，包括 "已删除"。 将此与本地（不包括已删除的实体）进行比较。  
- 当使用非泛型条目方法时，将返回所有实体类型的条目。 当使用泛型条目方法时，仅为作为泛型类型的实例的实体返回条目。 上述内容用于获取所有博客的条目。 它还用于获取实现 IPerson 的所有实体的条目。 这表明泛型类型不一定是实际的实体类型。  
- LINQ to Objects 可用于筛选返回的结果。 在上述情况下，只要修改了任何类型的实体，就可以找到它。  

请注意，DbEntityEntry 实例始终包含非 null 的实体。 关系项和存根项不表示为 DbEntityEntry 实例，因此无需对其进行筛选。
