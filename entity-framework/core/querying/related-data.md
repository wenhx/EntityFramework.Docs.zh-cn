---
title: 加载相关数据 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 65cfea07a40939c1c3615c97ec785a4082b21de5
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994783"
---
# <a name="loading-related-data"></a><span data-ttu-id="0b490-102">加载相关数据</span><span class="sxs-lookup"><span data-stu-id="0b490-102">Loading Related Data</span></span>

<span data-ttu-id="0b490-103">Entity Framework Core 允许在模型中使用导航属性来加载关联实体。</span><span class="sxs-lookup"><span data-stu-id="0b490-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="0b490-104">有三种常见的 O/RM 模式可用于加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-104">There are three common O/RM patterns used to load related data.</span></span>
* <span data-ttu-id="0b490-105">“预先加载”表示从数据库中加载相关数据，作为初始查询的一部分。</span><span class="sxs-lookup"><span data-stu-id="0b490-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="0b490-106">“显式加载”表示稍后从数据库中显式加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="0b490-107">“延迟加载”表示在访问导航属性时，从数据库中以透明方式加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="0b490-108">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="0b490-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="0b490-109">预先加载</span><span class="sxs-lookup"><span data-stu-id="0b490-109">Eager loading</span></span>

<span data-ttu-id="0b490-110">可以使用 `Include` 方法来指定要包含在查询结果中的关联数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="0b490-111">在以下示例中，结果中返回的blogs将使用关联的posts填充其 `Posts` 属性。</span><span class="sxs-lookup"><span data-stu-id="0b490-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="0b490-112">Entity Framework Core 会根据之前已加载到上下文实例中的实体自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="0b490-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="0b490-113">因此，即使不显式包含导航属性的数据，如果先前加载了部分或所有关联实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="0b490-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>


<span data-ttu-id="0b490-114">可以在单个查询中包含多个关系的关联数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="0b490-115">包含多个层级</span><span class="sxs-lookup"><span data-stu-id="0b490-115">Including multiple levels</span></span>

<span data-ttu-id="0b490-116">使用 `ThenInclude` 方法可以依循关系包含多个层级的关联数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="0b490-117">以下示例加载了所有博客、其关联文章及每篇文章的作者。</span><span class="sxs-lookup"><span data-stu-id="0b490-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> <span data-ttu-id="0b490-118">当前版本的 Visual Studio 提供的代码完成选项不正确，因此在集合导航属性之后使用 `ThenInclude` 方法时，可能会对正确的表达式提示语法错误。</span><span class="sxs-lookup"><span data-stu-id="0b490-118">Current versions of Visual Studio offer incorrect code completion options and can cause correct expressions to be flagged with syntax errors when using the `ThenInclude` method after a collection navigation property.</span></span> <span data-ttu-id="0b490-119">这是 https://github.com/dotnet/roslyn/issues/8237 中跟踪的 IntelliSense bug 的一个特征。</span><span class="sxs-lookup"><span data-stu-id="0b490-119">This is a symptom of an IntelliSense bug tracked at https://github.com/dotnet/roslyn/issues/8237.</span></span> <span data-ttu-id="0b490-120">只要代码正确无误且能够成功编译，就可以放心地忽略这些语法错误。</span><span class="sxs-lookup"><span data-stu-id="0b490-120">It is safe to ignore these spurious syntax errors as long as the code is correct and can be compiled successfully.</span></span> 

<span data-ttu-id="0b490-121">可通过链式调用 `ThenInclude`，进一步包含更深级别的关联数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-121">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="0b490-122">可以将来自多个级别和多个根的关联数据合并到同一查询中。</span><span class="sxs-lookup"><span data-stu-id="0b490-122">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="0b490-123">你可能希望将已包含的某个实体的多个关联实体都包含进来。</span><span class="sxs-lookup"><span data-stu-id="0b490-123">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="0b490-124">例如，当查询 `Blog` 时，会包含 `Posts` ，然后希望同时包含 `Posts` 的 `Author` 和 `Tags`。</span><span class="sxs-lookup"><span data-stu-id="0b490-124">For example, when querying `Blog`s, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="0b490-125">为此，需要从根级别开始指定每个包含路径。</span><span class="sxs-lookup"><span data-stu-id="0b490-125">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="0b490-126">例如，`Blog -> Posts -> Author` 和 `Blog -> Posts -> Tags`。</span><span class="sxs-lookup"><span data-stu-id="0b490-126">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="0b490-127">这并不意味着会获得冗余联接查询，在大多数情况下，EF 会在生成 SQL 时合并相应的联接查询。</span><span class="sxs-lookup"><span data-stu-id="0b490-127">This does not mean you will get redundant joins, in most cases EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a><span data-ttu-id="0b490-128">派生类型上的包含</span><span class="sxs-lookup"><span data-stu-id="0b490-128">Include on derived types</span></span>

