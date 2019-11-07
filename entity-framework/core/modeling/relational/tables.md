---
title: 表映射-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c807aa4c-7845-443d-b8d0-bfc9b42691a3
uid: core/modeling/relational/tables
ms.openlocfilehash: 474c49aca4c65cd5d58b184b1f3c2d30e7abff84
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656105"
---
# <a name="table-mapping"></a><span data-ttu-id="5c29c-102">表映射</span><span class="sxs-lookup"><span data-stu-id="5c29c-102">Table Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="5c29c-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="5c29c-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="5c29c-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="5c29c-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="5c29c-105">表映射标识应在数据库中查询并保存到的表数据。</span><span class="sxs-lookup"><span data-stu-id="5c29c-105">Table mapping identifies which table data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="5c29c-106">约定</span><span class="sxs-lookup"><span data-stu-id="5c29c-106">Conventions</span></span>

<span data-ttu-id="5c29c-107">按照约定，每个实体将设置为映射到名称与 `DbSet<TEntity>` 属性（公开派生上下文中的实体）相同的表中。</span><span class="sxs-lookup"><span data-stu-id="5c29c-107">By convention, each entity will be set up to map to a table with the same name as the `DbSet<TEntity>` property that exposes the entity on the derived context.</span></span> <span data-ttu-id="5c29c-108">如果给定实体不包含 `DbSet<TEntity>`，则使用类名称。</span><span class="sxs-lookup"><span data-stu-id="5c29c-108">If no `DbSet<TEntity>` is included for the given entity, the class name is used.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="5c29c-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="5c29c-109">Data Annotations</span></span>

<span data-ttu-id="5c29c-110">您可以使用数据批注来配置类型映射到的表。</span><span class="sxs-lookup"><span data-stu-id="5c29c-110">You can use Data Annotations to configure the table that a type maps to.</span></span>

``` csharp
using System.ComponentModel.DataAnnotations.Schema;

[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="5c29c-111">您还可以指定表所属的架构。</span><span class="sxs-lookup"><span data-stu-id="5c29c-111">You can also specify a schema that the table belongs to.</span></span>

``` csharp
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="5c29c-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="5c29c-112">Fluent API</span></span>

<span data-ttu-id="5c29c-113">您可以使用熟知的 API 来配置类型映射到的表。</span><span class="sxs-lookup"><span data-stu-id="5c29c-113">You can use the Fluent API to configure the table that a type maps to.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;

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

<span data-ttu-id="5c29c-114">您还可以指定表所属的架构。</span><span class="sxs-lookup"><span data-stu-id="5c29c-114">You can also specify a schema that the table belongs to.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/TableAndSchema.cs?name=Table&highlight=2)]
