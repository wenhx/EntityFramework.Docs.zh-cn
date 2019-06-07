---
title: 卷影属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 4029539f3642f539a427f5901577d4df96c00f30
ms.sourcegitcommit: 119058fefd7f35952048f783ada68be9aa612256
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66749701"
---
# <a name="shadow-properties"></a><span data-ttu-id="a0f24-102">隐藏属性</span><span class="sxs-lookup"><span data-stu-id="a0f24-102">Shadow Properties</span></span>

<span data-ttu-id="a0f24-103">卷影属性是指你.NET 实体类中未定义但 EF Core模型中该实体类型定义。</span><span class="sxs-lookup"><span data-stu-id="a0f24-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="a0f24-104">完全在更改跟踪器中维护的值和这些属性的状态。</span><span class="sxs-lookup"><span data-stu-id="a0f24-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="a0f24-105">不应在映射的实体类型公开数据库中没有数据时，卷影属性非常有用。</span><span class="sxs-lookup"><span data-stu-id="a0f24-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="a0f24-106">它们通常用于外键属性，其中两个实体之间的关系表示通过在数据库中的外键值，但此关系管理使用实体类型之间的导航属性的实体类型上。</span><span class="sxs-lookup"><span data-stu-id="a0f24-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="a0f24-107">阴影属性值可以获取并通过更改`ChangeTracker`API。</span><span class="sxs-lookup"><span data-stu-id="a0f24-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="a0f24-108">可以通过 LINQ 查询中引用隐藏属性`EF.Property`静态方法。</span><span class="sxs-lookup"><span data-stu-id="a0f24-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="a0f24-109">约定</span><span class="sxs-lookup"><span data-stu-id="a0f24-109">Conventions</span></span>

<span data-ttu-id="a0f24-110">当发现一种关系，但依赖实体类中找到任何外键属性时，可以通过约定创建隐藏属性。</span><span class="sxs-lookup"><span data-stu-id="a0f24-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="a0f24-111">在这种情况下，将引入了卷影的外键属性。</span><span class="sxs-lookup"><span data-stu-id="a0f24-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="a0f24-112">卷影外键属性将被命名为`<navigation property name><principal key property name>`（指向主体实体，依赖实体的导航用于命名）。</span><span class="sxs-lookup"><span data-stu-id="a0f24-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="a0f24-113">如果主体键属性名称包含导航属性的名称，则名称只是将`<principal key property name>`。</span><span class="sxs-lookup"><span data-stu-id="a0f24-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="a0f24-114">如果依赖实体上没有任何导航属性，则主体类型名称使用在其原位置。</span><span class="sxs-lookup"><span data-stu-id="a0f24-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="a0f24-115">例如，以下代码列表将导致`BlogId`影子属性引入到`Post`实体。</span><span class="sxs-lookup"><span data-stu-id="a0f24-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/ShadowForeignKey.cs)] -->
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

## <a name="data-annotations"></a><span data-ttu-id="a0f24-116">数据注释</span><span class="sxs-lookup"><span data-stu-id="a0f24-116">Data Annotations</span></span>

<span data-ttu-id="a0f24-117">使用数据注释，可以创建隐藏属性。</span><span class="sxs-lookup"><span data-stu-id="a0f24-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a0f24-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a0f24-118">Fluent API</span></span>

<span data-ttu-id="a0f24-119">可以使用 Fluent API 配置卷影属性。</span><span class="sxs-lookup"><span data-stu-id="a0f24-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="a0f24-120">一旦调用的字符串重载`Property`可以链接的任何其他属性会配置调用。</span><span class="sxs-lookup"><span data-stu-id="a0f24-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="a0f24-121">如果名称提供给`Property`方法与现有属性 （的影子属性或一个实体类中定义） 的名称相匹配，则代码将配置该现有属性，而不是引入的新的影子属性。</span><span class="sxs-lookup"><span data-stu-id="a0f24-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/ShadowProperty.cs?highlight=7,8)] -->
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
