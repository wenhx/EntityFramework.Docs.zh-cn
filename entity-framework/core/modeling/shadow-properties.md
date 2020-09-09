---
title: 阴影属性-EF Core
description: 在 Entity Framework Core 模型中配置阴影属性
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 06dbba7a4f6da247e6afde96debb0762fa55ce7e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616714"
---
# <a name="shadow-properties"></a><span data-ttu-id="23baf-103">阴影属性</span><span class="sxs-lookup"><span data-stu-id="23baf-103">Shadow Properties</span></span>

<span data-ttu-id="23baf-104">影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。</span><span class="sxs-lookup"><span data-stu-id="23baf-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="23baf-105">这些属性的值和状态纯粹在更改跟踪器中进行维护。</span><span class="sxs-lookup"><span data-stu-id="23baf-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="23baf-106">当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。</span><span class="sxs-lookup"><span data-stu-id="23baf-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="23baf-107">外键阴影属性</span><span class="sxs-lookup"><span data-stu-id="23baf-107">Foreign key shadow properties</span></span>

<span data-ttu-id="23baf-108">影子属性最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体类型之间的导航属性在实体类型上管理关系。</span><span class="sxs-lookup"><span data-stu-id="23baf-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="23baf-109">按照约定，当发现关系但在依赖实体类中找不到外键属性时，EF 会引入一个影子属性。</span><span class="sxs-lookup"><span data-stu-id="23baf-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="23baf-110">该属性将被命名 `<navigation property name><principal key property name>` (在依赖实体上导航（指向主体实体）用于命名) 。</span><span class="sxs-lookup"><span data-stu-id="23baf-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="23baf-111">如果主体键属性名称包含导航属性的名称，则该名称将只是 `<principal key property name>` 。</span><span class="sxs-lookup"><span data-stu-id="23baf-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="23baf-112">如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。</span><span class="sxs-lookup"><span data-stu-id="23baf-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="23baf-113">例如，下面的代码列表将导致将 `BlogId` 影子属性引入到 `Post` 实体：</span><span class="sxs-lookup"><span data-stu-id="23baf-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="23baf-114">配置阴影属性</span><span class="sxs-lookup"><span data-stu-id="23baf-114">Configuring shadow properties</span></span>

<span data-ttu-id="23baf-115">你可以使用 "熟知 API" 配置阴影属性。</span><span class="sxs-lookup"><span data-stu-id="23baf-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="23baf-116">调用的字符串重载后 `Property` ，可以链接到其他属性的任何配置调用。</span><span class="sxs-lookup"><span data-stu-id="23baf-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="23baf-117">在下面的示例中，由于 `Blog` 没有名为的 CLR 属性，因此将 `LastUpdated` 创建一个影子属性：</span><span class="sxs-lookup"><span data-stu-id="23baf-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="23baf-118">如果提供给方法的名称 `Property` 与现有属性的名称相匹配 (影子属性或在实体类) 上定义的属性，则代码将配置该现有属性，而不是引入新的阴影属性。</span><span class="sxs-lookup"><span data-stu-id="23baf-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="23baf-119">访问阴影属性</span><span class="sxs-lookup"><span data-stu-id="23baf-119">Accessing shadow properties</span></span>

<span data-ttu-id="23baf-120">可以通过 API 获取和更改影子属性值 `ChangeTracker` ：</span><span class="sxs-lookup"><span data-stu-id="23baf-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="23baf-121">可以通过静态方法在 LINQ 查询中引用影子属性 `EF.Property` ：</span><span class="sxs-lookup"><span data-stu-id="23baf-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="23baf-122">在无跟踪查询后，不能访问阴影属性，因为更改跟踪器不跟踪返回的实体。</span><span class="sxs-lookup"><span data-stu-id="23baf-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
