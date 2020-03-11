---
title: 使用构造函数的 EF Core 的实体类型
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: ddfaa8eebde388a9d3309f21b8891de593077956
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414649"
---
# <a name="entity-types-with-constructors"></a>具有构造函数的实体类型

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新增功能。

从开始 EF Core 2.1，它则现在可以定义参数的构造函数，并让 EF Core 创建实体的实例时调用此构造函数。 构造函数参数可以绑定到映射的属性或各种类型的服务，以促进延迟加载等行为。

> [!NOTE]  
> 截至 EF Core 2.1，按照约定将为所有构造函数绑定。 计划在将来的版本中配置要使用的特定构造函数。

## <a name="binding-to-mapped-properties"></a>绑定到映射的属性

请考虑典型的博客/公告模型：

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

当 EF Core 创建这些类型的实例时，如的结果的查询，它将首先调用默认的无参数构造函数，然后设置每个属性的值从数据库。 但是，如果 EF Core 查找的参数化构造函数参数名称和类型相匹配的映射属性，则它将改为调用这些属性具有值的参数化构造函数，然后将未显式设置每个属性。 例如：

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

需要注意的事项：

* 并非所有属性都需要具有构造函数参数。 例如，由任何构造函数参数，因此 EF Core 将以正常方式调用的构造函数后设置未设置 Post.Content 属性。
* 参数类型和名称必须与属性类型和名称相匹配，但在参数采用 camel 大小写格式时，属性可以采用 Pascal 大小写形式。
* 无法设置 （如博客或更高版本的文章） 的导航属性，EF Core 使用构造函数。
* 构造函数可以是公共的，也可以是私有的，或者具有任何其他可访问性。 不过，延迟加载代理要求构造函数可从继承代理类访问。 通常，这意味着将其设为公共或受保护。

### <a name="read-only-properties"></a>只读属性

通过构造函数设置属性后，就可以使某些属性成为只读的。 EF Core 支持此功能，但有一些需要注意的事项：

* 不会按约定映射没有 setter 的属性。 （这样做常常会映射不应映射的属性，例如计算属性。）
* 使用自动生成的键值需要键属性，该属性是读写的，因为在插入新实体时密钥生成器需要设置密钥值。

避免这种情况的简单方法是使用专用资源库。 例如：

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

EF Core 看到为读写模式，这意味着，像以前那样映射所有属性并密钥可能仍会由存储生成的具有专用 setter 的属性。

使用专用资源库的一种替代方法是使属性真正为只读，并在 OnModelCreating 中添加更多显式映射。 同样，可以完全删除某些属性并仅替换为字段。 例如，请考虑以下实体类型：

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

OnModelCreating 中的此配置：

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

注意事项：

* "属性" 键现在是字段。 它不是 `readonly` 字段，因此可以使用存储生成的键。
* 其他属性是仅在构造函数中设置的只读属性。
* 如果主键值只是由 EF 设置或从数据库中读取，则无需将其包含在构造函数中。 这会使 "属性" 键作为一个简单字段，并清楚地指出不应在创建新的博客或文章时显式设置。

> [!NOTE]  
> 此代码将导致编译器警告 "169"，指示该字段从未使用过。 由于在现实中 EF Core extralinguistic 的方式使用该字段，这可予以忽视。

## <a name="injecting-services"></a>注入服务

EF Core 还可以将"服务"注入到实体类型的构造函数。 例如，可以注入以下内容：

* `DbContext`-当前上下文实例，也可以类型化为派生的 DbContext 类型
* `ILazyLoader`-延迟加载服务-有关更多详细信息，请参阅[延迟加载文档](../querying/related-data.md)
* `Action<object, string>`-延迟加载委托--有关更多详细信息，请参阅[延迟加载文档](../querying/related-data.md)
* `IEntityType`-与此实体类型关联的 EF Core 元数据

> [!NOTE]  
> 截至 EF Core 2.1，可插入仅通过 EF Core 已知的服务。 将来的版本会考虑对注入应用程序服务的支持。

例如，注入的 DbContext 可用于有选择地访问数据库，以获取相关实体的相关信息，而无需将它们全部加载。 在下面的示例中，这用于在不加载帖子的情况下获取博客中的帖子数：

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

请注意以下几点：

* 构造函数是专用容器，因为它只能由 EF Core，并且没有用于常规用途的另一个公共构造函数。
* 使用注入的服务（即上下文）的代码可防御 `null` 处理 EF Core 不创建实例的情况。
* 因为服务存储在读/写属性中，所以当将实体附加到新的上下文实例时，它将被重置。

> [!WARNING]  
> 因为它将直接与 EF Core 的实体类型，将注入如下 DbContext 通常被视为反模式。 使用此类服务注入之前，请仔细考虑所有选项。
