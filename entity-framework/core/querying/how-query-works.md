---
title: 查询的工作原理 - EF Core
author: rowanmiller
ms.date: 09/26/2018
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: ba0d68469530e6272ffbb51946d7856122a261c7
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656242"
---
# <a name="how-queries-work"></a><span data-ttu-id="2be03-102">查询的工作原理</span><span class="sxs-lookup"><span data-stu-id="2be03-102">How Queries Work</span></span>

<span data-ttu-id="2be03-103">Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="2be03-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="2be03-104">通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）基于派生上下文和实体类编写强类型查询。</span><span class="sxs-lookup"><span data-stu-id="2be03-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="2be03-105">查询的生命周期</span><span class="sxs-lookup"><span data-stu-id="2be03-105">The life of a query</span></span>

<span data-ttu-id="2be03-106">下面是每个查询所经历的过程的高级概述。</span><span class="sxs-lookup"><span data-stu-id="2be03-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="2be03-107">LINQ 查询由 Entity Framework Core 处理，用于生成已准备好由数据库提供程序处理的表示形式</span><span class="sxs-lookup"><span data-stu-id="2be03-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="2be03-108">结果会被缓存，以便每次执行查询时无需进行此处理</span><span class="sxs-lookup"><span data-stu-id="2be03-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="2be03-109">结果会传递到数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="2be03-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="2be03-110">数据库提供程序会识别出查询的哪些部分可以在数据库中求值</span><span class="sxs-lookup"><span data-stu-id="2be03-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="2be03-111">查询的这些部分会转换为特定数据库的查询语言（例如，关系数据库的 SQL）</span><span class="sxs-lookup"><span data-stu-id="2be03-111">These parts of the query are translated to database specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="2be03-112">一个或多个查询会发送到数据库并返回结果集（返回的是数据库中的值，而不是实体实例中的）</span><span class="sxs-lookup"><span data-stu-id="2be03-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="2be03-113">对于结果集中的每一项</span><span class="sxs-lookup"><span data-stu-id="2be03-113">For each item in the result set</span></span>
   1. <span data-ttu-id="2be03-114">如果这是跟踪查询，EF 会检查数据是否表示已在上下文实例的更改跟踪器中的实体</span><span class="sxs-lookup"><span data-stu-id="2be03-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="2be03-115">如果是，则会返回现有实体</span><span class="sxs-lookup"><span data-stu-id="2be03-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="2be03-116">如果不是，则会创建新实体、设置更改跟踪并返回该新实体</span><span class="sxs-lookup"><span data-stu-id="2be03-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="2be03-117">如果这是非跟踪查询，EF 会检查数据是否表示已在此查询的结果集中的实体</span><span class="sxs-lookup"><span data-stu-id="2be03-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="2be03-118">如果是，则会返回现有实体 <sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="2be03-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="2be03-119">如果不是，则会创建新实体并返回该新实体</span><span class="sxs-lookup"><span data-stu-id="2be03-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="2be03-120"><sup>(1)</sup> 非跟踪查询使用弱引用跟踪已返回的实体。</span><span class="sxs-lookup"><span data-stu-id="2be03-120"><sup>(1)</sup> No-tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="2be03-121">如果具有相同标识的上一个结果超出范围，并遇到垃圾回收机制执行，则可能会获得新的实体实例。</span><span class="sxs-lookup"><span data-stu-id="2be03-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="2be03-122">执行查询时</span><span class="sxs-lookup"><span data-stu-id="2be03-122">When queries are executed</span></span>

<span data-ttu-id="2be03-123">调用 LINQ 运算符时，只会生成查询的内存中表示形式。</span><span class="sxs-lookup"><span data-stu-id="2be03-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="2be03-124">使用结果时，查询只会发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="2be03-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="2be03-125">导致查询发送到数据库的最常见操作如下：</span><span class="sxs-lookup"><span data-stu-id="2be03-125">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="2be03-126">在 `for` 循环中循环访问结果</span><span class="sxs-lookup"><span data-stu-id="2be03-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="2be03-127">使用 `ToList`、`ToArray`、`Single`、`Count` 等运算符</span><span class="sxs-lookup"><span data-stu-id="2be03-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="2be03-128">将查询结果数据绑定到 UI</span><span class="sxs-lookup"><span data-stu-id="2be03-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="2be03-129">**始终验证用户输入：** 尽管 EF Core 通过在查询中使用参数和转义文本来防止 SQL 注入攻击，但它不会验证输入。</span><span class="sxs-lookup"><span data-stu-id="2be03-129">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="2be03-130">根据应用程序的要求，在将 LINQ 查询中使用的来自不受信任的源的值分配给实体属性或传递给其他 EF Core API 之前，应执行相应的验证。</span><span class="sxs-lookup"><span data-stu-id="2be03-130">Appropriate validation, per the application's requirements, should be performed before values from untrusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="2be03-131">这包括用于动态构造查询的所有用户输入。</span><span class="sxs-lookup"><span data-stu-id="2be03-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="2be03-132">即使在使用 LINQ 时，如果接受用于生成表达式的用户输入，也会需要确保只能构造预期表达式。</span><span class="sxs-lookup"><span data-stu-id="2be03-132">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
