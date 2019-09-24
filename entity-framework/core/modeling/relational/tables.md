---
title: 表映射-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 62dce317b901bc862b3c7d20ed1d176805bb24dd
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196965"
---
# <a name="table-mapping"></a><span data-ttu-id="2f2b2-102">表映射</span><span class="sxs-lookup"><span data-stu-id="2f2b2-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="2f2b2-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="2f2b2-104">安装关系数据库提供程序后，此处显示的扩展方法将变为可用（原因在于 Microsoft.EntityFrameworkCore.Relational 包是共享的）。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="2f2b2-105">表映射标识在数据库中哪张表应该进行内容查询和保存操作。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="2f2b2-106">约定</span><span class="sxs-lookup"><span data-stu-id="2f2b2-106">Conventions</span></span>

<span data-ttu-id="2f2b2-107">按照约定，每个实体将设置为映射到名称与 `DbSet<TEntity>` 属性（公开派生上下文中的实体）相同的表中。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="2f2b2-108">如果给定`DbSet<TEntity>`实体中不包含，则使用类名称。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="2f2b2-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="2f2b2-109">Data Annotations</span></span>

<span data-ttu-id="2f2b2-110">可以使用数据注释来配置类型映射表。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="2f2b2-111">您还可以指定表所属的架构。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="2f2b2-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="2f2b2-112">Fluent API</span></span>

<span data-ttu-id="2f2b2-113">您可以使用熟知的 API 来配置类型映射到的表。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

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

<span data-ttu-id="2f2b2-114">您还可以指定表所属的架构。</span><span class="sxs-lookup"><span data-stu-id="2f2b2-114">You can also specify a schema that the table belongs to.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/TableAndSchema.cs?highlight=2)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .ToTable("blogs", schema: "blogging");
```
