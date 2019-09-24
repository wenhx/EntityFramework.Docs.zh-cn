---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: b6f11401b69bd8e8795f6b22e5392ba16fc9ba2e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197248"
---
# <a name="indexes"></a><span data-ttu-id="a7dcb-102">索引</span><span class="sxs-lookup"><span data-stu-id="a7dcb-102">Indexes</span></span>

<span data-ttu-id="a7dcb-103">索引是跨多个数据存储区的常见概念。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="a7dcb-104">尽管它们在数据存储中的实现可能会有所不同，但也可用于基于列（或一组列）更高效地进行查找。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="a7dcb-105">约定</span><span class="sxs-lookup"><span data-stu-id="a7dcb-105">Conventions</span></span>

<span data-ttu-id="a7dcb-106">按照约定，将在用作外键的每个属性（或一组属性）中创建索引。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a7dcb-107">数据注释</span><span class="sxs-lookup"><span data-stu-id="a7dcb-107">Data Annotations</span></span>

<span data-ttu-id="a7dcb-108">不能使用数据批注创建索引。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a7dcb-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a7dcb-109">Fluent API</span></span>

<span data-ttu-id="a7dcb-110">您可以使用熟知的 API 来指定单个属性的索引。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="a7dcb-111">默认情况下，索引不是唯一的。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-111">By default, indexes are non-unique.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Index.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="a7dcb-112">您还可以指定索引应是唯一的，这意味着对于给定的属性，不能有两个实体具有相同的值。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="a7dcb-113">您还可以为多个列指定索引。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-113">You can also specify an index over more than one column.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/IndexComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .HasIndex(p => new { p.FirstName, p.LastName });
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="a7dcb-114">每个不同的属性集只有一个索引。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="a7dcb-115">如果使用 "熟知 API" 来配置已定义索引的属性集的索引（按照约定或以前的配置），则会更改该索引的定义。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="a7dcb-116">如果要进一步配置由约定创建的索引，则此操作非常有用。</span><span class="sxs-lookup"><span data-stu-id="a7dcb-116">This is useful if you want to further configure an index that was created by convention.</span></span>
