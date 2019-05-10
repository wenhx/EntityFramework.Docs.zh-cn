---
title: 使用构造函数的 EF Core 的实体类型
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 5bf49718f02c1860871b1f4c255ec4d98fce2fc7
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405249"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="d5095-102">使用构造函数的实体类型</span><span class="sxs-lookup"><span data-stu-id="d5095-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="d5095-103">此功能是在 EF Core 2.1 的新增功能。</span><span class="sxs-lookup"><span data-stu-id="d5095-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="d5095-104">从开始 EF Core 2.1，它则现在可以定义参数的构造函数，并让 EF Core 创建实体的实例时调用此构造函数。</span><span class="sxs-lookup"><span data-stu-id="d5095-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="d5095-105">构造函数参数可以绑定到映射的属性，或到各种类型的服务，以促进行为 like 延迟加载。</span><span class="sxs-lookup"><span data-stu-id="d5095-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="d5095-106">截至 EF Core 2.1，按照约定将为所有构造函数绑定。</span><span class="sxs-lookup"><span data-stu-id="d5095-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="d5095-107">配置特定的构造函数使用计划在将来的版本。</span><span class="sxs-lookup"><span data-stu-id="d5095-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="d5095-108">绑定到映射的属性</span><span class="sxs-lookup"><span data-stu-id="d5095-108">Binding to mapped properties</span></span>

<span data-ttu-id="d5095-109">请考虑一个典型博客/文章模型：</span><span class="sxs-lookup"><span data-stu-id="d5095-109">Consider a typical Blog/Post model:</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="d5095-110">当 EF Core 创建这些类型的实例时，如的结果的查询，它将首先调用默认的无参数构造函数，然后设置每个属性的值从数据库。</span><span class="sxs-lookup"><span data-stu-id="d5095-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="d5095-111">但是，如果 EF Core 查找的参数化构造函数参数名称和类型相匹配的映射属性，则它将改为调用这些属性具有值的参数化构造函数，然后将未显式设置每个属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="d5095-112">例如：</span><span class="sxs-lookup"><span data-stu-id="d5095-112">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="d5095-113">需要注意一些事项：</span><span class="sxs-lookup"><span data-stu-id="d5095-113">Some things to note:</span></span>
* <span data-ttu-id="d5095-114">需要具有构造函数参数不是所有属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="d5095-115">例如，由任何构造函数参数，因此 EF Core 将以正常方式调用的构造函数后设置未设置 Post.Content 属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="d5095-116">参数类型和名称必须与匹配的属性类型和名称，只不过属性可以是 Pascal 大小写而参数 camel 大小写。</span><span class="sxs-lookup"><span data-stu-id="d5095-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="d5095-117">无法设置 （如博客或更高版本的文章） 的导航属性，EF Core 使用构造函数。</span><span class="sxs-lookup"><span data-stu-id="d5095-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="d5095-118">构造函数可以是公共、 私有的或具有任何其他可访问性。</span><span class="sxs-lookup"><span data-stu-id="d5095-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="d5095-119">但是，延迟加载代理要求的构造函数是可从继承的代理类进行访问。</span><span class="sxs-lookup"><span data-stu-id="d5095-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="d5095-120">通常这意味着使其公共或受保护。</span><span class="sxs-lookup"><span data-stu-id="d5095-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="d5095-121">只读属性</span><span class="sxs-lookup"><span data-stu-id="d5095-121">Read-only properties</span></span>

