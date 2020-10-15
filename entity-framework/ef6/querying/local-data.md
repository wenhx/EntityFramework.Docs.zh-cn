---
title: 本地数据-EF6
description: 实体框架6中的本地数据
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: cff3612072ad9caa057d8589d47cc75ae32be8db
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065870"
---
# <a name="local-data"></a><span data-ttu-id="a1e67-103">本地数据</span><span class="sxs-lookup"><span data-stu-id="a1e67-103">Local Data</span></span>
<span data-ttu-id="a1e67-104">直接对 DbSet 运行 LINQ 查询将始终向数据库发送查询，但你可以使用 DbSet 属性访问当前在内存中的数据。</span><span class="sxs-lookup"><span data-stu-id="a1e67-104">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="a1e67-105">使用 DbContext 和 DbContext 方法，还可以访问额外的信息 EF 正在跟踪实体。</span><span class="sxs-lookup"><span data-stu-id="a1e67-105">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="a1e67-106">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="a1e67-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="a1e67-107">使用本地查看本地数据</span><span class="sxs-lookup"><span data-stu-id="a1e67-107">Using Local to look at local data</span></span>  

<span data-ttu-id="a1e67-108">DbSet 的 Local 属性提供对当前正在由上下文跟踪并且未标记为已删除的集的实体的简单访问。</span><span class="sxs-lookup"><span data-stu-id="a1e67-108">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="a1e67-109">访问本地属性绝不会使查询发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="a1e67-109">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="a1e67-110">这意味着它通常在查询已执行后使用。</span><span class="sxs-lookup"><span data-stu-id="a1e67-110">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="a1e67-111">负载扩展方法可用于执行查询，以便上下文可以跟踪结果。</span><span class="sxs-lookup"><span data-stu-id="a1e67-111">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="a1e67-112">例如：</span><span class="sxs-lookup"><span data-stu-id="a1e67-112">For example:</span></span>  

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

<span data-ttu-id="a1e67-113">如果数据库中有两个博客-"ADO.NET Blog" 的 BlogId 为1，"Visual Studio 博客" 的 BlogId 为2，则可能会收到以下输出：</span><span class="sxs-lookup"><span data-stu-id="a1e67-113">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="a1e67-114">这说明了三个要点：</span><span class="sxs-lookup"><span data-stu-id="a1e67-114">This illustrates three points:</span></span>  

- <span data-ttu-id="a1e67-115">新博客的 "我的新博客" 包含在本地集合中，即使尚未将其保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="a1e67-115">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="a1e67-116">此博客的主键为零，因为数据库尚未生成实体的实际密钥。</span><span class="sxs-lookup"><span data-stu-id="a1e67-116">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="a1e67-117">即使上下文仍在跟踪 "ADO.NET Blog"，也不会将其包含在本地集合中。</span><span class="sxs-lookup"><span data-stu-id="a1e67-117">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="a1e67-118">这是因为我们从 DbSet 中删除了它，从而将其标记为已删除。</span><span class="sxs-lookup"><span data-stu-id="a1e67-118">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="a1e67-119">当使用 DbSet 来执行查询时，将在结果中包含标记为删除 (ADO.NET 博客) 的博客，而新的博客 (尚未保存到数据库中的新博客) 将不包含在结果中。</span><span class="sxs-lookup"><span data-stu-id="a1e67-119">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="a1e67-120">这是因为，DbSet 正在对数据库执行查询，并且返回的结果始终反映了数据库中的内容。</span><span class="sxs-lookup"><span data-stu-id="a1e67-120">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="a1e67-121">使用本地来添加和删除上下文中的实体</span><span class="sxs-lookup"><span data-stu-id="a1e67-121">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="a1e67-122">DbSet 上的本地属性返回一个 [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) ，其中包含与该上下文的内容保持同步的事件。</span><span class="sxs-lookup"><span data-stu-id="a1e67-122">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="a1e67-123">这意味着可以在本地集合或 DbSet 中添加或移除实体。</span><span class="sxs-lookup"><span data-stu-id="a1e67-123">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="a1e67-124">这也意味着，将新实体引入上下文的查询将导致用这些实体更新本地集合。</span><span class="sxs-lookup"><span data-stu-id="a1e67-124">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="a1e67-125">例如：</span><span class="sxs-lookup"><span data-stu-id="a1e67-125">For example:</span></span>  

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

