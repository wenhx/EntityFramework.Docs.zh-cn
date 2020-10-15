---
title: 具有构造函数的实体类型-EF Core
description: 使用构造函数将数据与 Entity Framework Core 模型绑定
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 9502d75072eebb80c37cf1805e21f7d112269ba1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063707"
---
# <a name="entity-types-with-constructors"></a>具有构造函数的实体类型

可以使用参数定义构造函数，并在创建实体实例时使 EF Core 调用此构造函数。 构造函数参数可以绑定到映射的属性或各种类型的服务，以促进延迟加载等行为。

> [!NOTE]
> 目前，所有构造函数绑定都按约定进行。 计划在将来的版本中配置要使用的特定构造函数。

## <a name="binding-to-mapped-properties"></a>绑定到映射的属性

请考虑典型的博客/公告模型：

```csharp
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

当 EF Core 创建这些类型的实例（例如，对于查询结果）时，它将首先调用默认的无参数构造函数，然后将每个属性设置为数据库中的值。 但是，如果 EF Core 查找参数名称和类型与映射的属性相匹配的参数化构造函数，则将改为使用这些属性的值调用参数化构造函数，并且不会显式设置每个属性。 例如：

```csharp
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

需要注意的事项：

* 并非所有属性都需要具有构造函数参数。 例如，后期内容属性不是由任何构造函数参数设置的，因此 EF Core 会在以正常方式调用构造函数后对其进行设置。
* 参数类型和名称必须与属性类型和名称相匹配，但在参数采用 camel 大小写格式时，属性可以采用 Pascal 大小写形式。
* EF Core 无法使用构造函数在) 以上的博客或文章 (设置导航属性。
* 构造函数可以是公共的，也可以是私有的，或者具有任何其他可访问性。 不过，延迟加载代理要求构造函数可从继承代理类访问。 通常，这意味着将其设为公共或受保护。

### <a name="read-only-properties"></a>只读属性

通过构造函数设置属性后，就可以使某些属性成为只读的。 EF Core 支持此功能，但需要注意以下事项：

* 不会按约定映射没有 setter 的属性。  (这样做通常是为了映射不应映射的属性，如计算属性。 ) 
* 使用自动生成的键值需要键属性，该属性是读写的，因为在插入新实体时密钥生成器需要设置密钥值。

避免这种情况的简单方法是使用专用资源库。 例如：

```csharp
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

EF Core 会看到一个具有专用资源库的属性为读写属性，这意味着所有属性都将像以前一样映射，并且该密钥仍可存储生成。

使用专用资源库的一种替代方法是使属性真正为只读，并在 OnModelCreating 中添加更多显式映射。 同样，可以完全删除某些属性并仅替换为字段。 以下面这些实体类型为例：

```csharp
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

OnModelCreating 中的此配置：

```csharp
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

注意事项：

* "属性" 键现在是字段。 它不是一个 `readonly` 字段，因此可以使用存储生成的键。
* 其他属性是仅在构造函数中设置的只读属性。
* 如果主键值只是由 EF 设置或从数据库中读取，则无需将其包含在构造函数中。 这会使 "属性" 键作为一个简单字段，并清楚地指出不应在创建新的博客或文章时显式设置。

> [!NOTE]
> 此代码将导致编译器警告 "169"，指示该字段从未使用过。 这可能会被忽略，因为在现实中 EF Core 以 extralinguistic 的方式使用该字段。

## <a name="injecting-services"></a>注入服务

EF Core 还可以将 "服务" 注入到实体类型的构造函数中。 例如，可以注入以下内容：

* `DbContext` -当前上下文实例，也可以类型化为派生的 DbContext 类型
* `ILazyLoader`-延迟加载服务-有关更多详细信息，请参阅[延迟加载文档](xref:core/querying/related-data)
* `Action<object, string>`-延迟加载委托-有关更多详细信息，请参阅[延迟加载文档](xref:core/querying/related-data)
* `IEntityType` -与此实体类型关联的 EF Core 元数据

> [!NOTE]
> 目前，只能注入 EF Core 已知的服务。 将来的版本会考虑对注入应用程序服务的支持。

例如，注入的 DbContext 可用于有选择地访问数据库，以获取相关实体的相关信息，而无需将它们全部加载。 在下面的示例中，这用于在不加载帖子的情况下获取博客中的帖子数：

```csharp
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

请注意以下几点：

* 构造函数是私有的，因为它只是由 EF Core 调用的，另一个公共构造函数用于常规用途。
* 使用注入的服务的代码 (即，上下文) 防御 `null` 处理 EF Core 不创建实例的情况。
* 因为服务存储在读/写属性中，所以当将实体附加到新的上下文实例时，它将被重置。

> [!WARNING]
> 注入此类 DbContext 通常被视为一种反模式，因为它会将您的实体类型直接耦合到 EF Core。 使用此类服务注入之前，请仔细考虑所有选项。
