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
# <a name="alternate-keys"></a><span data-ttu-id="aadd5-102">备用键</span><span class="sxs-lookup"><span data-stu-id="aadd5-102">Alternate Keys</span></span>

<span data-ttu-id="aadd5-103">备用键与主键相对，用作每个实体实例的备用唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="aadd5-103">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key.</span></span> <span data-ttu-id="aadd5-104">备用键可用作关系的目标。</span><span class="sxs-lookup"><span data-stu-id="aadd5-104">Alternate keys can be used as the target of a relationship.</span></span> <span data-ttu-id="aadd5-105">使用关系数据库时，这将映射到备用键列上的唯一索引/约束和引用列的一个或多个外键约束的概念。</span><span class="sxs-lookup"><span data-stu-id="aadd5-105">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]  
> <span data-ttu-id="aadd5-106">如果只想强制实施列的唯一性，则需要唯一索引而不是备用键，请参阅[索引](indexes.md)。</span><span class="sxs-lookup"><span data-stu-id="aadd5-106">If you just want to enforce uniqueness of a column then you want a unique index rather than an alternate key, see [Indexes](indexes.md).</span></span> <span data-ttu-id="aadd5-107">在 EF 中，备用键提供可比唯一索引更强大的功能，因为它们可以用作外键的目标。</span><span class="sxs-lookup"><span data-stu-id="aadd5-107">In EF, alternate keys provide greater functionality than unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="aadd5-108">备用键通常引入为你在需要时，不需要手动配置它们。</span><span class="sxs-lookup"><span data-stu-id="aadd5-108">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="aadd5-109">请参阅[约定](#conventions)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="aadd5-109">See [Conventions](#conventions) for more details.</span></span>

## <a name="conventions"></a><span data-ttu-id="aadd5-110">约定</span><span class="sxs-lookup"><span data-stu-id="aadd5-110">Conventions</span></span>

<span data-ttu-id="aadd5-111">按照约定，系统将在识别属性（不是主键）时为你引入备用键，充当关系的目标。</span><span class="sxs-lookup"><span data-stu-id="aadd5-111">By convention, an alternate key is introduced for you when you identify a property, that is not the primary key, as the target of a relationship.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="aadd5-112">数据注释</span><span class="sxs-lookup"><span data-stu-id="aadd5-112">Data Annotations</span></span>

<span data-ttu-id="aadd5-113">不能使用数据注释配置备用键。</span><span class="sxs-lookup"><span data-stu-id="aadd5-113">Alternate keys can not be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="aadd5-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="aadd5-114">Fluent API</span></span>

<span data-ttu-id="aadd5-115">Fluent API 可用于配置要作为备用键的单个属性。</span><span class="sxs-lookup"><span data-stu-id="aadd5-115">You can use the Fluent API to configure a single property to be an alternate key.</span></span>

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

<span data-ttu-id="aadd5-116">Fluent API 还可用于配置要作为备用键 （称为复合的备用键） 的多个属性。</span><span class="sxs-lookup"><span data-stu-id="aadd5-116">You can also use the Fluent API to configure multiple properties to be an alternate key (known as a composite alternate key).</span></span>

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
