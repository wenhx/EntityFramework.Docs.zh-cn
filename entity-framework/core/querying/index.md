---
title: 查询数据 - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 009235c3673a414e06d1a64f9877b60e7cde97b0
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181919"
---
# <a name="querying-data"></a><span data-ttu-id="d445e-102">查询数据</span><span class="sxs-lookup"><span data-stu-id="d445e-102">Querying Data</span></span>

<span data-ttu-id="d445e-103">Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="d445e-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="d445e-104">通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="d445e-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="d445e-105">它使用你派生得到的上下文和实体类来引用数据库对象。</span><span class="sxs-lookup"><span data-stu-id="d445e-105">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="d445e-106">EF Core 将 LINQ 查询的表示形式传递给数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="d445e-106">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="d445e-107">反过来，数据库提供程序将其转换为数据库特定的查询语言（例如，用于关系数据库的 SQL）。</span><span class="sxs-lookup"><span data-stu-id="d445e-107">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="d445e-108">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。</span><span class="sxs-lookup"><span data-stu-id="d445e-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="d445e-109">以下片段显示的几个示例展示了如何使用 Entity Framework Core 完成常见任务。</span><span class="sxs-lookup"><span data-stu-id="d445e-109">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="d445e-110">加载所有数据</span><span class="sxs-lookup"><span data-stu-id="d445e-110">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="d445e-111">加载单个实体</span><span class="sxs-lookup"><span data-stu-id="d445e-111">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="d445e-112">筛选</span><span class="sxs-lookup"><span data-stu-id="d445e-112">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="d445e-113">延伸阅读</span><span class="sxs-lookup"><span data-stu-id="d445e-113">Further readings</span></span>

- <span data-ttu-id="d445e-114">了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息</span><span class="sxs-lookup"><span data-stu-id="d445e-114">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="d445e-115">要更详细地了解如何在 EF Core 中处理查询，请参阅[查询的工作原理](xref:core/querying/how-query-works)。</span><span class="sxs-lookup"><span data-stu-id="d445e-115">For more detailed information on how a query is processed in EF Core, see [How Query Works](xref:core/querying/how-query-works).</span></span>
