---
title: 概述 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 0e35a2b3f89c92b717b8e05c8fa3ae5af5ce8fd3
ms.sourcegitcommit: 06073f8efde97dd5f540dbfb69f574d8380566fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67333776"
---
# <a name="entity-framework-core"></a><span data-ttu-id="30dda-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="30dda-102">Entity Framework Core</span></span>

<span data-ttu-id="30dda-103">Entity Framework (EF) Core 是轻量化、可扩展、[开源](https://github.com/aspnet/EntityFrameworkCore)和跨平台版的常用 Entity Framework 数据访问技术。</span><span class="sxs-lookup"><span data-stu-id="30dda-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="30dda-104">EF Core 可用作对象关系映射程序 (O/RM)，以便于 .NET 开发人员能够使用 .NET 对象来处理数据库，这样就不必经常编写大部分数据访问代码了。</span><span class="sxs-lookup"><span data-stu-id="30dda-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="30dda-105">EF Core 支持多个数据库引擎，请参阅[数据库提供程序](providers/index.md)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="30dda-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="30dda-106">模型</span><span class="sxs-lookup"><span data-stu-id="30dda-106">The Model</span></span>

<span data-ttu-id="30dda-107">对于 EF Core，使用模型执行数据访问。</span><span class="sxs-lookup"><span data-stu-id="30dda-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="30dda-108">模型由实体类和表示数据库会话的上下文对象构成，可便于用户查询和保存数据。</span><span class="sxs-lookup"><span data-stu-id="30dda-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="30dda-109">有关详细信息，请参阅[创建模型](modeling/index.md)。</span><span class="sxs-lookup"><span data-stu-id="30dda-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="30dda-110">可以根据现有数据库生成模型，手动将模型编码为与数据库匹配，也可以使用 [EF 迁移](managing-schemas/migrations/index.md)根据模型创建数据库，然后在模型随时间推移发生更改时改进它。</span><span class="sxs-lookup"><span data-stu-id="30dda-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace Intro
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(
                @"Server=(localdb)\mssqllocaldb;Database=Blogging;Integrated Security=True");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }
        public int Rating { get; set; }
        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

## <a name="querying"></a><span data-ttu-id="30dda-111">查询</span><span class="sxs-lookup"><span data-stu-id="30dda-111">Querying</span></span>

<span data-ttu-id="30dda-112">使用语言集成查询 (LINQ) 从数据库检索实体类的实例。</span><span class="sxs-lookup"><span data-stu-id="30dda-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="30dda-113">有关详细信息，请参阅[查询数据](querying/index.md)。</span><span class="sxs-lookup"><span data-stu-id="30dda-113">See [Querying Data](querying/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a><span data-ttu-id="30dda-114">保存数据</span><span class="sxs-lookup"><span data-stu-id="30dda-114">Saving Data</span></span>

<span data-ttu-id="30dda-115">使用实体类的实例在数据库中创建、删除和修改数据。</span><span class="sxs-lookup"><span data-stu-id="30dda-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="30dda-116">有关详细信息，请参阅[保存数据](saving/index.md)。</span><span class="sxs-lookup"><span data-stu-id="30dda-116">See [Saving Data](saving/index.md) to learn more.</span></span>

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

## <a name="next-steps"></a><span data-ttu-id="30dda-117">后续步骤</span><span class="sxs-lookup"><span data-stu-id="30dda-117">Next steps</span></span>

<span data-ttu-id="30dda-118">有关介绍性教程，请参阅 [Entity Framework Core 入门](get-started/index.md)。</span><span class="sxs-lookup"><span data-stu-id="30dda-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>