<span data-ttu-id="0b490-129">可以使用 `Include` 和 `ThenInclude` 包含仅在派生类型上定义的导航的关联数据。</span><span class="sxs-lookup"><span data-stu-id="0b490-129">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span> 

<span data-ttu-id="0b490-130">给定以下模型：</span><span class="sxs-lookup"><span data-stu-id="0b490-130">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="0b490-131">对于具有学生身份的所有人员，可使用多种方法来预先加载其 `School` 导航属性的内容：</span><span class="sxs-lookup"><span data-stu-id="0b490-131">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

- <span data-ttu-id="0b490-132">使用强制转换</span><span class="sxs-lookup"><span data-stu-id="0b490-132">using cast</span></span>
  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

- <span data-ttu-id="0b490-133">使用 `as` 运算符</span><span class="sxs-lookup"><span data-stu-id="0b490-133">using `as` operator</span></span>
  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

- <span data-ttu-id="0b490-134">使用 `Include` 的重载，该方法采用 `string` 类型的参数</span><span class="sxs-lookup"><span data-stu-id="0b490-134">using overload of `Include` that takes parameter of type `string`</span></span>
  ```csharp
  context.People.Include("Student").ToList()
  ```

### <a name="ignored-includes"></a><span data-ttu-id="0b490-135">忽略包含</span><span class="sxs-lookup"><span data-stu-id="0b490-135">Ignored includes</span></span>

<span data-ttu-id="0b490-136">如果更改查询，使其不再返回查询最开始的实体类型的实例，则会忽略 include 方法。</span><span class="sxs-lookup"><span data-stu-id="0b490-136">If you change the query so that it no longer returns instances of the entity type that the query began with, then the include operators are ignored.</span></span>

<span data-ttu-id="0b490-137">以下示例中，include 运算符基于 `Blog`，但 `Select` 运算符将查询改变为返回匿名类型。</span><span class="sxs-lookup"><span data-stu-id="0b490-137">In the following example, the include operators are based on the `Blog`, but then the `Select` operator is used to change the query to return an anonymous type.</span></span> <span data-ttu-id="0b490-138">在这种情况下，include 运算符不起作用。</span><span class="sxs-lookup"><span data-stu-id="0b490-138">In this case, the include operators have no effect.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

<span data-ttu-id="0b490-139">默认情况下，当忽略 include 运算符时，EF Core 将记录警告。</span><span class="sxs-lookup"><span data-stu-id="0b490-139">By default, EF Core will log a warning when include operators are ignored.</span></span> <span data-ttu-id="0b490-140">有关查看日志记录输出的详细信息，请参阅[日志记录](../miscellaneous/logging.md)。</span><span class="sxs-lookup"><span data-stu-id="0b490-140">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="0b490-141">可更改 include 运算符被忽略时的行为，比如引发异常或不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="0b490-141">You can change the behavior when an include operator is ignored to either throw or do nothing.</span></span> <span data-ttu-id="0b490-142">将在设置上下文选项时完成此操作（如果使用的是 ASP.NET Core，则通常在 `DbContext.OnConfiguring` 或 `Startup.cs` 中设置）。</span><span class="sxs-lookup"><span data-stu-id="0b490-142">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a><span data-ttu-id="0b490-143">显式加载</span><span class="sxs-lookup"><span data-stu-id="0b490-143">Explicit loading</span></span>

> [!NOTE]  
> <span data-ttu-id="0b490-144">EF Core 1.1 中已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="0b490-144">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="0b490-145">可以通过 `DbContext.Entry(...)` API 显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="0b490-145">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="0b490-146">还可以通过执行返回关联实体的单独查询来显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="0b490-146">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="0b490-147">如果已启用更改跟踪，则在加载实体时，EF Core 将自动设置新加载的实体的导航属性以引用任何已加载的实体，并设置已加载实体的导航属性以引用新加载的实体。</span><span class="sxs-lookup"><span data-stu-id="0b490-147">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="0b490-148">查询关联实体</span><span class="sxs-lookup"><span data-stu-id="0b490-148">Querying related entities</span></span>

<span data-ttu-id="0b490-149">还可以获得表示导航属性内容的 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="0b490-149">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="0b490-150">这样就能做到（譬如）无需将关联实体加载到内存中，就可以对关联实体执行聚合运算。</span><span class="sxs-lookup"><span data-stu-id="0b490-150">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="0b490-151">还可以筛选要加载到内存中的关联实体。</span><span class="sxs-lookup"><span data-stu-id="0b490-151">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="0b490-152">延迟加载</span><span class="sxs-lookup"><span data-stu-id="0b490-152">Lazy loading</span></span>

