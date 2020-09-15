---
title: 查询的工作原理 - EF Core
description: 关于 Entity Framework Core 如何在内部编译和执行查询的常规信息
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e0e489f5f797b6f7d8f4860539a538dba90bd1c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616365"
---
# <a name="how-queries-work"></a><span data-ttu-id="5a8c0-103">查询的工作原理</span><span class="sxs-lookup"><span data-stu-id="5a8c0-103">How Queries Work</span></span>

<span data-ttu-id="5a8c0-104">Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="5a8c0-105">通过 LINQ 可使用 C#（或你选择的 .NET 语言）基于派生上下文和实体类编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="5a8c0-106">查询的寿命</span><span class="sxs-lookup"><span data-stu-id="5a8c0-106">The life of a query</span></span>

<span data-ttu-id="5a8c0-107">下面的描述是每个查询所经历的过程的综合概述。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-107">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="5a8c0-108">LINQ 查询由 Entity Framework Core 处理，用于生成已准备好由数据库提供程序处理的表示形式</span><span class="sxs-lookup"><span data-stu-id="5a8c0-108">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="5a8c0-109">结果会被缓存，以便每次执行查询时无需进行此处理</span><span class="sxs-lookup"><span data-stu-id="5a8c0-109">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="5a8c0-110">结果会传递到数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="5a8c0-110">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="5a8c0-111">数据库提供程序确定可以在数据库中评估查询的哪些部分</span><span class="sxs-lookup"><span data-stu-id="5a8c0-111">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="5a8c0-112">查询的这些部分会转换为特定数据库的查询语言（例如关系数据库的 SQL）</span><span class="sxs-lookup"><span data-stu-id="5a8c0-112">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="5a8c0-113">查询会发送到数据库并返回结果集（返回的是数据库中的值，而不是实体实例中的）</span><span class="sxs-lookup"><span data-stu-id="5a8c0-113">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="5a8c0-114">对于结果集中的每一项</span><span class="sxs-lookup"><span data-stu-id="5a8c0-114">For each item in the result set</span></span>
   1. <span data-ttu-id="5a8c0-115">如果该查询是跟踪查询，EF 会检查数据是否表示上下文实例内更改跟踪器中的现有实体</span><span class="sxs-lookup"><span data-stu-id="5a8c0-115">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="5a8c0-116">如果是，则会返回现有实体</span><span class="sxs-lookup"><span data-stu-id="5a8c0-116">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="5a8c0-117">如果不是，则会创建新实体、设置更改跟踪并返回该新实体</span><span class="sxs-lookup"><span data-stu-id="5a8c0-117">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="5a8c0-118">如果该查询是非跟踪查询，将始终创建并返回新实体</span><span class="sxs-lookup"><span data-stu-id="5a8c0-118">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="5a8c0-119">执行查询时</span><span class="sxs-lookup"><span data-stu-id="5a8c0-119">When queries are executed</span></span>

<span data-ttu-id="5a8c0-120">调用 LINQ 运算符时，只会构建查询在内存中的表示形式。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-120">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="5a8c0-121">使用结果时，查询只会发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-121">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="5a8c0-122">导致查询发送到数据库的最常见操作如下：</span><span class="sxs-lookup"><span data-stu-id="5a8c0-122">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="5a8c0-123">在 `for` 循环中循环访问结果</span><span class="sxs-lookup"><span data-stu-id="5a8c0-123">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="5a8c0-124">使用 `ToList`、`ToArray`、`Single`、`Count` 等操作或等效的异步重载</span><span class="sxs-lookup"><span data-stu-id="5a8c0-124">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="5a8c0-125">\*\*\*\* 始终验证用户输入：虽然 EF Core通过在查询中使用参数和转义文字来防止 SQL 注入攻击，但它不会验证输入。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-125">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="5a8c0-126">根据应用程序的要求，在将 LINQ 查询中使用的来自不受信任的源的值分配给实体属性或传递给其他 EF Core API 之前，应执行相应的验证。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-126">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="5a8c0-127">这包括用于动态构造查询的所有用户输入。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-127">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="5a8c0-128">即使在使用 LINQ 时，如果接受用于生成表达式的用户输入，也会需要确保只能构造预期表达式。</span><span class="sxs-lookup"><span data-stu-id="5a8c0-128">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
