---
title: 阴影属性-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 229cfd83f75b01dff9ac9ad30ee55c7cc727c19e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414709"
---
# <a name="shadow-properties"></a><span data-ttu-id="6b625-102">阴影属性</span><span class="sxs-lookup"><span data-stu-id="6b625-102">Shadow Properties</span></span>

<span data-ttu-id="6b625-103">影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。</span><span class="sxs-lookup"><span data-stu-id="6b625-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="6b625-104">这些属性的值和状态纯粹在更改跟踪器中进行维护。</span><span class="sxs-lookup"><span data-stu-id="6b625-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="6b625-105">当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。</span><span class="sxs-lookup"><span data-stu-id="6b625-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="6b625-106">外键阴影属性</span><span class="sxs-lookup"><span data-stu-id="6b625-106">Foreign key shadow properties</span></span>

<span data-ttu-id="6b625-107">影子属性最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体之间的导航属性在实体类型上管理关系各种.</span><span class="sxs-lookup"><span data-stu-id="6b625-107">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="6b625-108">按照约定，当发现关系但在依赖实体类中找不到外键属性时，EF 会引入一个影子属性。</span><span class="sxs-lookup"><span data-stu-id="6b625-108">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="6b625-109">属性将命名为 `<navigation property name><principal key property name>` （指向主体实体的依赖实体上的导航用于命名）。</span><span class="sxs-lookup"><span data-stu-id="6b625-109">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="6b625-110">如果主体键属性名称包含导航属性的名称，则该名称将只 `<principal key property name>`。</span><span class="sxs-lookup"><span data-stu-id="6b625-110">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="6b625-111">如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。</span><span class="sxs-lookup"><span data-stu-id="6b625-111">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="6b625-112">例如，下面的代码列表将导致向 `Post` 实体引入 `BlogId` 影子属性：</span><span class="sxs-lookup"><span data-stu-id="6b625-112">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="6b625-113">配置阴影属性</span><span class="sxs-lookup"><span data-stu-id="6b625-113">Configuring shadow properties</span></span>

<span data-ttu-id="6b625-114">你可以使用 "熟知 API" 配置阴影属性。</span><span class="sxs-lookup"><span data-stu-id="6b625-114">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="6b625-115">在您调用了 `Property`的字符串重载后，您可以将对其他属性的任何配置调用链接在一起。</span><span class="sxs-lookup"><span data-stu-id="6b625-115">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="6b625-116">在下面的示例中，由于 `Blog` 没有名为 `LastUpdated`的 CLR 属性，因此将创建一个影子属性：</span><span class="sxs-lookup"><span data-stu-id="6b625-116">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="6b625-117">如果提供给 `Property` 方法的名称与现有属性（在实体类中定义）的名称相匹配，则代码将配置该现有属性，而不是引入新的阴影属性。</span><span class="sxs-lookup"><span data-stu-id="6b625-117">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="6b625-118">访问阴影属性</span><span class="sxs-lookup"><span data-stu-id="6b625-118">Accessing shadow properties</span></span>

<span data-ttu-id="6b625-119">可以通过 `ChangeTracker` API 获取和更改影子属性值：</span><span class="sxs-lookup"><span data-stu-id="6b625-119">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="6b625-120">可以通过 `EF.Property` 静态方法在 LINQ 查询中引用阴影属性：</span><span class="sxs-lookup"><span data-stu-id="6b625-120">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```
