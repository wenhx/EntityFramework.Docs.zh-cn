---
title: 查询数据 - EF Core
description: 有关在 Entity Framework Core 中查询的信息的概述
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 028c640a17c4946158c86bdf1a663a4050f55921
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616326"
---
# <a name="querying-data"></a><span data-ttu-id="ceb70-103">查询数据</span><span class="sxs-lookup"><span data-stu-id="ceb70-103">Querying Data</span></span>

<span data-ttu-id="ceb70-104">Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="ceb70-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="ceb70-105">通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="ceb70-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="ceb70-106">它使用你派生得到的上下文和实体类来引用数据库对象。</span><span class="sxs-lookup"><span data-stu-id="ceb70-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="ceb70-107">EF Core 将 LINQ 查询的表示形式传递给数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="ceb70-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="ceb70-108">反过来，数据库提供程序将其转换为数据库特定的查询语言（例如，用于关系数据库的 SQL）。</span><span class="sxs-lookup"><span data-stu-id="ceb70-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="ceb70-109">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="ceb70-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="ceb70-110">以下片段显示的几个示例展示了如何使用 Entity Framework Core 完成常见任务。</span><span class="sxs-lookup"><span data-stu-id="ceb70-110">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="ceb70-111">加载所有数据</span><span class="sxs-lookup"><span data-stu-id="ceb70-111">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="ceb70-112">加载单个实体</span><span class="sxs-lookup"><span data-stu-id="ceb70-112">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="ceb70-113">筛选</span><span class="sxs-lookup"><span data-stu-id="ceb70-113">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="ceb70-114">延伸阅读</span><span class="sxs-lookup"><span data-stu-id="ceb70-114">Further readings</span></span>

- <span data-ttu-id="ceb70-115">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="ceb70-115">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="ceb70-116">若要更详细地了解 EF Core 中的查询处理方式，请参阅[查询的工作原理](xref:core/querying/how-query-works)。</span><span class="sxs-lookup"><span data-stu-id="ceb70-116">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
