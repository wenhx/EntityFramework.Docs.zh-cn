---
title: 定义 Dbset-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
ms.openlocfilehash: 045b22d2b9d26804948689dd7c9dd694baadda7e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415777"
---
# <a name="defining-dbsets"></a>定义 Dbset
使用 Code First 工作流进行开发时，可定义一个派生 DbContext，用于表示与数据库的会话，并为模型中的每个类型公开一个 DbSet。 本主题介绍可用于定义 DbSet 属性的各种方式。  

## <a name="dbcontext-with-dbset-properties"></a>具有 DbSet 属性的 DbContext  

Code First 示例中所示的常见情况是，对于模型的实体类型，DbContext 具有公共自动 DbSet 属性。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

当在 Code First 模式下使用时，这会将博客和公告配置为实体类型，并配置可从此访问的其他类型。 此外，DbContext 会自动为每个属性调用 setter，以设置相应 DbSet 的实例。  

## <a name="dbcontext-with-idbset-properties"></a>具有 IDbSet 属性的 DbContext  

在某些情况下，例如创建模拟或 fakes 时，使用接口来声明集属性更有用。 在这种情况下，可以使用 IDbSet 接口代替 DbSet。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

此上下文的工作方式与使用 DbSet 类作为其设置属性的上下文完全相同。  

## <a name="dbcontext-with-read-only-set-properties"></a>具有只读 set 属性的 DbContext  

如果你不希望为 DbSet 或 IDbSet 属性公开公共资源库，则可以改为创建只读属性并自行创建集实例。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

请注意，DbContext 将缓存从 Set 方法返回的 DbSet 的实例，使每个属性在每次调用时都返回相同的实例。  

Code First 的实体类型的发现的工作方式与使用公共 getter 和 setter 的属性相同。  
