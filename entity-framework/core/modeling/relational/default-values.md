---
title: 默认值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: 0d3613606f21a78e22cfe0ee752ea982a6a17f93
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196989"
---
# <a name="default-values"></a><span data-ttu-id="48791-102">默认值</span><span class="sxs-lookup"><span data-stu-id="48791-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="48791-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="48791-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="48791-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="48791-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="48791-105">如果插入新行，但没有为该列指定值，则列的默认值为要插入的值。</span><span class="sxs-lookup"><span data-stu-id="48791-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="48791-106">约定</span><span class="sxs-lookup"><span data-stu-id="48791-106">Conventions</span></span>

<span data-ttu-id="48791-107">按照约定，未配置默认值。</span><span class="sxs-lookup"><span data-stu-id="48791-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="48791-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="48791-108">Data Annotations</span></span>

<span data-ttu-id="48791-109">不能使用数据批注设置默认值。</span><span class="sxs-lookup"><span data-stu-id="48791-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="48791-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="48791-110">Fluent API</span></span>

<span data-ttu-id="48791-111">您可以使用 "熟知 API" 指定属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="48791-111">You can use the Fluent API to specify the default value for a property.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultValue.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Rating)
            .HasDefaultValue(3);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public int Rating { get; set; }
}
```

<span data-ttu-id="48791-112">还可以指定用于计算默认值的 SQL 片段。</span><span class="sxs-lookup"><span data-stu-id="48791-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/DefaultValueSql.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Created)
            .HasDefaultValueSql("getdate()");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public DateTime Created { get; set; }
}
```