<span data-ttu-id="d5095-122">通过构造函数中设置属性之后它以使其中一些只读可以很有意义。</span><span class="sxs-lookup"><span data-stu-id="d5095-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="d5095-123">EF Core 支持此功能，但有一些需要注意的事项：</span><span class="sxs-lookup"><span data-stu-id="d5095-123">EF Core supports this, but there are some things to look out for:</span></span>
* <span data-ttu-id="d5095-124">按照约定未映射不包含 setter 的属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="d5095-125">（执行此操作通常不应将映射，如计算属性的属性映射。）</span><span class="sxs-lookup"><span data-stu-id="d5095-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="d5095-126">使用自动生成的密钥值需要是读写的因为需要插入新实体时，密钥生成器设置密钥值的键属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="d5095-127">避免这些事情的简单方法是使用私有资源库。</span><span class="sxs-lookup"><span data-stu-id="d5095-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="d5095-128">例如：</span><span class="sxs-lookup"><span data-stu-id="d5095-128">For example:</span></span>
``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="d5095-129">EF Core 看到为读写模式，这意味着，像以前那样映射所有属性并密钥可能仍会由存储生成的具有专用 setter 的属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="d5095-130">使用私有资源库的替代方法是使属性实际上是只读的并在 OnModelCreating 中添加更多显式映射。</span><span class="sxs-lookup"><span data-stu-id="d5095-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="d5095-131">同样，某些属性可以彻底删除并替换为只有字段。</span><span class="sxs-lookup"><span data-stu-id="d5095-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="d5095-132">例如，考虑这些实体类型：</span><span class="sxs-lookup"><span data-stu-id="d5095-132">For example, consider these entity types:</span></span>

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="d5095-133">与此配置在 OnModelCreating 中：</span><span class="sxs-lookup"><span data-stu-id="d5095-133">And this configuration in OnModelCreating:</span></span>
``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```
<span data-ttu-id="d5095-134">需要注意的事项：</span><span class="sxs-lookup"><span data-stu-id="d5095-134">Things to note:</span></span>
* <span data-ttu-id="d5095-135">键"属性"现在是一个字段。</span><span class="sxs-lookup"><span data-stu-id="d5095-135">The key "property" is now a field.</span></span> <span data-ttu-id="d5095-136">它不是`readonly`字段，以便可以使用应用商店生成的键。</span><span class="sxs-lookup"><span data-stu-id="d5095-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="d5095-137">其他属性是只读的只能在构造函数中设置的属性。</span><span class="sxs-lookup"><span data-stu-id="d5095-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="d5095-138">如果主键值是只通过 EF 设置或从数据库读取，则无需将其包括在构造函数。</span><span class="sxs-lookup"><span data-stu-id="d5095-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="d5095-139">这为简单字段离开密钥"属性"，并使用户清楚地知道，它不应设置显式创建新的博客或帖子时。</span><span class="sxs-lookup"><span data-stu-id="d5095-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="d5095-140">此代码将导致编译器警告"169"，永远不会使用该字段指示。</span><span class="sxs-lookup"><span data-stu-id="d5095-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="d5095-141">由于在现实中 EF Core extralinguistic 的方式使用该字段，这可予以忽视。</span><span class="sxs-lookup"><span data-stu-id="d5095-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="d5095-142">注入服务</span><span class="sxs-lookup"><span data-stu-id="d5095-142">Injecting services</span></span>

<span data-ttu-id="d5095-143">EF Core 还可以将"服务"注入到实体类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="d5095-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="d5095-144">例如，下面可以注入：</span><span class="sxs-lookup"><span data-stu-id="d5095-144">For example, the following can be injected:</span></span>
* <span data-ttu-id="d5095-145">`DbContext` -当前上下文实例，也可以作为派生 DbContext 类型类型化</span><span class="sxs-lookup"><span data-stu-id="d5095-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="d5095-146">`ILazyLoader` -延迟加载服务中--请参阅[延迟加载文档](../querying/related-data.md)的更多详细信息</span><span class="sxs-lookup"><span data-stu-id="d5095-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="d5095-147">`Action<object, string>` -延迟加载委托-请参阅[延迟加载文档](../querying/related-data.md)的更多详细信息</span><span class="sxs-lookup"><span data-stu-id="d5095-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="d5095-148">`IEntityType` -与此实体类型关联的 EF Core 元数据</span><span class="sxs-lookup"><span data-stu-id="d5095-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="d5095-149">截至 EF Core 2.1，可插入仅通过 EF Core 已知的服务。</span><span class="sxs-lookup"><span data-stu-id="d5095-149">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="d5095-150">注入应用程序服务的支持正在考虑针对将来的版本。</span><span class="sxs-lookup"><span data-stu-id="d5095-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="d5095-151">例如，注入的 DbContext 可用于有选择地访问数据库以获取有关相关实体的信息不加载所有这些情况下。</span><span class="sxs-lookup"><span data-stu-id="d5095-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="d5095-152">在下面的示例这用于获取在博客中的帖子数而不加载发布的文章：</span><span class="sxs-lookup"><span data-stu-id="d5095-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```
<span data-ttu-id="d5095-153">请注意，有关这几个注意事项：</span><span class="sxs-lookup"><span data-stu-id="d5095-153">A few things to notice about this:</span></span>
* <span data-ttu-id="d5095-154">构造函数是专用容器，因为它只能由 EF Core，并且没有用于常规用途的另一个公共构造函数。</span><span class="sxs-lookup"><span data-stu-id="d5095-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="d5095-155">使用注入的服务 （上下文） 的代码是针对该防御性正在`null`处理情况下，其中 EF Core 不创建实例。</span><span class="sxs-lookup"><span data-stu-id="d5095-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="d5095-156">由于服务存储在读/写属性将重置时该实体附加到新的上下文实例。</span><span class="sxs-lookup"><span data-stu-id="d5095-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="d5095-157">因为它将直接与 EF Core 的实体类型，将注入如下 DbContext 通常被视为反模式。</span><span class="sxs-lookup"><span data-stu-id="d5095-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="d5095-158">使用此类服务注入之前仔细考虑所有选项。</span><span class="sxs-lookup"><span data-stu-id="d5095-158">Carefully consider all options before using service injection like this.</span></span>
