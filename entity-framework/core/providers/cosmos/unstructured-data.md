---
title: Azure Cosmos DB 提供程序-使用非结构化数据-EF Core
description: 如何使用 Entity Framework Core 处理 Azure Cosmos DB 非结构化数据
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 9f96af00e8fcb012c33fc7528787560ea3a5e481
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619040"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="4fe74-103">使用 EF Core Azure Cosmos DB 提供程序中的非结构化数据</span><span class="sxs-lookup"><span data-stu-id="4fe74-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="4fe74-104">EF Core 旨在使使用在模型中定义的架构的数据变得简单。</span><span class="sxs-lookup"><span data-stu-id="4fe74-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="4fe74-105">但 Azure Cosmos DB 的优点之一是存储数据形状的灵活性。</span><span class="sxs-lookup"><span data-stu-id="4fe74-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="4fe74-106">访问原始 JSON</span><span class="sxs-lookup"><span data-stu-id="4fe74-106">Accessing the raw JSON</span></span>

<span data-ttu-id="4fe74-107">可以通过名为的 [卷影状态](xref:core/modeling/shadow-properties) 中的一个特殊属性来访问未跟踪的属性，该属性 `"__jObject"` 包含 `JObject` 表示从存储中接收的数据的 EF Core 和将存储的数据：</span><span class="sxs-lookup"><span data-stu-id="4fe74-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](xref:core/modeling/shadow-properties) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> <span data-ttu-id="4fe74-108">该 `"__jObject"` 属性是 EF Core 基础结构的一部分，只应用作最后的手段，因为在将来的版本中可能会有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="4fe74-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="4fe74-109">对实体所做的更改将覆盖在过程中存储的值 `"__jObject"` `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="4fe74-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="4fe74-110">使用 CosmosClient</span><span class="sxs-lookup"><span data-stu-id="4fe74-110">Using CosmosClient</span></span>

<span data-ttu-id="4fe74-111">若要完全分离 EF Core 获取作为[AZURE COSMOS DB SDK 的一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)对象 `DbContext` ：</span><span class="sxs-lookup"><span data-stu-id="4fe74-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="4fe74-112">缺少属性值</span><span class="sxs-lookup"><span data-stu-id="4fe74-112">Missing property values</span></span>

<span data-ttu-id="4fe74-113">在上面的示例中，我们 `"TrackingNumber"` 从顺序中删除了属性。</span><span class="sxs-lookup"><span data-stu-id="4fe74-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="4fe74-114">由于在 Cosmos DB 中索引的工作原理，引用缺少的属性的其他位置的查询可能会返回意外的结果。</span><span class="sxs-lookup"><span data-stu-id="4fe74-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="4fe74-115">例如：</span><span class="sxs-lookup"><span data-stu-id="4fe74-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="4fe74-116">排序的查询实际上不返回任何结果。</span><span class="sxs-lookup"><span data-stu-id="4fe74-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="4fe74-117">这意味着，当直接使用存储时，应注意始终填充由 EF Core 映射的属性。</span><span class="sxs-lookup"><span data-stu-id="4fe74-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="4fe74-118">在未来版本的 Cosmos 中，此行为可能会发生变化。</span><span class="sxs-lookup"><span data-stu-id="4fe74-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="4fe74-119">例如，当前如果索引策略定义了复合索引 {Id/？</span><span class="sxs-lookup"><span data-stu-id="4fe74-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="4fe74-120">ASC，TrackingNumber/？</span><span class="sxs-lookup"><span data-stu-id="4fe74-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="4fe74-121">ASC) }，然后是一个包含 "ORDER BY c.Id ASC，ASC" 的查询 __将__ 返回缺少该属性的项 `"TrackingNumber"` 。</span><span class="sxs-lookup"><span data-stu-id="4fe74-121">ASC)}, then a query that has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
