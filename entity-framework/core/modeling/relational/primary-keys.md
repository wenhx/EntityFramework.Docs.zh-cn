---
title: 主键-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: bdb31964d717c64bddc28e7f1ce55b261e285a9b
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196950"
---
# <a name="primary-keys"></a><span data-ttu-id="c7274-102">主键</span><span class="sxs-lookup"><span data-stu-id="c7274-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="c7274-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="c7274-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="c7274-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="c7274-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="c7274-105">为每个实体类型的键引入 primary key 约束。</span><span class="sxs-lookup"><span data-stu-id="c7274-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="c7274-106">约定</span><span class="sxs-lookup"><span data-stu-id="c7274-106">Conventions</span></span>

<span data-ttu-id="c7274-107">按照约定，会将数据库中的主键命名为`PK_<type name>`。</span><span class="sxs-lookup"><span data-stu-id="c7274-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="c7274-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="c7274-108">Data Annotations</span></span>

<span data-ttu-id="c7274-109">不能使用数据批注配置主键的关系数据库的特定方面。</span><span class="sxs-lookup"><span data-stu-id="c7274-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="c7274-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="c7274-110">Fluent API</span></span>

<span data-ttu-id="c7274-111">您可以使用 "熟知 API" 在数据库中配置 primary key 约束的名称。</span><span class="sxs-lookup"><span data-stu-id="c7274-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/KeyName.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasKey(b => b.BlogId)
            .HasName("PrimaryKey_BlogId");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
