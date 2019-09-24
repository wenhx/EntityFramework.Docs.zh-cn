---
title: 阴影属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 5fdc4c50c295f73d0fa5eef3518adf4d3eb95599
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197698"
---
# <a name="shadow-properties"></a><span data-ttu-id="fe78f-102">阴影属性</span><span class="sxs-lookup"><span data-stu-id="fe78f-102">Shadow Properties</span></span>

<span data-ttu-id="fe78f-103">影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="fe78f-104">这些属性的值和状态纯粹在更改跟踪器中进行维护。</span><span class="sxs-lookup"><span data-stu-id="fe78f-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="fe78f-105">当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。</span><span class="sxs-lookup"><span data-stu-id="fe78f-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="fe78f-106">它们最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体类型之间的导航属性在实体类型上管理关系。</span><span class="sxs-lookup"><span data-stu-id="fe78f-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="fe78f-107">可以通过`ChangeTracker` API 获取和更改影子属性值。</span><span class="sxs-lookup"><span data-stu-id="fe78f-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="fe78f-108">可以通过`EF.Property`静态方法在 LINQ 查询中引用影子属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="fe78f-109">约定</span><span class="sxs-lookup"><span data-stu-id="fe78f-109">Conventions</span></span>

<span data-ttu-id="fe78f-110">如果发现了关系，但在依赖实体类中找不到外键属性，则可以按约定创建阴影属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="fe78f-111">在这种情况下，将引入阴影外键属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="fe78f-112">影子外键属性将命名`<navigation property name><principal key property name>`为（指向主体实体的依赖实体上的导航用于命名）。</span><span class="sxs-lookup"><span data-stu-id="fe78f-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="fe78f-113">如果主体键属性名称包含导航属性的名称，则该名称将只是`<principal key property name>`。</span><span class="sxs-lookup"><span data-stu-id="fe78f-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="fe78f-114">如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。</span><span class="sxs-lookup"><span data-stu-id="fe78f-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="fe78f-115">例如，下面的代码列表将导致`BlogId` `Post`向实体引入阴影属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/ShadowForeignKey.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="fe78f-116">数据注释</span><span class="sxs-lookup"><span data-stu-id="fe78f-116">Data Annotations</span></span>

<span data-ttu-id="fe78f-117">不能通过数据批注创建阴影属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="fe78f-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="fe78f-118">Fluent API</span></span>

<span data-ttu-id="fe78f-119">你可以使用 "熟知 API" 配置阴影属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="fe78f-120">一旦您调用了的字符串重载`Property` ，就可以链接到其他属性的任何配置调用。</span><span class="sxs-lookup"><span data-stu-id="fe78f-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="fe78f-121">如果提供给`Property`方法的名称与现有属性的名称相匹配（一个阴影属性或在实体类中定义的属性），则代码将配置该现有属性，而不是引入新的阴影属性。</span><span class="sxs-lookup"><span data-stu-id="fe78f-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/ShadowProperty.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```