> [!NOTE]  
> <span data-ttu-id="0b490-153">EF Core 2.1 中已引入此功能。</span><span class="sxs-lookup"><span data-stu-id="0b490-153">This feature was introduced in EF Core 2.1.</span></span>

<span data-ttu-id="0b490-154">使用延迟加载的最简单方式是通过安装 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) 包，并通过调用 `UseLazyLoadingProxies` 来启用该包。</span><span class="sxs-lookup"><span data-stu-id="0b490-154">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="0b490-155">例如：</span><span class="sxs-lookup"><span data-stu-id="0b490-155">For example:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
<span data-ttu-id="0b490-156">或在使用 AddDbContext 时：</span><span class="sxs-lookup"><span data-stu-id="0b490-156">Or when using AddDbContext:</span></span>
```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```
<span data-ttu-id="0b490-157">EF Core 接着会为可重写的任何导航属性（即，必须是 `virtual` 且在可被继承的类上）启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="0b490-157">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="0b490-158">例如，在以下实体中，`Post.Blog` 和 `Blog.Posts` 导航属性将被延迟加载。</span><span class="sxs-lookup"><span data-stu-id="0b490-158">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>
```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```
### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="0b490-159">不使用代理的延迟加载</span><span class="sxs-lookup"><span data-stu-id="0b490-159">Lazy loading without proxies</span></span>

<span data-ttu-id="0b490-160">使用代理进行延迟加载的工作方式是将 `ILazyLoader` 注入到实体中，如[实体类型构造函数](../modeling/constructors.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="0b490-160">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="0b490-161">例如：</span><span class="sxs-lookup"><span data-stu-id="0b490-161">For example:</span></span>
```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
<span data-ttu-id="0b490-162">这不要求实体类型为可继承的类型，也不要求导航属性必须是虚拟的，且允许通过 `new` 创建的实体实例在附加到上下文后可进行延迟加载。</span><span class="sxs-lookup"><span data-stu-id="0b490-162">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="0b490-163">但它需要对 [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) 包中定义的 `ILazyLoader` 服务的引用。</span><span class="sxs-lookup"><span data-stu-id="0b490-163">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="0b490-164">此包包含所允许的最少的一组类型，以便将依赖此包时所产生的影响降至最低。</span><span class="sxs-lookup"><span data-stu-id="0b490-164">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="0b490-165">不过，可以将 `ILazyLoader.Load` 方法以委托的形式注入，这样就可以完全避免依赖于实体类型的任何 EF Core 包。</span><span class="sxs-lookup"><span data-stu-id="0b490-165">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="0b490-166">例如：</span><span class="sxs-lookup"><span data-stu-id="0b490-166">For example:</span></span>
```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
<span data-ttu-id="0b490-167">上述代码使用 `Load` 扩展方法，以便更干净地使用委托：</span><span class="sxs-lookup"><span data-stu-id="0b490-167">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>
```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```
> [!NOTE]  
> <span data-ttu-id="0b490-168">延迟加载委托的构造函数参数必须名为“lazyLoader”。</span><span class="sxs-lookup"><span data-stu-id="0b490-168">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="0b490-169">未来的一个版本中的配置将计划采用另一个名称。</span><span class="sxs-lookup"><span data-stu-id="0b490-169">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="0b490-170">关联数据和序列化</span><span class="sxs-lookup"><span data-stu-id="0b490-170">Related data and serialization</span></span>

<span data-ttu-id="0b490-171">由于 EF Core 会自动修正导航属性，因此在对象图中可能会产生循环引用。</span><span class="sxs-lookup"><span data-stu-id="0b490-171">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="0b490-172">例如，加载博客及其关联文章会生成引用文章集合的博客对象。</span><span class="sxs-lookup"><span data-stu-id="0b490-172">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="0b490-173">而其中每篇文章又会引用该博客。</span><span class="sxs-lookup"><span data-stu-id="0b490-173">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="0b490-174">某些序列化框架不允许使用循环引用。</span><span class="sxs-lookup"><span data-stu-id="0b490-174">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="0b490-175">例如，Json.NET 在产生循环引用的情况下，会引发以下异常。</span><span class="sxs-lookup"><span data-stu-id="0b490-175">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="0b490-176">Newtonsoft.Json.JsonSerializationException：为“MyApplication.Models.Blog”类型的“Blog”属性检测到自引用循环。</span><span class="sxs-lookup"><span data-stu-id="0b490-176">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="0b490-177">如果正在使用 ASP.NET Core，则可以将 Json.NET 配置为忽略在对象图中找到的循环引用。</span><span class="sxs-lookup"><span data-stu-id="0b490-177">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="0b490-178">这是在 `Startup.cs` 中通过 `ConfigureServices(...)` 方法实现的。</span><span class="sxs-lookup"><span data-stu-id="0b490-178">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```
