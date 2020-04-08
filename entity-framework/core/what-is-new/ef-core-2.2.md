---
title: EF Core 2.2 中的新变化 - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 998C04F3-676A-4FCF-8450-CFB0457B4198
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: fb9de799753bebd7b4092cd8f4af74703dee3e45
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413551"
---
# <a name="new-features-in-ef-core-22"></a><span data-ttu-id="32779-102">EF Core 2.2 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="32779-102">New features in EF Core 2.2</span></span>

## <a name="spatial-data-support"></a><span data-ttu-id="32779-103">空间数据支持</span><span class="sxs-lookup"><span data-stu-id="32779-103">Spatial data support</span></span>

<span data-ttu-id="32779-104">空间数据可用于表示对象的物理位置和形状。</span><span class="sxs-lookup"><span data-stu-id="32779-104">Spatial data can be used to represent the physical location and shape of objects.</span></span>
<span data-ttu-id="32779-105">许多数据库都可以本机存储、索引和查询空间数据。</span><span class="sxs-lookup"><span data-stu-id="32779-105">Many databases can natively store, index, and query spatial data.</span></span>
<span data-ttu-id="32779-106">常见方案包括，查询给定距离内的对象，以及测试多边形是否包含给定位置。</span><span class="sxs-lookup"><span data-stu-id="32779-106">Common scenarios include querying for objects within a given distance, and testing if a polygon contains a given location.</span></span>
<span data-ttu-id="32779-107">EF Core 2.2 现在支持使用 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) 库中的类型，处理各种数据库中的空间数据。</span><span class="sxs-lookup"><span data-stu-id="32779-107">EF Core 2.2 now supports working with spatial data from various databases using types from the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) library.</span></span>

