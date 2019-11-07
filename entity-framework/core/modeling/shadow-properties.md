---
title: 阴影属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: ab57358dd247e32c4ca0f57d07b4cb98f2b85d29
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655956"
---
# <a name="shadow-properties"></a><span data-ttu-id="ab2f9-102">阴影属性</span><span class="sxs-lookup"><span data-stu-id="ab2f9-102">Shadow Properties</span></span>

<span data-ttu-id="ab2f9-103">影子属性是未在 .NET 实体类中定义但在 EF Core 模型中为该实体类型定义的属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="ab2f9-104">这些属性的值和状态纯粹在更改跟踪器中进行维护。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span>

<span data-ttu-id="ab2f9-105">当数据库中的数据不应在映射的实体类型上公开时，阴影属性非常有用。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span> <span data-ttu-id="ab2f9-106">它们最常用于外键属性，其中两个实体之间的关系由数据库中的外键值表示，但使用实体类型之间的导航属性在实体类型上管理关系。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-106">They are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span>

<span data-ttu-id="ab2f9-107">可以通过 `ChangeTracker` API 获取和更改影子属性值。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-107">Shadow property values can be obtained and changed through the `ChangeTracker` API.</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="ab2f9-108">可以通过 `EF.Property` 静态方法在 LINQ 查询中引用影子属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-108">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method.</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a><span data-ttu-id="ab2f9-109">约定</span><span class="sxs-lookup"><span data-stu-id="ab2f9-109">Conventions</span></span>

<span data-ttu-id="ab2f9-110">如果发现了关系，但在依赖实体类中找不到外键属性，则可以按约定创建阴影属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-110">Shadow properties can be created by convention when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span> <span data-ttu-id="ab2f9-111">在这种情况下，将引入阴影外键属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-111">In this case, a shadow foreign key property will be introduced.</span></span> <span data-ttu-id="ab2f9-112">影子外键属性将命名为 `<navigation property name><principal key property name>` （指向主体实体的依赖实体上的导航用于命名）。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-112">The shadow foreign key property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="ab2f9-113">如果主体键属性名称包含导航属性的名称，则该名称将只 `<principal key property name>`。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-113">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="ab2f9-114">如果依赖实体上没有导航属性，则会在其位置使用主体类型名称。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-114">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="ab2f9-115">例如，下面的代码列表将导致向 `Post` 实体引入 `BlogId` 影子属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-115">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions)]

## <a name="data-annotations"></a><span data-ttu-id="ab2f9-116">数据注释</span><span class="sxs-lookup"><span data-stu-id="ab2f9-116">Data Annotations</span></span>

<span data-ttu-id="ab2f9-117">不能通过数据批注创建阴影属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-117">Shadow properties can not be created with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="ab2f9-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ab2f9-118">Fluent API</span></span>

<span data-ttu-id="ab2f9-119">你可以使用 "熟知 API" 配置阴影属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-119">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="ab2f9-120">在您调用了的字符串重载之后 `Property` 您可以将对其他属性的任何配置调用链接在一起。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-120">Once you have called the string overload of `Property` you can chain any of the configuration calls you would for other properties.</span></span>

<span data-ttu-id="ab2f9-121">如果提供给 `Property` 方法的名称与现有属性（在实体类中定义）的名称相匹配，则代码将配置该现有属性，而不是引入新的阴影属性。</span><span class="sxs-lookup"><span data-stu-id="ab2f9-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]