<span data-ttu-id="a1e67-126">假设我们有几个标记有 "entity-框架" 和 "asp.net" 的文章，输出可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="a1e67-126">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

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

<span data-ttu-id="a1e67-127">这说明了三个要点：</span><span class="sxs-lookup"><span data-stu-id="a1e67-127">This illustrates three points:</span></span>  

- <span data-ttu-id="a1e67-128">添加到本地集合中的新 post "EF 新增功能" 将由上下文在已添加状态中进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="a1e67-128">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="a1e67-129">因此，当调用 SaveChanges 时，它将被插入到数据库中。</span><span class="sxs-lookup"><span data-stu-id="a1e67-129">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="a1e67-130">从本地集合中删除的 post (EF 初学者指南) 现在在上下文中标记为已删除。</span><span class="sxs-lookup"><span data-stu-id="a1e67-130">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="a1e67-131">因此，当调用 SaveChanges 时，将从数据库中删除该方法。</span><span class="sxs-lookup"><span data-stu-id="a1e67-131">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="a1e67-132">额外的 post (ASP.NET 初学者指南) 加载到上下文中，第二个查询会自动添加到本地集合中。</span><span class="sxs-lookup"><span data-stu-id="a1e67-132">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="a1e67-133">关于本地需要注意的最后一点是，因为这是一个 ObservableCollection 的性能，不适用于大量实体。</span><span class="sxs-lookup"><span data-stu-id="a1e67-133">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="a1e67-134">因此，如果你在上下文中处理数千个实体，则可能不建议使用本地。</span><span class="sxs-lookup"><span data-stu-id="a1e67-134">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="a1e67-135">使用本地进行 WPF 数据绑定</span><span class="sxs-lookup"><span data-stu-id="a1e67-135">Using Local for WPF data binding</span></span>  

<span data-ttu-id="a1e67-136">DbSet 上的本地属性可以直接用于 WPF 应用程序中的数据绑定，因为它是 ObservableCollection 的实例。</span><span class="sxs-lookup"><span data-stu-id="a1e67-136">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="a1e67-137">如前面部分所述，这意味着它将与上下文内容自动保持同步，并且上下文的内容将自动与它保持同步。</span><span class="sxs-lookup"><span data-stu-id="a1e67-137">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="a1e67-138">请注意，您需要预先填充本地集合，其中包含的数据可以绑定到任何内容，因为本地从不会导致数据库查询。</span><span class="sxs-lookup"><span data-stu-id="a1e67-138">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="a1e67-139">这不是完整的 WPF 数据绑定示例的合适位置，但关键元素是：</span><span class="sxs-lookup"><span data-stu-id="a1e67-139">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="a1e67-140">设置绑定源</span><span class="sxs-lookup"><span data-stu-id="a1e67-140">Setup a binding source</span></span>  
- <span data-ttu-id="a1e67-141">将其绑定到集的本地属性</span><span class="sxs-lookup"><span data-stu-id="a1e67-141">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="a1e67-142">使用数据库的查询填充本地。</span><span class="sxs-lookup"><span data-stu-id="a1e67-142">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="a1e67-143">WPF 绑定到导航属性</span><span class="sxs-lookup"><span data-stu-id="a1e67-143">WPF binding to navigation properties</span></span>  

