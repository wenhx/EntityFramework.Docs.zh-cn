---
title: 索引的 EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 810fccc0c6b035f515107601b245811f7b4118a6
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413935"
---
# <a name="indexes"></a><span data-ttu-id="f1f15-102">索引</span><span class="sxs-lookup"><span data-stu-id="f1f15-102">Indexes</span></span>

<span data-ttu-id="f1f15-103">索引是跨多个数据存储区的常见概念。</span><span class="sxs-lookup"><span data-stu-id="f1f15-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="f1f15-104">尽管它们在数据存储中的实现可能会有所不同，但也可用于基于列（或一组列）更高效地进行查找。</span><span class="sxs-lookup"><span data-stu-id="f1f15-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="f1f15-105">不能使用数据批注创建索引。</span><span class="sxs-lookup"><span data-stu-id="f1f15-105">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="f1f15-106">您可以使用 "熟知 API" 按如下方式为单个列指定索引：</span><span class="sxs-lookup"><span data-stu-id="f1f15-106">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="f1f15-107">您还可以为多个列指定索引：</span><span class="sxs-lookup"><span data-stu-id="f1f15-107">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="f1f15-108">按照约定，将在用作外键的每个属性（或一组属性）中创建索引。</span><span class="sxs-lookup"><span data-stu-id="f1f15-108">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="f1f15-109">EF Core 每个不同的属性集仅支持一个索引。</span><span class="sxs-lookup"><span data-stu-id="f1f15-109">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="f1f15-110">如果使用 "熟知 API" 来配置已定义索引的属性集的索引（按照约定或以前的配置），则会更改该索引的定义。</span><span class="sxs-lookup"><span data-stu-id="f1f15-110">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="f1f15-111">如果要进一步配置由约定创建的索引，则此操作非常有用。</span><span class="sxs-lookup"><span data-stu-id="f1f15-111">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="f1f15-112">索引唯一性</span><span class="sxs-lookup"><span data-stu-id="f1f15-112">Index uniqueness</span></span>

<span data-ttu-id="f1f15-113">默认情况下，索引不唯一：允许多行具有与索引的列集相同的值。</span><span class="sxs-lookup"><span data-stu-id="f1f15-113">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="f1f15-114">可以使索引唯一，如下所示：</span><span class="sxs-lookup"><span data-stu-id="f1f15-114">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="f1f15-115">尝试为索引的列集插入多个具有相同值的实体将导致引发异常。</span><span class="sxs-lookup"><span data-stu-id="f1f15-115">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="f1f15-116">索引名称</span><span class="sxs-lookup"><span data-stu-id="f1f15-116">Index name</span></span>

<span data-ttu-id="f1f15-117">按照约定，在关系数据库中创建的索引将命名为 `IX_<type name>_<property name>`。</span><span class="sxs-lookup"><span data-stu-id="f1f15-117">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="f1f15-118">对于复合索引，`<property name>` 变成以下划线分隔的属性名称列表。</span><span class="sxs-lookup"><span data-stu-id="f1f15-118">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="f1f15-119">您可以使用 "熟知 API" 设置在数据库中创建的索引的名称：</span><span class="sxs-lookup"><span data-stu-id="f1f15-119">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="f1f15-120">索引筛选器</span><span class="sxs-lookup"><span data-stu-id="f1f15-120">Index filter</span></span>

<span data-ttu-id="f1f15-121">某些关系数据库允许您指定筛选索引或部分索引。</span><span class="sxs-lookup"><span data-stu-id="f1f15-121">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="f1f15-122">这使您可以只为列的值的一个子集编制索引，从而减少索引的大小并改善性能和磁盘空间的使用情况。</span><span class="sxs-lookup"><span data-stu-id="f1f15-122">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="f1f15-123">有关 SQL Server 筛选索引的详细信息，[请参阅文档](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes)。</span><span class="sxs-lookup"><span data-stu-id="f1f15-123">For more information on SQL Server filtered indexes, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="f1f15-124">您可以使用熟知的 API 来指定索引的筛选器，作为 SQL 表达式提供：</span><span class="sxs-lookup"><span data-stu-id="f1f15-124">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="f1f15-125">当使用 SQL Server 提供程序 EF 时，将为唯一索引中包含的所有可以为 null 的列添加 `'IS NOT NULL'` 筛选器。</span><span class="sxs-lookup"><span data-stu-id="f1f15-125">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="f1f15-126">若要重写此约定，可以提供 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="f1f15-126">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="f1f15-127">包含列</span><span class="sxs-lookup"><span data-stu-id="f1f15-127">Included columns</span></span>

<span data-ttu-id="f1f15-128">某些关系数据库允许配置一组列，这些列包含在索引中，但不是其 "键" 的一部分。</span><span class="sxs-lookup"><span data-stu-id="f1f15-128">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="f1f15-129">当查询中的所有列都作为键列或非键列包含在索引中时，这可以显著提高查询性能，因为表本身无需访问。</span><span class="sxs-lookup"><span data-stu-id="f1f15-129">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="f1f15-130">有关 SQL Server 包含列的详细信息，[请参阅文档](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)。</span><span class="sxs-lookup"><span data-stu-id="f1f15-130">For more information on SQL Server included columns, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="f1f15-131">在下面的示例中，`Url` 列是索引键的一部分，因此对该列的任何查询筛选都可以使用索引。</span><span class="sxs-lookup"><span data-stu-id="f1f15-131">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="f1f15-132">但此外，仅访问 `Title` 和 `PublishedOn` 列的查询将不需要访问表，并且将更有效地运行：</span><span class="sxs-lookup"><span data-stu-id="f1f15-132">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
