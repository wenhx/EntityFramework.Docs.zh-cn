---
title: 相关数据的显式加载 - EF Core
description: 使用 Entity Framework Core 显式加载相关数据
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 0cfc4b5304d81d5a58497857474804946d3f85d7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065714"
---
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="a09a3-103">相关数据的显式加载</span><span class="sxs-lookup"><span data-stu-id="a09a3-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="a09a3-104">显式加载</span><span class="sxs-lookup"><span data-stu-id="a09a3-104">Explicit loading</span></span>

<span data-ttu-id="a09a3-105">可以通过 `DbContext.Entry(...)` API 显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="a09a3-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

<span data-ttu-id="a09a3-106">还可以通过执行返回关联实体的单独查询来显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="a09a3-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="a09a3-107">如果已启用更改跟踪，则在查询具体化实体时，EF Core 将自动设置新加载的实体的导航属性以引用任何已加载的实体，并设置已加载实体的导航属性以引用新加载的实体。</span><span class="sxs-lookup"><span data-stu-id="a09a3-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="a09a3-108">查询相关实体</span><span class="sxs-lookup"><span data-stu-id="a09a3-108">Querying related entities</span></span>

<span data-ttu-id="a09a3-109">还可以获得表示导航属性内容的 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="a09a3-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="a09a3-110">这使你可对查询应用其他运算符。</span><span class="sxs-lookup"><span data-stu-id="a09a3-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="a09a3-111">例如，无需将关联实体加载到内存中，即可对关联实体执行聚合运算。</span><span class="sxs-lookup"><span data-stu-id="a09a3-111">For example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

<span data-ttu-id="a09a3-112">还可以筛选要加载到内存中的关联实体。</span><span class="sxs-lookup"><span data-stu-id="a09a3-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