<span data-ttu-id="a1e67-144">如果要执行主/详细数据绑定，则可能需要将详细信息视图绑定到某个实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="a1e67-144">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="a1e67-145">若要执行此操作，一种简单的方法是将 ObservableCollection 用于导航属性。</span><span class="sxs-lookup"><span data-stu-id="a1e67-145">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="a1e67-146">例如：</span><span class="sxs-lookup"><span data-stu-id="a1e67-146">For example:</span></span>  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="a1e67-147">使用 Local 在 SaveChanges 中清理实体</span><span class="sxs-lookup"><span data-stu-id="a1e67-147">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="a1e67-148">在大多数情况下，从导航属性中删除的实体在上下文中不会自动标记为 "已删除"。</span><span class="sxs-lookup"><span data-stu-id="a1e67-148">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="a1e67-149">例如，如果从博客发布了一个 Post 对象，则在调用 SaveChanges 后，将不会自动删除此 post。</span><span class="sxs-lookup"><span data-stu-id="a1e67-149">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="a1e67-150">如果需要删除此实体，则可能需要在调用 SaveChanges 之前，或将其标记为已删除，然后再将其标记为已删除。</span><span class="sxs-lookup"><span data-stu-id="a1e67-150">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="a1e67-151">例如：</span><span class="sxs-lookup"><span data-stu-id="a1e67-151">For example:</span></span>  

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

