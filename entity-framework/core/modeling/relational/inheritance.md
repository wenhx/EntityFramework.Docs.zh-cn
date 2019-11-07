---
title: 继承（关系数据库）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 381d1878007bb78b359eb49649f4356f1e5eb04a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655631"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="2e281-102">继承（关系数据库）</span><span class="sxs-lookup"><span data-stu-id="2e281-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="2e281-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="2e281-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="2e281-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="2e281-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="2e281-105">EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。</span><span class="sxs-lookup"><span data-stu-id="2e281-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="2e281-106">目前，EF Core 中仅实现了每个层次结构一个表（TPH）模式。</span><span class="sxs-lookup"><span data-stu-id="2e281-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="2e281-107">其他常见模式（例如每种类型一个表（TPT））和每个具体的表类型（TPC）尚不可用。</span><span class="sxs-lookup"><span data-stu-id="2e281-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="2e281-108">约定</span><span class="sxs-lookup"><span data-stu-id="2e281-108">Conventions</span></span>

<span data-ttu-id="2e281-109">按照约定，将使用每个层次结构一个表（TPH）模式映射继承。</span><span class="sxs-lookup"><span data-stu-id="2e281-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="2e281-110">TPH 使用单个表来存储层次结构中所有类型的数据。</span><span class="sxs-lookup"><span data-stu-id="2e281-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="2e281-111">鉴别器列用于标识每行所表示的类型。</span><span class="sxs-lookup"><span data-stu-id="2e281-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="2e281-112">如果模型中显式包括两个或更多个继承类型，EF Core 将仅设置继承（有关更多详细信息，请参阅[继承](../inheritance.md)）。</span><span class="sxs-lookup"><span data-stu-id="2e281-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="2e281-113">下面的示例演示了一个简单的继承方案，以及使用 TPH 模式存储在关系数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="2e281-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="2e281-114">*鉴别*器列标识每个行中存储哪种类型的*博客*。</span><span class="sxs-lookup"><span data-stu-id="2e281-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![图像](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="2e281-116">使用 TPH 映射时，数据库列会根据需要自动进行为 null。</span><span class="sxs-lookup"><span data-stu-id="2e281-116">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="2e281-117">数据注释</span><span class="sxs-lookup"><span data-stu-id="2e281-117">Data Annotations</span></span>

<span data-ttu-id="2e281-118">不能使用数据批注来配置继承。</span><span class="sxs-lookup"><span data-stu-id="2e281-118">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="2e281-119">Fluent API</span><span class="sxs-lookup"><span data-stu-id="2e281-119">Fluent API</span></span>

<span data-ttu-id="2e281-120">您可以使用熟知的 API 来配置鉴别器列的名称和类型，以及用于标识层次结构中的每个类型的值。</span><span class="sxs-lookup"><span data-stu-id="2e281-120">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="2e281-121">配置鉴别器属性</span><span class="sxs-lookup"><span data-stu-id="2e281-121">Configuring the discriminator property</span></span>

<span data-ttu-id="2e281-122">在上面的示例中，将在层次结构的基实体上将鉴别器创建为[影子属性](xref:core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="2e281-122">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="2e281-123">由于它是模型中的属性，因此可以像配置其他属性一样对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e281-123">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="2e281-124">例如，若要设置默认的、按约定的鉴别器正在使用的最大长度，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2e281-124">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="2e281-125">鉴别器还可以映射到实体中的实际 CLR 属性。</span><span class="sxs-lookup"><span data-stu-id="2e281-125">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="2e281-126">例如:</span><span class="sxs-lookup"><span data-stu-id="2e281-126">For example:</span></span>

```C#
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("BlogType");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public string BlogType { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

<span data-ttu-id="2e281-127">将这两个内容组合在一起可以将鉴别器映射到实际属性并对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="2e281-127">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>

```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
