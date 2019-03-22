---
title: 继承的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 754be334-dd21-450e-9d22-2591e80012a2
uid: core/modeling/inheritance
ms.openlocfilehash: f6b5c8f5a398ac1e28e29bc17f0674c5b76d7b20
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319122"
---
# <a name="inheritance"></a><span data-ttu-id="a16ed-102">继承</span><span class="sxs-lookup"><span data-stu-id="a16ed-102">Inheritance</span></span>

<span data-ttu-id="a16ed-103">EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。</span><span class="sxs-lookup"><span data-stu-id="a16ed-103">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="a16ed-104">约定</span><span class="sxs-lookup"><span data-stu-id="a16ed-104">Conventions</span></span>

<span data-ttu-id="a16ed-105">按照约定，负责数据库提供程序，以确定如何继承将表示在数据库中。</span><span class="sxs-lookup"><span data-stu-id="a16ed-105">By convention, it is up to the database provider to determine how inheritance will be represented in the database.</span></span> <span data-ttu-id="a16ed-106">请参阅[继承 （关系数据库）](relational/inheritance.md)这与关系数据库提供程序的处理方式。</span><span class="sxs-lookup"><span data-stu-id="a16ed-106">See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.</span></span>

<span data-ttu-id="a16ed-107">如果两个或多个继承的类型显式包含在模型中，EF 将仅设置继承。</span><span class="sxs-lookup"><span data-stu-id="a16ed-107">EF will only setup inheritance if two or more inherited types are explicitly included in the model.</span></span> <span data-ttu-id="a16ed-108">EF 不会扫描的基类或派生类型，否则不包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="a16ed-108">EF will not scan for base or derived types that were not otherwise included in the model.</span></span> <span data-ttu-id="a16ed-109">可以在模型中包含类型，通过公开*DbSet<TEntity>* 继承层次结构中每个类型。</span><span class="sxs-lookup"><span data-stu-id="a16ed-109">You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceDbSets.cs?highlight=3-4&name=Model)]

<span data-ttu-id="a16ed-110">如果不想要公开*DbSet<TEntity>* 对于层次结构中的一个或多个实体，你可以使用 Fluent API 以确保它们都包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="a16ed-110">If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.</span></span>
<span data-ttu-id="a16ed-111">如果您不依赖于约定，可以指定使用显式的基类型`HasBaseType`。</span><span class="sxs-lookup"><span data-stu-id="a16ed-111">And if you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/InheritanceModelBuilder.cs?highlight=7&name=Context)]

> [!NOTE]
> <span data-ttu-id="a16ed-112">可以使用`.HasBaseType((Type)null)`从层次结构中删除某个实体类型。</span><span class="sxs-lookup"><span data-stu-id="a16ed-112">You can use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a16ed-113">数据注释</span><span class="sxs-lookup"><span data-stu-id="a16ed-113">Data Annotations</span></span>

<span data-ttu-id="a16ed-114">不能使用数据注释来配置继承。</span><span class="sxs-lookup"><span data-stu-id="a16ed-114">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a16ed-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a16ed-115">Fluent API</span></span>

<span data-ttu-id="a16ed-116">用于继承的 Fluent API 取决于正在使用的数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="a16ed-116">The Fluent API for inheritance depends on the database provider you are using.</span></span> <span data-ttu-id="a16ed-117">请参阅[继承 （关系数据库）](relational/inheritance.md)可以针对关系数据库提供程序执行的配置。</span><span class="sxs-lookup"><span data-stu-id="a16ed-117">See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.</span></span>