<span data-ttu-id="32779-108">空间数据支持是作为一系列提供程序专用扩展包进行实现。</span><span class="sxs-lookup"><span data-stu-id="32779-108">Spatial data support is implemented as a series of provider-specific extension packages.</span></span>
<span data-ttu-id="32779-109">每个包都为 NTS 类型和方法以及数据库中相应的空间类型和函数提供映射。</span><span class="sxs-lookup"><span data-stu-id="32779-109">Each of these packages contributes mappings for NTS types and methods, and the corresponding spatial types and functions in the database.</span></span>
<span data-ttu-id="32779-110">此类提供程序扩展现在可用于 [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/)、[SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) 和 [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/)（来自 [Npgsql 项目](https://www.npgsql.org/)）。</span><span class="sxs-lookup"><span data-stu-id="32779-110">Such provider extensions are now available for [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), and [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (from the [Npgsql project](https://www.npgsql.org/)).</span></span>
<span data-ttu-id="32779-111">空间类型可以直接与 [EF Core 内存中提供程序](xref:core/providers/in-memory/index)一起使用，无需使用额外扩展。</span><span class="sxs-lookup"><span data-stu-id="32779-111">Spatial types can be used directly with the [EF Core in-memory provider](xref:core/providers/in-memory/index) without additional extensions.</span></span>

<span data-ttu-id="32779-112">安装提供程序扩展后，便能向实体添加受支持类型的属性。</span><span class="sxs-lookup"><span data-stu-id="32779-112">Once the provider extension is installed, you can add properties of supported types to your entities.</span></span> <span data-ttu-id="32779-113">例如：</span><span class="sxs-lookup"><span data-stu-id="32779-113">For example:</span></span>

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

<span data-ttu-id="32779-114">然后，可以暂留包含空间数据的实体：</span><span class="sxs-lookup"><span data-stu-id="32779-114">You can then persist entities with spatial data:</span></span>

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

<span data-ttu-id="32779-115">还可以根据空间数据和操作执行数据库查询：</span><span class="sxs-lookup"><span data-stu-id="32779-115">And you can execute database queries based on spatial data and operations:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="32779-116">若要详细了解此功能，请参阅[空间类型文档](xref:core/modeling/spatial)。</span><span class="sxs-lookup"><span data-stu-id="32779-116">For more information on this feature, see the [spatial types documentation](xref:core/modeling/spatial).</span></span>

## <a name="collections-of-owned-entities"></a><span data-ttu-id="32779-117">从属实体集合</span><span class="sxs-lookup"><span data-stu-id="32779-117">Collections of owned entities</span></span>

<span data-ttu-id="32779-118">EF Core 2.0 增加了在一对一关联中对所有权进行建模的功能。</span><span class="sxs-lookup"><span data-stu-id="32779-118">EF Core 2.0 added the ability to model ownership in one-to-one associations.</span></span>
<span data-ttu-id="32779-119">EF Core 2.2 将此功能扩展为，将所有权表达为一对多关联。</span><span class="sxs-lookup"><span data-stu-id="32779-119">EF Core 2.2 extends the ability to express ownership to one-to-many associations.</span></span>
<span data-ttu-id="32779-120">所有权有助于约束实体的使用方式。</span><span class="sxs-lookup"><span data-stu-id="32779-120">Ownership helps constrain how entities are used.</span></span>

<span data-ttu-id="32779-121">例如，从属实体：</span><span class="sxs-lookup"><span data-stu-id="32779-121">For example, owned entities:</span></span>

- <span data-ttu-id="32779-122">只能出现在其他实体类型的导航属性中。</span><span class="sxs-lookup"><span data-stu-id="32779-122">Can only ever appear on navigation properties of other entity types.</span></span>
- <span data-ttu-id="32779-123">自动加载，并且只能被 DbContext 和所有者跟踪。</span><span class="sxs-lookup"><span data-stu-id="32779-123">Are automatically loaded, and can only be tracked by a DbContext alongside their owner.</span></span>

<span data-ttu-id="32779-124">在关系数据库中，从属集合映射到所有者的各个表，就像是常规的一对多关联一样。</span><span class="sxs-lookup"><span data-stu-id="32779-124">In relational databases, owned collections are mapped to separate tables from the owner, just like regular one-to-many associations.</span></span>
<span data-ttu-id="32779-125">不过，在面向文档的数据库中，我们计划将（从属集合或引用中的）从属实体与所有者嵌套在同一个文档中。</span><span class="sxs-lookup"><span data-stu-id="32779-125">But in document-oriented databases, we plan to nest owned entities (in owned collections or references) within the same document as the owner.</span></span>

<span data-ttu-id="32779-126">若要使用此功能，可以调用新增的 OwnsMany() API：</span><span class="sxs-lookup"><span data-stu-id="32779-126">You can use the feature by calling the new OwnsMany() API:</span></span>

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

<span data-ttu-id="32779-127">有关详细信息，请参阅[更新后的从属实体文档](xref:core/modeling/owned-entities#collections-of-owned-types)。</span><span class="sxs-lookup"><span data-stu-id="32779-127">For more information, see the [updated owned entities documentation](xref:core/modeling/owned-entities#collections-of-owned-types).</span></span>

## <a name="query-tags"></a><span data-ttu-id="32779-128">查询标记</span><span class="sxs-lookup"><span data-stu-id="32779-128">Query tags</span></span>

<span data-ttu-id="32779-129">此功能简化了将代码中的 LINQ 查询与日志中捕获的已生成 SQL 查询相关联的过程。</span><span class="sxs-lookup"><span data-stu-id="32779-129">This feature simplifies the correlation of LINQ queries in code with generated SQL queries captured in logs.</span></span>

<span data-ttu-id="32779-130">若要利用查询标记，请使用新增的 TagWith() 方法对 LINQ 查询进行批注。</span><span class="sxs-lookup"><span data-stu-id="32779-130">To take advantage of query tags, you annotate a LINQ query using the new TagWith() method.</span></span>
<span data-ttu-id="32779-131">使用上一示例中的空间查询：</span><span class="sxs-lookup"><span data-stu-id="32779-131">Using the spatial query from a previous example:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="32779-132">此 LINQ 查询将生成以下 SQL 输出：</span><span class="sxs-lookup"><span data-stu-id="32779-132">This LINQ query will produce the following SQL output:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="32779-133">有关详细信息，请参阅[查询标记文档](xref:core/querying/tags)。</span><span class="sxs-lookup"><span data-stu-id="32779-133">For more information, see the [query tags documentation](xref:core/querying/tags).</span></span>
