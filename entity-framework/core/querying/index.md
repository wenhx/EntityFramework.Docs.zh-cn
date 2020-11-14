---
title: 查询数据 - EF Core
description: 有关在 Entity Framework Core 中查询的信息的概述
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430113"
---
# <a name="querying-data"></a><span data-ttu-id="14af8-103">查询数据</span><span class="sxs-lookup"><span data-stu-id="14af8-103">Querying Data</span></span>

<span data-ttu-id="14af8-104">Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="14af8-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="14af8-105">通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="14af8-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="14af8-106">它使用你派生得到的上下文和实体类来引用数据库对象。</span><span class="sxs-lookup"><span data-stu-id="14af8-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="14af8-107">EF Core 将 LINQ 查询的表示形式传递给数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="14af8-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="14af8-108">反过来，数据库提供程序将其转换为数据库特定的查询语言（例如，用于关系数据库的 SQL）。</span><span class="sxs-lookup"><span data-stu-id="14af8-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="14af8-109">即使结果中返回的实体已存在于上下文中，也始终对数据库执行查询。</span><span class="sxs-lookup"><span data-stu-id="14af8-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="14af8-110">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview)。</span><span class="sxs-lookup"><span data-stu-id="14af8-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="14af8-111">以下片段显示的几个示例展示了如何使用 Entity Framework Core 完成常见任务。</span><span class="sxs-lookup"><span data-stu-id="14af8-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="14af8-112">加载所有数据</span><span class="sxs-lookup"><span data-stu-id="14af8-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="14af8-113">加载单个实体</span><span class="sxs-lookup"><span data-stu-id="14af8-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="14af8-114">筛选</span><span class="sxs-lookup"><span data-stu-id="14af8-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="14af8-115">延伸阅读</span><span class="sxs-lookup"><span data-stu-id="14af8-115">Further readings</span></span>

- <span data-ttu-id="14af8-116">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="14af8-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="14af8-117">若要更详细地了解 EF Core 中的查询处理方式，请参阅[查询的工作原理](xref:core/querying/how-query-works)。</span><span class="sxs-lookup"><span data-stu-id="14af8-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
