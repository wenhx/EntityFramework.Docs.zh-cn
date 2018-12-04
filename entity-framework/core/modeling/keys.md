---
title: EF Core 中的主键
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
uid: core/modeling/keys
ms.openlocfilehash: 9e6946100ebabc6ba57cb792b3672219098b1e21
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994016"
---
# <a name="keys-primary"></a><span data-ttu-id="cd5f4-102">主键</span><span class="sxs-lookup"><span data-stu-id="cd5f4-102">Keys (primary)</span></span>

<span data-ttu-id="cd5f4-103">键用作每个实体实例的主要唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-103">A key serves as the primary unique identifier for each entity instance.</span></span> <span data-ttu-id="cd5f4-104">使用关系数据库时，这会映射到主键的概念。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-104">When using a relational database this maps to the concept of a *primary key*.</span></span> <span data-ttu-id="cd5f4-105">您还可以配置不是主键的唯一标识符（有关详细信息，请参阅[备用键](alternate-keys.md)</span><span class="sxs-lookup"><span data-stu-id="cd5f4-105">You can also configure a unique identifier that is not the primary key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

## <a name="conventions"></a><span data-ttu-id="cd5f4-106">约定</span><span class="sxs-lookup"><span data-stu-id="cd5f4-106">Conventions</span></span>

<span data-ttu-id="cd5f4-107">按照约定，会将名为 `Id` 或 `<type name>Id` 的属性配置为一个实体的键。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-107">By convention, a property named `Id` or `<type name>Id` will be configured as the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="cd5f4-108">数据注释</span><span class="sxs-lookup"><span data-stu-id="cd5f4-108">Data Annotations</span></span>

<span data-ttu-id="cd5f4-109">可以使用数据注释将单个属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-109">You can use Data Annotations to configure a single property to be the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=3,4)] -->
``` csharp
class Car
{
    [Key]
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="cd5f4-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="cd5f4-110">Fluent API</span></span>

<span data-ttu-id="cd5f4-111">可以使用 Fluent API 将单个属性配置为实体的键。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-111">You can use the Fluent API to configure a single property to be the key of an entity.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => c.LicensePlate);
    }
}

class Car
{
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<span data-ttu-id="cd5f4-112">Fluent API 还可用于将多个属性配置为实体的键（称为复合键）。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-112">You can also use the Fluent API to configure multiple properties to be the key of an entity (known as a composite key).</span></span> <span data-ttu-id="cd5f4-113">只能使用 Fluent API 配置复合键 - 不能使用约定来设置复合键，也不能使用数据注释来配置复合键。</span><span class="sxs-lookup"><span data-stu-id="cd5f4-113">Composite keys can only be configured using the Fluent API - conventions will never setup a composite key and you can not use Data Annotations to configure one.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```
