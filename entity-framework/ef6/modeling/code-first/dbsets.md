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
# <a name="defining-dbsets"></a><span data-ttu-id="613c8-102">定义 Dbset</span><span class="sxs-lookup"><span data-stu-id="613c8-102">Defining DbSets</span></span>
<span data-ttu-id="613c8-103">使用 Code First 工作流进行开发时，可定义一个派生 DbContext，用于表示与数据库的会话，并为模型中的每个类型公开一个 DbSet。</span><span class="sxs-lookup"><span data-stu-id="613c8-103">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="613c8-104">本主题介绍可用于定义 DbSet 属性的各种方式。</span><span class="sxs-lookup"><span data-stu-id="613c8-104">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="613c8-105">具有 DbSet 属性的 DbContext</span><span class="sxs-lookup"><span data-stu-id="613c8-105">DbContext with DbSet properties</span></span>  

<span data-ttu-id="613c8-106">Code First 示例中所示的常见情况是，对于模型的实体类型，DbContext 具有公共自动 DbSet 属性。</span><span class="sxs-lookup"><span data-stu-id="613c8-106">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="613c8-107">例如：</span><span class="sxs-lookup"><span data-stu-id="613c8-107">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="613c8-108">当在 Code First 模式下使用时，这会将博客和公告配置为实体类型，并配置可从此访问的其他类型。</span><span class="sxs-lookup"><span data-stu-id="613c8-108">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="613c8-109">此外，DbContext 会自动为每个属性调用 setter，以设置相应 DbSet 的实例。</span><span class="sxs-lookup"><span data-stu-id="613c8-109">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="613c8-110">具有 IDbSet 属性的 DbContext</span><span class="sxs-lookup"><span data-stu-id="613c8-110">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="613c8-111">在某些情况下，例如创建模拟或 fakes 时，使用接口来声明集属性更有用。</span><span class="sxs-lookup"><span data-stu-id="613c8-111">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="613c8-112">在这种情况下，可以使用 IDbSet 接口代替 DbSet。</span><span class="sxs-lookup"><span data-stu-id="613c8-112">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="613c8-113">例如：</span><span class="sxs-lookup"><span data-stu-id="613c8-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="613c8-114">此上下文的工作方式与使用 DbSet 类作为其设置属性的上下文完全相同。</span><span class="sxs-lookup"><span data-stu-id="613c8-114">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="613c8-115">具有只读 set 属性的 DbContext</span><span class="sxs-lookup"><span data-stu-id="613c8-115">DbContext with read-only set properties</span></span>  

<span data-ttu-id="613c8-116">如果你不希望为 DbSet 或 IDbSet 属性公开公共资源库，则可以改为创建只读属性并自行创建集实例。</span><span class="sxs-lookup"><span data-stu-id="613c8-116">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="613c8-117">例如：</span><span class="sxs-lookup"><span data-stu-id="613c8-117">For example:</span></span>  

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

<span data-ttu-id="613c8-118">请注意，DbContext 将缓存从 Set 方法返回的 DbSet 的实例，使每个属性在每次调用时都返回相同的实例。</span><span class="sxs-lookup"><span data-stu-id="613c8-118">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="613c8-119">Code First 的实体类型的发现的工作方式与使用公共 getter 和 setter 的属性相同。</span><span class="sxs-lookup"><span data-stu-id="613c8-119">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
