---
title: 继承-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: abc1caa4d3839b7cdb52b316bcfc8f648b609b70
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655689"
---
# <a name="inheritance"></a><span data-ttu-id="05864-102">继承</span><span class="sxs-lookup"><span data-stu-id="05864-102">Inheritance</span></span>

<span data-ttu-id="05864-103">EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。</span><span class="sxs-lookup"><span data-stu-id="05864-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="05864-104">约定</span><span class="sxs-lookup"><span data-stu-id="05864-104">Conventions</span></span>

<span data-ttu-id="05864-105">按照约定，由数据库提供商确定如何在数据库中表示继承。</span><span class="sxs-lookup"><span data-stu-id="05864-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="05864-106">有关如何使用关系数据库提供程序来处理此情况的详细说明，请参阅[继承（关系数据库）](relational/inheritance.md) 。</span><span class="sxs-lookup"><span data-stu-id="05864-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="05864-107">如果模型中显式包括两个或更多个继承类型，则 EF 仅会设置继承。</span><span class="sxs-lookup"><span data-stu-id="05864-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="05864-108">EF 不会扫描模型中未包含的基类型或派生类型。</span><span class="sxs-lookup"><span data-stu-id="05864-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="05864-109">可以通过为继承层次结构中的每个类型公开*DbSet\<TEntity >* ，在模型中包含类型。</span><span class="sxs-lookup"><span data-stu-id="05864-109">You can include types in the model by exposing a *DbSet\<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="05864-110">如果不想公开层次结构中一个或多个实体的*DbSet\<TEntity >* ，可以使用熟知的 API 来确保它们包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="05864-110">If you don't want to expose a *DbSet\<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="05864-111">如果不依赖约定，可以使用 `HasBaseType`显式指定基类型。</span><span class="sxs-lookup"><span data-stu-id="05864-111">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="05864-112">您可以使用 `.HasBaseType((Type)null)` 从层次结构中删除实体类型。</span><span class="sxs-lookup"><span data-stu-id="05864-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="05864-113">数据注释</span><span class="sxs-lookup"><span data-stu-id="05864-113">Data Annotations</span></span>

<span data-ttu-id="05864-114">不能使用数据批注来配置继承。</span><span class="sxs-lookup"><span data-stu-id="05864-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="05864-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="05864-115">Fluent API</span></span>

<span data-ttu-id="05864-116">用于继承的流畅 API 取决于所使用的数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="05864-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="05864-117">有关可为关系数据库提供程序执行的配置，请参阅[继承（关系数据库）](relational/inheritance.md) 。</span><span class="sxs-lookup"><span data-stu-id="05864-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
