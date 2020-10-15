---
title: 影子和索引器属性-EF Core
description: 在 Entity Framework Core 模型中配置影子和索引器属性
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 417ab57a4a77ecf626e54eeca900744d84e3fe08
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063889"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="910b8-103">影子和索引器属性</span><span class="sxs-lookup"><span data-stu-id="910b8-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="910b8-104">影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。</span><span class="sxs-lookup"><span data-stu-id="910b8-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="910b8-105">这些属性的值和状态纯粹在更改跟踪器中进行维护。</span><span class="sxs-lookup"><span data-stu-id="910b8-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="910b8-106">当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。</span><span class="sxs-lookup"><span data-stu-id="910b8-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="910b8-107">外键阴影属性</span><span class="sxs-lookup"><span data-stu-id="910b8-107">Foreign key shadow properties</span></span>

<span data-ttu-id="910b8-108">影子属性最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体类型之间的导航属性在实体类型上管理关系。</span><span class="sxs-lookup"><span data-stu-id="910b8-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="910b8-109">按照约定，当发现关系但在依赖实体类中找不到外键属性时，EF 会引入一个影子属性。</span><span class="sxs-lookup"><span data-stu-id="910b8-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="910b8-110">该属性将被命名 `<navigation property name><principal key property name>` (在依赖实体上导航（指向主体实体）用于命名) 。</span><span class="sxs-lookup"><span data-stu-id="910b8-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="910b8-111">如果主体键属性名称包含导航属性的名称，则该名称将只是 `<principal key property name>` 。</span><span class="sxs-lookup"><span data-stu-id="910b8-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="910b8-112">如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。</span><span class="sxs-lookup"><span data-stu-id="910b8-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="910b8-113">例如，下面的代码列表将导致将 `BlogId` 影子属性引入到 `Post` 实体：</span><span class="sxs-lookup"><span data-stu-id="910b8-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="910b8-114">配置阴影属性</span><span class="sxs-lookup"><span data-stu-id="910b8-114">Configuring shadow properties</span></span>

<span data-ttu-id="910b8-115">你可以使用 "熟知 API" 配置阴影属性。</span><span class="sxs-lookup"><span data-stu-id="910b8-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="910b8-116">调用的字符串重载后 `Property` ，可以链接到其他属性的任何配置调用。</span><span class="sxs-lookup"><span data-stu-id="910b8-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="910b8-117">在下面的示例中，由于 `Blog` 没有名为的 CLR 属性，因此将 `LastUpdated` 创建一个影子属性：</span><span class="sxs-lookup"><span data-stu-id="910b8-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="910b8-118">如果提供给方法的名称 `Property` 与现有属性的名称相匹配 (影子属性或在实体类) 上定义的属性，则代码将配置该现有属性，而不是引入新的阴影属性。</span><span class="sxs-lookup"><span data-stu-id="910b8-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="910b8-119">访问阴影属性</span><span class="sxs-lookup"><span data-stu-id="910b8-119">Accessing shadow properties</span></span>

<span data-ttu-id="910b8-120">可以通过 API 获取和更改影子属性值 `ChangeTracker` ：</span><span class="sxs-lookup"><span data-stu-id="910b8-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="910b8-121">可以通过静态方法在 LINQ 查询中引用影子属性 `EF.Property` ：</span><span class="sxs-lookup"><span data-stu-id="910b8-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="910b8-122">在无跟踪查询后，不能访问阴影属性，因为更改跟踪器不跟踪返回的实体。</span><span class="sxs-lookup"><span data-stu-id="910b8-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="910b8-123">属性包实体类型</span><span class="sxs-lookup"><span data-stu-id="910b8-123">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="910b8-124">EF Core 5.0 中添加了对属性包实体类型的支持。</span><span class="sxs-lookup"><span data-stu-id="910b8-124">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="910b8-125">仅包含索引器属性的实体类型称为 "属性包实体类型"。</span><span class="sxs-lookup"><span data-stu-id="910b8-125">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="910b8-126">这些实体类型没有阴影属性。</span><span class="sxs-lookup"><span data-stu-id="910b8-126">These entity types don't have shadow properties.</span></span> <span data-ttu-id="910b8-127">目前仅 `Dictionary<string, object>` 支持作为属性包实体类型。</span><span class="sxs-lookup"><span data-stu-id="910b8-127">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="910b8-128">这意味着，必须将其配置为具有唯一名称的共享实体类型，并且 `DbSet` 必须使用调用来实现相应的属性 `Set` 。</span><span class="sxs-lookup"><span data-stu-id="910b8-128">This means that it must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]