---
title: 备用密钥-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: b26d8bc1630af9e811d9c4e7da850a618bc8042e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996966"
---
# <a name="alternate-keys"></a>备用键

除主键之外，备用键用作每个实体实例的备用唯一标识符。 备用键可以用作关联关系的选项。 当使用关系数据库时，这映射到备用键列上的唯一索引/约束的概念以及引用列的一个或多个外键约束。

> [!TIP]  
> 如果只是想要强制实施列的唯一性，则你希望唯一索引而不是备用键，请参阅[索引](indexes.md)。 在 EF 中，备用键提供比唯一索引更强大的功能，因为它们可以用作外键的目标。

备用密钥通常在需要时为您引入，您无需手动配置它们。有关详细信息，请参阅约定](#conventions)。

## <a name="conventions"></a>约定

按照惯例，当您将属性（不是主键）标识为关系的目标时，会为您引入备用键。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/AlternateKey.cs?highlight=12)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .HasForeignKey(p => p.BlogUrl)
            .HasPrincipalKey(b => b.Url);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public string BlogUrl { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a>数据注释

不能使用数据注释配置备用键。

## <a name="fluent-api"></a>Fluent API

Fluent API 可用于配置要作为备用键的单个属性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/AlternateKeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => c.LicensePlate);
    }
}

class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
```

Fluent API 还可用于配置要作为备用键 （称为复合的备用键） 的多个属性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/AlternateKeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
```
