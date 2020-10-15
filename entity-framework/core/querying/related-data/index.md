---
title: 加载相关数据 - EF Core
description: 使用 Entity Framework Core 加载相关数据的不同策略
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063668"
---
# <a name="loading-related-data"></a><span data-ttu-id="90790-103">加载相关数据</span><span class="sxs-lookup"><span data-stu-id="90790-103">Loading Related Data</span></span>

<span data-ttu-id="90790-104">Entity Framework Core 允许你在模型中使用导航属性来加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="90790-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="90790-105">有三种常见的 O/RM 模式可用于加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="90790-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="90790-106">[预先加载](xref:core/querying/related-data/eager)表示从数据库中加载关联数据，作为初始查询的一部分。</span><span class="sxs-lookup"><span data-stu-id="90790-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="90790-107">[显式加载](xref:core/querying/related-data/explicit)表示稍后从数据库中显式加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="90790-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="90790-108">[延迟加载](xref:core/querying/related-data/lazy)表示在访问导航属性时，从数据库中以透明方式加载关联数据。</span><span class="sxs-lookup"><span data-stu-id="90790-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="90790-109">可以在 GitHub 上的此部分中查看[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData)。</span><span class="sxs-lookup"><span data-stu-id="90790-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) under this section on GitHub.</span></span>
