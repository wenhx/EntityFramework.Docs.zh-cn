---
title: 继承-EF Core
description: 如何使用 Entity Framework Core 配置实体类型继承
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 4d43a432174c92ab7f3f9d78a234aefb0a4a17e8
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824674"
---
# <a name="inheritance"></a><span data-ttu-id="515ac-103">继承</span><span class="sxs-lookup"><span data-stu-id="515ac-103">Inheritance</span></span>

<span data-ttu-id="515ac-104">EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。</span><span class="sxs-lookup"><span data-stu-id="515ac-104">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="515ac-105">约定</span><span class="sxs-lookup"><span data-stu-id="515ac-105">Conventions</span></span>

<span data-ttu-id="515ac-106">默认情况下，由数据库提供程序确定如何在数据库中表示继承。</span><span class="sxs-lookup"><span data-stu-id="515ac-106">By default, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="515ac-107">有关如何使用关系数据库提供程序来处理此情况的详细说明，请参阅[继承（关系数据库）](relational/inheritance.md) 。</span><span class="sxs-lookup"><span data-stu-id="515ac-107">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="515ac-108">如果模型中显式包括两个或更多个继承类型，则 EF 仅会设置继承。</span><span class="sxs-lookup"><span data-stu-id="515ac-108">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="515ac-109">EF 不会扫描模型中未包含的基类型或派生类型。</span><span class="sxs-lookup"><span data-stu-id="515ac-109">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="515ac-110">可以通过为继承层次结构中的每个类型公开*DbSet\<TEntity >* ，在模型中包含类型。</span><span class="sxs-lookup"><span data-stu-id="515ac-110">You can include types in the model by exposing a *DbSet\<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="515ac-111">如果不想公开层次结构中一个或多个实体的*DbSet\<TEntity >* ，可以使用熟知的 API 来确保它们包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="515ac-111">If you don't want to expose a *DbSet\<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="515ac-112">如果不依赖约定，可以使用 `HasBaseType`显式指定基类型。</span><span class="sxs-lookup"><span data-stu-id="515ac-112">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="515ac-113">您可以使用 `.HasBaseType((Type)null)` 从层次结构中删除实体类型。</span><span class="sxs-lookup"><span data-stu-id="515ac-113">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="515ac-114">数据注释</span><span class="sxs-lookup"><span data-stu-id="515ac-114">Data Annotations</span></span>

<span data-ttu-id="515ac-115">不能使用数据批注来配置继承。</span><span class="sxs-lookup"><span data-stu-id="515ac-115">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="515ac-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="515ac-116">Fluent API</span></span>

<span data-ttu-id="515ac-117">用于继承的流畅 API 取决于所使用的数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="515ac-117">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="515ac-118">有关可为关系数据库提供程序执行的配置，请参阅[继承（关系数据库）](relational/inheritance.md) 。</span><span class="sxs-lookup"><span data-stu-id="515ac-118">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
