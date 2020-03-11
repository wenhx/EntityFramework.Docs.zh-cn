---
title: Azure Cosmos DB 提供程序-使用非结构化数据-EF Core
description: 如何使用 Entity Framework Core 处理 Azure Cosmos DB 非结构化数据
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 69f979d46174ff56310b334f28438ac271f45155
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414547"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>使用 EF Core Azure Cosmos DB 提供程序中的非结构化数据

EF Core 旨在使使用在模型中定义的架构的数据变得简单。 但 Azure Cosmos DB 的优点之一是存储数据形状的灵活性。

## <a name="accessing-the-raw-json"></a>访问原始 JSON

可以通过名为 `"__jObject"` 的[卷影状态](../../modeling/shadow-properties.md)中 `JObject` 包含表示从存储区接收的数据的特定属性来访问不 EF Core 跟踪的属性，以及将存储的数据：

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
> `"__jObject"` 属性是 EF Core 基础结构的一部分，只应用作最后的手段，因为在将来的版本中可能会有不同的行为。

> [!NOTE]
> 对实体所做的更改将覆盖 `SaveChanges`期间 `"__jObject"` 中存储的值。

## <a name="using-cosmosclient"></a>使用 CosmosClient

若要完全分离 EF Core 获取 `DbContext`中[AZURE COSMOS DB SDK 的一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)对象：

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>缺少属性值

在上面的示例中，我们从顺序中删除了 `"TrackingNumber"` 属性。 由于在 Cosmos DB 中索引的工作原理，引用缺少的属性的其他位置的查询可能会返回意外的结果。 例如：

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

排序的查询实际上不返回任何结果。 这意味着，当直接使用存储时，应注意始终填充由 EF Core 映射的属性。

> [!NOTE]
> 在未来版本的 Cosmos 中，此行为可能会发生变化。 例如，当前如果索引策略定义了复合索引 {Id/？ ASC，TrackingNumber/？ ASC）}，则为 "ORDER BY c.Id ASC，ASC" 的查询__将__返回缺少 `"TrackingNumber"` 属性的项。
