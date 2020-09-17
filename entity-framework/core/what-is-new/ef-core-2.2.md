---
title: EF Core 2.2 中的新变化 - EF Core
description: Entity Framework Core 2.2 中的更改和改进
author: divega
ms.date: 11/14/2018
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 68e3cbd5c7345330a47f1457c9b096fee5dd49e9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072324"
---
# <a name="new-features-in-ef-core-22"></a><span data-ttu-id="28351-103">EF Core 2.2 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="28351-103">New features in EF Core 2.2</span></span>

## <a name="spatial-data-support"></a><span data-ttu-id="28351-104">空间数据支持</span><span class="sxs-lookup"><span data-stu-id="28351-104">Spatial data support</span></span>

<span data-ttu-id="28351-105">空间数据可用于表示对象的物理位置和形状。</span><span class="sxs-lookup"><span data-stu-id="28351-105">Spatial data can be used to represent the physical location and shape of objects.</span></span>
<span data-ttu-id="28351-106">许多数据库都可以本机存储、索引和查询空间数据。</span><span class="sxs-lookup"><span data-stu-id="28351-106">Many databases can natively store, index, and query spatial data.</span></span>
<span data-ttu-id="28351-107">常见方案包括，查询给定距离内的对象，以及测试多边形是否包含给定位置。</span><span class="sxs-lookup"><span data-stu-id="28351-107">Common scenarios include querying for objects within a given distance, and testing if a polygon contains a given location.</span></span>
<span data-ttu-id="28351-108">EF Core 2.2 现在支持使用 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) 库中的类型，处理各种数据库中的空间数据。</span><span class="sxs-lookup"><span data-stu-id="28351-108">EF Core 2.2 now supports working with spatial data from various databases using types from the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) library.</span></span>

