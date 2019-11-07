---
title: 索引-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: d1b5cd6853cd24f7e1aa3aace2f0a3455c657cc1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655700"
---
# <a name="indexes"></a><span data-ttu-id="a8604-102">索引</span><span class="sxs-lookup"><span data-stu-id="a8604-102">Indexes</span></span>

<span data-ttu-id="a8604-103">索引是跨多个数据存储区的常见概念。</span><span class="sxs-lookup"><span data-stu-id="a8604-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="a8604-104">尽管它们在数据存储中的实现可能会有所不同，但也可用于基于列（或一组列）更高效地进行查找。</span><span class="sxs-lookup"><span data-stu-id="a8604-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="a8604-105">约定</span><span class="sxs-lookup"><span data-stu-id="a8604-105">Conventions</span></span>

<span data-ttu-id="a8604-106">按照约定，将在用作外键的每个属性（或一组属性）中创建索引。</span><span class="sxs-lookup"><span data-stu-id="a8604-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="a8604-107">数据注释</span><span class="sxs-lookup"><span data-stu-id="a8604-107">Data Annotations</span></span>

<span data-ttu-id="a8604-108">不能使用数据批注创建索引。</span><span class="sxs-lookup"><span data-stu-id="a8604-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="a8604-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="a8604-109">Fluent API</span></span>

<span data-ttu-id="a8604-110">您可以使用熟知的 API 来指定单个属性的索引。</span><span class="sxs-lookup"><span data-stu-id="a8604-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="a8604-111">默认情况下，索引不是唯一的。</span><span class="sxs-lookup"><span data-stu-id="a8604-111">By default, indexes are non-unique.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=7,8)]

<span data-ttu-id="a8604-112">您还可以指定索引应是唯一的，这意味着对于给定的属性，不能有两个实体具有相同的值。</span><span class="sxs-lookup"><span data-stu-id="a8604-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=ModelBuilder&highlight=3)]

<span data-ttu-id="a8604-113">您还可以为多个列指定索引。</span><span class="sxs-lookup"><span data-stu-id="a8604-113">You can also specify an index over more than one column.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=7,8)]

> [!TIP]  
> <span data-ttu-id="a8604-114">每个不同的属性集只有一个索引。</span><span class="sxs-lookup"><span data-stu-id="a8604-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="a8604-115">如果使用 "熟知 API" 来配置已定义索引的属性集的索引（按照约定或以前的配置），则会更改该索引的定义。</span><span class="sxs-lookup"><span data-stu-id="a8604-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="a8604-116">如果要进一步配置由约定创建的索引，则此操作非常有用。</span><span class="sxs-lookup"><span data-stu-id="a8604-116">This is useful if you want to further configure an index that was created by convention.</span></span>
