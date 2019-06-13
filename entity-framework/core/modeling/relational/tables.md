---
title: 表映射的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 32c5e3cc0e498005ce8e6be1f1ee7e8ddf9b510d
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994132"
---
# <a name="table-mapping"></a><span data-ttu-id="aa375-102">表映射</span><span class="sxs-lookup"><span data-stu-id="aa375-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="aa375-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="aa375-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="aa375-104">安装关系数据库提供程序后，此处显示的扩展方法将变为可用（原因在于 Microsoft.EntityFrameworkCore.Relational 包是共享的  ）。</span><span class="sxs-lookup"><span data-stu-id="aa375-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="aa375-105">表映射标识在数据库中哪张表应该进行内容查询和保存操作。</span><span class="sxs-lookup"><span data-stu-id="aa375-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="aa375-106">约定</span><span class="sxs-lookup"><span data-stu-id="aa375-106">Conventions</span></span>

<span data-ttu-id="aa375-107">按照约定，每个实体将会设置将映射到具有相同的名称的表`DbSet<TEntity>`公开所派生上下文的实体的属性。</span><span class="sxs-lookup"><span data-stu-id="aa375-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="aa375-108">如果没有`DbSet<TEntity>`包含对于给定的实体中，使用此类名称。</span><span class="sxs-lookup"><span data-stu-id="aa375-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="aa375-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="aa375-109">Data Annotations</span></span>

<span data-ttu-id="aa375-110">可以使用数据注释来配置类型映射表。</span><span class="sxs-lookup"><span data-stu-id="aa375-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

``` csharp
using System.ComponentModel.DataAnnotations.Schema;
```
``` csharp
[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="aa375-111">此外可以指定此表所属的架构。</span><span class="sxs-lookup"><span data-stu-id="aa375-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="aa375-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="aa375-112">Fluent API</span></span>

<span data-ttu-id="aa375-113">可以使用 Fluent API 配置类型映射到表。</span><span class="sxs-lookup"><span data-stu-id="aa375-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
```
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .ToTable("blogs");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="aa375-114">此外可以指定此表所属的架构。</span><span class="sxs-lookup"><span data-stu-id="aa375-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