<span data-ttu-id="28351-109">空间数据支持是作为一系列提供程序专用扩展包进行实现。</span><span class="sxs-lookup"><span data-stu-id="28351-109">Spatial data support is implemented as a series of provider-specific extension packages.</span></span>
<span data-ttu-id="28351-110">每个包都为 NTS 类型和方法以及数据库中相应的空间类型和函数提供映射。</span><span class="sxs-lookup"><span data-stu-id="28351-110">Each of these packages contributes mappings for NTS types and methods, and the corresponding spatial types and functions in the database.</span></span>
<span data-ttu-id="28351-111">此类提供程序扩展现在可用于 [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) 和 [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/)（来自 [Npgsql 项目](https://www.npgsql.org/)）。</span><span class="sxs-lookup"><span data-stu-id="28351-111">Such provider extensions are now available for [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), and [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (from the [Npgsql project](https://www.npgsql.org/)).</span></span>
<span data-ttu-id="28351-112">空间类型可以直接与 [EF Core 内存中提供程序](xref:core/providers/in-memory/index)一起使用，无需使用额外扩展。</span><span class="sxs-lookup"><span data-stu-id="28351-112">Spatial types can be used directly with the [EF Core in-memory provider](xref:core/providers/in-memory/index) without additional extensions.</span></span>

<span data-ttu-id="28351-113">安装提供程序扩展后，便能向实体添加受支持类型的属性。</span><span class="sxs-lookup"><span data-stu-id="28351-113">Once the provider extension is installed, you can add properties of supported types to your entities.</span></span> <span data-ttu-id="28351-114">例如：</span><span class="sxs-lookup"><span data-stu-id="28351-114">For example:</span></span>

``` csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }
  
    [Required]
    public Point Location { get; set; }
  }
}
```

<span data-ttu-id="28351-115">然后，可以暂留包含空间数据的实体：</span><span class="sxs-lookup"><span data-stu-id="28351-115">You can then persist entities with spatial data:</span></span>

``` csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```

<span data-ttu-id="28351-116">还可以根据空间数据和操作执行数据库查询：</span><span class="sxs-lookup"><span data-stu-id="28351-116">And you can execute database queries based on spatial data and operations:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="28351-117">若要详细了解此功能，请参阅[空间类型文档](xref:core/modeling/spatial)。</span><span class="sxs-lookup"><span data-stu-id="28351-117">For more information on this feature, see the [spatial types documentation](xref:core/modeling/spatial).</span></span>

## <a name="collections-of-owned-entities"></a><span data-ttu-id="28351-118">从属实体集合</span><span class="sxs-lookup"><span data-stu-id="28351-118">Collections of owned entities</span></span>

<span data-ttu-id="28351-119">EF Core 2.0 增加了在一对一关联中对所有权进行建模的功能。</span><span class="sxs-lookup"><span data-stu-id="28351-119">EF Core 2.0 added the ability to model ownership in one-to-one associations.</span></span>
<span data-ttu-id="28351-120">EF Core 2.2 将此功能扩展为，将所有权表达为一对多关联。</span><span class="sxs-lookup"><span data-stu-id="28351-120">EF Core 2.2 extends the ability to express ownership to one-to-many associations.</span></span>
<span data-ttu-id="28351-121">所有权有助于约束实体的使用方式。</span><span class="sxs-lookup"><span data-stu-id="28351-121">Ownership helps constrain how entities are used.</span></span>

<span data-ttu-id="28351-122">例如，从属实体：</span><span class="sxs-lookup"><span data-stu-id="28351-122">For example, owned entities:</span></span>

- <span data-ttu-id="28351-123">只能出现在其他实体类型的导航属性中。</span><span class="sxs-lookup"><span data-stu-id="28351-123">Can only ever appear on navigation properties of other entity types.</span></span>
- <span data-ttu-id="28351-124">自动加载，并且只能被 DbContext 和所有者跟踪。</span><span class="sxs-lookup"><span data-stu-id="28351-124">Are automatically loaded, and can only be tracked by a DbContext alongside their owner.</span></span>

<span data-ttu-id="28351-125">在关系数据库中，从属集合映射到所有者的各个表，就像是常规的一对多关联一样。</span><span class="sxs-lookup"><span data-stu-id="28351-125">In relational databases, owned collections are mapped to separate tables from the owner, just like regular one-to-many associations.</span></span>
<span data-ttu-id="28351-126">不过，在面向文档的数据库中，我们计划将（从属集合或引用中的）从属实体与所有者嵌套在同一个文档中。</span><span class="sxs-lookup"><span data-stu-id="28351-126">But in document-oriented databases, we plan to nest owned entities (in owned collections or references) within the same document as the owner.</span></span>

<span data-ttu-id="28351-127">若要使用此功能，可以调用新增的 OwnsMany() API：</span><span class="sxs-lookup"><span data-stu-id="28351-127">You can use the feature by calling the new OwnsMany() API:</span></span>

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

<span data-ttu-id="28351-128">有关详细信息，请参阅[更新后的从属实体文档](xref:core/modeling/owned-entities#collections-of-owned-types)。</span><span class="sxs-lookup"><span data-stu-id="28351-128">For more information, see the [updated owned entities documentation](xref:core/modeling/owned-entities#collections-of-owned-types).</span></span>

## <a name="query-tags"></a><span data-ttu-id="28351-129">查询标记</span><span class="sxs-lookup"><span data-stu-id="28351-129">Query tags</span></span>

<span data-ttu-id="28351-130">此功能简化了将代码中的 LINQ 查询与日志中捕获的已生成 SQL 查询相关联的过程。</span><span class="sxs-lookup"><span data-stu-id="28351-130">This feature simplifies the correlation of LINQ queries in code with generated SQL queries captured in logs.</span></span>

<span data-ttu-id="28351-131">若要利用查询标记，请使用新增的 TagWith() 方法对 LINQ 查询进行批注。</span><span class="sxs-lookup"><span data-stu-id="28351-131">To take advantage of query tags, you annotate a LINQ query using the new TagWith() method.</span></span>
<span data-ttu-id="28351-132">使用上一示例中的空间查询：</span><span class="sxs-lookup"><span data-stu-id="28351-132">Using the spatial query from a previous example:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="28351-133">此 LINQ 查询将生成以下 SQL 输出：</span><span class="sxs-lookup"><span data-stu-id="28351-133">This LINQ query will produce the following SQL output:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="28351-134">有关详细信息，请参阅[查询标记文档](xref:core/querying/tags)。</span><span class="sxs-lookup"><span data-stu-id="28351-134">For more information, see the [query tags documentation](xref:core/querying/tags).</span></span>
