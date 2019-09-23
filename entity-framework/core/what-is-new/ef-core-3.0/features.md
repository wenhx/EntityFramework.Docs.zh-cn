---
title: EF Core 3.0 中的新增功能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: 528733d6eec33de2c9538541a6ed5be704b9d433
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005560"
---
# <a name="new-features-included-in-ef-core-30"></a><span data-ttu-id="75e79-102">EF Core 3.0 中包含的新功能</span><span class="sxs-lookup"><span data-stu-id="75e79-102">New features included in EF Core 3.0</span></span>

<span data-ttu-id="75e79-103">以下列表包括为 EF Core 3.0 计划的主要新功能。</span><span class="sxs-lookup"><span data-stu-id="75e79-103">The following list includes the major new features planned for EF Core 3.0.</span></span>

<span data-ttu-id="75e79-104">EF Core 3.0 是一个主要版本，还包含许多[中断性变更](xref:core/what-is-new/ef-core-3.0/breaking-changes)，即可能对现有应用程序产生负面影响的 API 改进。</span><span class="sxs-lookup"><span data-stu-id="75e79-104">EF Core 3.0 is a major release and also contains numerous [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-improvements"></a><span data-ttu-id="75e79-105">LINQ 的改进</span><span class="sxs-lookup"><span data-stu-id="75e79-105">LINQ improvements</span></span> 

<span data-ttu-id="75e79-106">通过 LINQ，可继续使用所选语言编写数据库查询，利用丰富的类型信息来提供 IntelliSense 和编译时类型检查。</span><span class="sxs-lookup"><span data-stu-id="75e79-106">LINQ enables you to write database queries without leaving your language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="75e79-107">而 LINQ 还允许编写包含任意表达式（方法调用或操作）的不限数量的复杂查询。</span><span class="sxs-lookup"><span data-stu-id="75e79-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="75e79-108">处理所有这些组合一直是 LINQ 提供程序面临的重大挑战。</span><span class="sxs-lookup"><span data-stu-id="75e79-108">Handling all those combinations has always been a significant challenge for LINQ providers.</span></span>
<span data-ttu-id="75e79-109">在 EF Core 3.0 中，我们重写了 LINQ 实现，以便将更多的表达式转换为 SQL，在更多情况下生成高效查询，有效发现低效查询，并更容易地在不中断现有应用程序和数据提供程序的情况下逐步引入新的查询功能和性能改进。</span><span class="sxs-lookup"><span data-stu-id="75e79-109">In EF Core 3.0, we've rewritten our LINQ implementation to enable translating more expressions into SQL, to generate efficient queries in more cases, to prevent inefficient queries from going undetected, and to make it easier for us to gradually introduce new query capabilities and performance improvementswithout breaking existing applications and data providers.</span></span>

### <a name="client-evaluation"></a><span data-ttu-id="75e79-110">客户端求值</span><span class="sxs-lookup"><span data-stu-id="75e79-110">Client evaluation</span></span>

<span data-ttu-id="75e79-111">EF Core 3.0 的主要设计更改与它如何处理无法转换为 SQL 或参数的 LINQ 表达式有关：</span><span class="sxs-lookup"><span data-stu-id="75e79-111">The main design change in EF Core 3.0 has to do with how it handles LINQ expressions that it cannot translate to SQL or parameters:</span></span>

<span data-ttu-id="75e79-112">在前几个版本中，EF Core 只仅明确查询的哪些部分可以转换为 SQL，并在客户端上执行查询的其余部分。</span><span class="sxs-lookup"><span data-stu-id="75e79-112">In the first few versions, EF Core simply figured out what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="75e79-113">在某些情况下，这种客户端执行是可取的，但在其他许多情况下，这可能会导致低效的查询。</span><span class="sxs-lookup"><span data-stu-id="75e79-113">This type of client-side execution can be desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>
<span data-ttu-id="75e79-114">例如，如果 EF Core 2.2 无法转换 `Where()` 调用中的谓词，则会执行一个不带筛选器的 SQL 语句，从数据库中读取所有行，然后在内存中对其进行筛选。</span><span class="sxs-lookup"><span data-stu-id="75e79-114">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed a SQL statement without a filter, read all all the rows from the database, and then filtered them in-memory.</span></span>
<span data-ttu-id="75e79-115">如果数据库包含少量行，那么这可能是可以接受的，但如果数据库包含大量行，则可能导致严重的性能问题甚至应用程序故障。</span><span class="sxs-lookup"><span data-stu-id="75e79-115">That may be acceptable if the database contains a small number of rows, but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>
<span data-ttu-id="75e79-116">在 EF Core 3.0 中，我们限制客户端评估仅发生在顶级投影（最后一次调用 `Select()`）上。</span><span class="sxs-lookup"><span data-stu-id="75e79-116">In EF Core 3.0 we have restricted client evaluation to only happen on the top-level projection (the last call to `Select()`).</span></span>
<span data-ttu-id="75e79-117">当 EF Core 3.0 检测到无法在查询中的任何其他位置转换的表达式时，它会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="75e79-117">When EF Core 3.0 detects expressions that cannot be translated anywhere else in the query, it throws a runtime exception.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="75e79-118">Cosmos DB 支持</span><span class="sxs-lookup"><span data-stu-id="75e79-118">Cosmos DB support</span></span> 

<span data-ttu-id="75e79-119">通过 EF Core 的 Cosmos DB 提供程序，熟悉 EF 编程模型的开发人员可以轻松地将 Azure Cosmos DB 定为应用程序数据库目标。</span><span class="sxs-lookup"><span data-stu-id="75e79-119">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span>
<span data-ttu-id="75e79-120">目标是利用 Cosmos DB 的一些优势，如全局分发、“始终开启”可用性、弹性可伸缩性和低延迟，甚至包括 .NET 开发人员可以更轻松地访问它。</span><span class="sxs-lookup"><span data-stu-id="75e79-120">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span>
<span data-ttu-id="75e79-121">此提供程序将针对 Cosmos DB 中的 SQL API 启用大部分 EF Core 功能，如自动更改跟踪、LINQ 和值转换。</span><span class="sxs-lookup"><span data-stu-id="75e79-121">The provider will enable most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="75e79-122">C#8.0 支持</span><span class="sxs-lookup"><span data-stu-id="75e79-122">C# 8.0 support</span></span>

<span data-ttu-id="75e79-123">EF Core 3.0 利用了 C#8.0 中的一些新功能：</span><span class="sxs-lookup"><span data-stu-id="75e79-123">EF Core 3.0 takes advantage of some of the new features in C# 8.0:</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="75e79-124">异步流</span><span class="sxs-lookup"><span data-stu-id="75e79-124">Asynchronous streams</span></span>

<span data-ttu-id="75e79-125">异步查询结果现在使用新的标准 `IAsyncEnumerable<T>` 接口公开，并且可以通过 `await foreach` 使用。</span><span class="sxs-lookup"><span data-stu-id="75e79-125">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders)
{
  Proccess(o);
} 
```

### <a name="nullable-reference-types"></a><span data-ttu-id="75e79-126">可为空引用类型</span><span class="sxs-lookup"><span data-stu-id="75e79-126">Nullable reference types</span></span> 

<span data-ttu-id="75e79-127">当在代码中启用这个新功能时，EF Core 可以推断引用类型的属性（字符串等基本类型或导航属性）的为 Null 性，从而决定数据库中列和关系的为 Null 性。</span><span class="sxs-lookup"><span data-stu-id="75e79-127">When this new feature is enabled in your code, EF Core can reason about the nullability of properties of refrence types (either of primitive types like string or navigation properties) to decide the nullability of columns and relationships in the database.</span></span>

## <a name="interception"></a><span data-ttu-id="75e79-128">Interception</span><span class="sxs-lookup"><span data-stu-id="75e79-128">Interception</span></span>

<span data-ttu-id="75e79-129">EF Core 3.0 中新的拦截 API 允许以编程方式观察和修改属于正常 EF Core 操作的低级数据库操作（例如打开连接、初始化事务和执行命令等）的结果。</span><span class="sxs-lookup"><span data-stu-id="75e79-129">The new interception API in EF Core 3.0 allows programatically observing and modifying the outcome of low-level database operations that occur as part of the normal operation of EF Core, such as opening connections, initating transactions, and executing commands.</span></span> 

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="75e79-130">数据库视图的反向工程</span><span class="sxs-lookup"><span data-stu-id="75e79-130">Reverse engineering of database views</span></span>

<span data-ttu-id="75e79-131">没有键的实体类型（以前称为[查询类型](xref:core/modeling/query-types)）表示可以从数据库读取但不能更新的数据。</span><span class="sxs-lookup"><span data-stu-id="75e79-131">Entity types without keys (previously known as [query types](xref:core/modeling/query-types)) represent data that can be read from the database, but cannot be updated.</span></span>
<span data-ttu-id="75e79-132">在大多数情况下，这一特性使它们非常适合映射数据库视图，因此我们在执行数据库视图的反向工程时自动创建了没有键的实体类型。</span><span class="sxs-lookup"><span data-stu-id="75e79-132">This characteristic makes them an excellent fit for mapping database views in most scenarios, so we automated the creation of entity types without keys when reverse engineering database views.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="75e79-133">与主体共享表的依赖实体现为可选项</span><span class="sxs-lookup"><span data-stu-id="75e79-133">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="75e79-134">自 EF Core 3.0 起，如果 `OrderDetails` 由 `Order` 拥有且显式映射到同一张表中，则它将可能添加 `Order` 而不添加 `OrderDetails`，并且除主键外的所有 `OrderDetails` 属性都将映射到不为 null 的列中。</span><span class="sxs-lookup"><span data-stu-id="75e79-134">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="75e79-135">查询时，如果其任意所需属性均没有值，或者它在主键之外没有任何必需属性且所有属性均为 `null`，则 EF Core 会将 `OrderDetails` 设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="75e79-135">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="75e79-136">.NET Core 上的 EF 6.3</span><span class="sxs-lookup"><span data-stu-id="75e79-136">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="75e79-137">我们知道许多现有应用程序使用以前版本的 EF，而仅为了利用 .NET Core 将其移植到 EF Core 有时需要大量工作。</span><span class="sxs-lookup"><span data-stu-id="75e79-137">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can sometimes require a significant effort.</span></span>
<span data-ttu-id="75e79-138">为此，我们允许了最新版本的 EF 6 在 .NET Core 3.0 上运行。</span><span class="sxs-lookup"><span data-stu-id="75e79-138">For that reason, we have enabled the newewst version of EF 6 to run on .NET Core 3.0.</span></span>
<span data-ttu-id="75e79-139">有一些限制，例如：</span><span class="sxs-lookup"><span data-stu-id="75e79-139">There are some limitations, for example:</span></span>
- <span data-ttu-id="75e79-140">新的提供程序需要在 .NET Core 上运行</span><span class="sxs-lookup"><span data-stu-id="75e79-140">New providers are required to work on .NET Core</span></span>
- <span data-ttu-id="75e79-141">将不启用 SQL Server 的空间支持</span><span class="sxs-lookup"><span data-stu-id="75e79-141">Spatial support with SQL Server won't be enabled</span></span>

## <a name="postponed-features"></a><span data-ttu-id="75e79-142">推迟的功能</span><span class="sxs-lookup"><span data-stu-id="75e79-142">Postponed features</span></span>

<span data-ttu-id="75e79-143">最初计划为 EF Core 3.0 提供的一些功能已推迟到将来的版本：</span><span class="sxs-lookup"><span data-stu-id="75e79-143">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span> 

- <span data-ttu-id="75e79-144">在迁移部分忽略模型的功能，跟踪编号为 [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725)。</span><span class="sxs-lookup"><span data-stu-id="75e79-144">Ability to ingore parts of a model in migrations, tracked by [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="75e79-145">属性包实体，由两个单独的问题跟踪：关于共享类型实体的 [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) 和关于索引属性映射支持的 [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610)。</span><span class="sxs-lookup"><span data-stu-id="75e79-145">Property bag entities, tracked by two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