<span data-ttu-id="a1e67-152">上面的代码使用本地集合查找所有帖子，并将没有博客引用的任何文章标记为已删除。</span><span class="sxs-lookup"><span data-stu-id="a1e67-152">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="a1e67-153">System.linq.enumerable.tolist 调用是必需的，因为在枚举时，删除调用会对集合进行修改。</span><span class="sxs-lookup"><span data-stu-id="a1e67-153">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="a1e67-154">在大多数其他情况下，您可以直接对本地属性进行查询，而不先使用 System.linq.enumerable.tolist。</span><span class="sxs-lookup"><span data-stu-id="a1e67-154">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="a1e67-155">为 Windows 窗体数据绑定使用本地和对 tobindinglist 获得</span><span class="sxs-lookup"><span data-stu-id="a1e67-155">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="a1e67-156">Windows 窗体不支持直接使用 ObservableCollection 的完全保真数据绑定。</span><span class="sxs-lookup"><span data-stu-id="a1e67-156">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="a1e67-157">不过，你仍然可以使用 DbSet 本地属性进行数据绑定，以获得前面几节中所述的全部权益。</span><span class="sxs-lookup"><span data-stu-id="a1e67-157">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="a1e67-158">这是通过对 tobindinglist 获得扩展方法实现的，该方法创建由本地 ObservableCollection 支持的 [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) 实现。</span><span class="sxs-lookup"><span data-stu-id="a1e67-158">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="a1e67-159">此位置不适合完全 Windows 窗体的数据绑定示例，但关键元素是：</span><span class="sxs-lookup"><span data-stu-id="a1e67-159">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="a1e67-160">设置对象绑定源</span><span class="sxs-lookup"><span data-stu-id="a1e67-160">Setup an object binding source</span></span>  
- <span data-ttu-id="a1e67-161">使用对 tobindinglist 获得将其绑定到集的本地属性 ( # A1</span><span class="sxs-lookup"><span data-stu-id="a1e67-161">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="a1e67-162">使用数据库的查询填充本地</span><span class="sxs-lookup"><span data-stu-id="a1e67-162">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="a1e67-163">获取有关被跟踪实体的详细信息</span><span class="sxs-lookup"><span data-stu-id="a1e67-163">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="a1e67-164">此系列中的许多示例使用 Entry 方法返回实体的 DbEntityEntry 实例。</span><span class="sxs-lookup"><span data-stu-id="a1e67-164">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="a1e67-165">然后，此 entry 对象充当用于收集有关实体的信息（如当前状态）的起始点，以及用于对实体执行操作（如显式加载相关实体）的起始点。</span><span class="sxs-lookup"><span data-stu-id="a1e67-165">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="a1e67-166">条目方法为上下文跟踪的多个或所有实体返回 DbEntityEntry 对象。</span><span class="sxs-lookup"><span data-stu-id="a1e67-166">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="a1e67-167">这允许您收集信息或对多个实体执行操作，而不是只收集单个条目。</span><span class="sxs-lookup"><span data-stu-id="a1e67-167">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="a1e67-168">例如：</span><span class="sxs-lookup"><span data-stu-id="a1e67-168">For example:</span></span>  

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

<span data-ttu-id="a1e67-169">你会注意到，我们在示例中引入了一个作者和读者类，这两个类都实现 IPerson 接口。</span><span class="sxs-lookup"><span data-stu-id="a1e67-169">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

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

<span data-ttu-id="a1e67-170">假设数据库中包含以下数据：</span><span class="sxs-lookup"><span data-stu-id="a1e67-170">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="a1e67-171">BlogId = 1 和 Name = ' ADO.NET Blog ' 的博客</span><span class="sxs-lookup"><span data-stu-id="a1e67-171">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="a1e67-172">BlogId = 2 的博客和名称 = ' Visual Studio 博客 '</span><span class="sxs-lookup"><span data-stu-id="a1e67-172">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="a1e67-173">BlogId = 3 且名称为 ".NET Framework 博客" 的博客</span><span class="sxs-lookup"><span data-stu-id="a1e67-173">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="a1e67-174">作者为 AuthorId = 1，名称 = "Joe Bloggs"</span><span class="sxs-lookup"><span data-stu-id="a1e67-174">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="a1e67-175">ReaderId = 1 且名称 = "John Doe" 的读取器</span><span class="sxs-lookup"><span data-stu-id="a1e67-175">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="a1e67-176">运行代码的输出为：</span><span class="sxs-lookup"><span data-stu-id="a1e67-176">The output from running the code would be:</span></span>  

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

<span data-ttu-id="a1e67-177">这些示例阐释了几个要点：</span><span class="sxs-lookup"><span data-stu-id="a1e67-177">These examples illustrate several points:</span></span>  

- <span data-ttu-id="a1e67-178">条目方法返回所有状态中实体的条目，包括 "已删除"。</span><span class="sxs-lookup"><span data-stu-id="a1e67-178">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="a1e67-179">将此与本地（不包括已删除的实体）进行比较。</span><span class="sxs-lookup"><span data-stu-id="a1e67-179">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="a1e67-180">当使用非泛型条目方法时，将返回所有实体类型的条目。</span><span class="sxs-lookup"><span data-stu-id="a1e67-180">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="a1e67-181">当使用泛型条目方法时，仅为作为泛型类型的实例的实体返回条目。</span><span class="sxs-lookup"><span data-stu-id="a1e67-181">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="a1e67-182">上述内容用于获取所有博客的条目。</span><span class="sxs-lookup"><span data-stu-id="a1e67-182">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="a1e67-183">它还用于获取实现 IPerson 的所有实体的条目。</span><span class="sxs-lookup"><span data-stu-id="a1e67-183">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="a1e67-184">这表明泛型类型不一定是实际的实体类型。</span><span class="sxs-lookup"><span data-stu-id="a1e67-184">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="a1e67-185">LINQ to Objects 可用于筛选返回的结果。</span><span class="sxs-lookup"><span data-stu-id="a1e67-185">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="a1e67-186">在上述情况下，只要修改了任何类型的实体，就可以找到它。</span><span class="sxs-lookup"><span data-stu-id="a1e67-186">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="a1e67-187">请注意，DbEntityEntry 实例始终包含非 null 的实体。</span><span class="sxs-lookup"><span data-stu-id="a1e67-187">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="a1e67-188">关系项和存根项不表示为 DbEntityEntry 实例，因此无需对其进行筛选。</span><span class="sxs-lookup"><span data-stu-id="a1e67-188">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
