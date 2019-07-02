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
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core 是轻量化、可扩展、[开源](https://github.com/aspnet/EntityFrameworkCore)和跨平台版的常用 Entity Framework 数据访问技术。

EF Core 可用作对象关系映射程序 (O/RM)，以便于 .NET 开发人员能够使用 .NET 对象来处理数据库，这样就不必经常编写大部分数据访问代码了。

EF Core 支持多个数据库引擎，请参阅[数据库提供程序](providers/index.md)了解详细信息。

## <a name="the-model"></a>模型

对于 EF Core，使用模型执行数据访问。 模型由实体类和表示数据库会话的上下文对象构成，可便于用户查询和保存数据。 有关详细信息，请参阅[创建模型](modeling/index.md)。

可以根据现有数据库生成模型，手动将模型编码为与数据库匹配，也可以使用 [EF 迁移](managing-schemas/migrations/index.md)根据模型创建数据库，然后在模型随时间推移发生更改时改进它。

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

## <a name="querying"></a>查询

使用语言集成查询 (LINQ) 从数据库检索实体类的实例。 有关详细信息，请参阅[查询数据](querying/index.md)。

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a>保存数据

使用实体类的实例在数据库中创建、删除和修改数据。 有关详细信息，请参阅[保存数据](saving/index.md)。

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

## <a name="next-steps"></a>后续步骤

有关介绍性教程，请参阅 [Entity Framework Core 入门](get-started/index.md)。

