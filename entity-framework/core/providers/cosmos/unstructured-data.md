---
title: Azure Cosmos DB 提供程序-使用非结构化数据-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: b47d41b6-984f-419a-ab10-2ed3b95e3919
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 86bb0f7915c8a2561e7d5cd5dffc27474218a112
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150769"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>使用 EF Core Azure Cosmos DB 提供程序中的非结构化数据

EF Core 旨在使使用在模型中定义的架构的数据变得简单。 但 Azure Cosmos DB 的优点之一是存储数据形状的灵活性。

## <a name="accessing-the-raw-json"></a>访问原始 JSON

可以通过名为`"__jObject"`的[卷影状态](../../modeling/shadow-properties.md)中的一个特殊属性来访问未跟踪的属性，该属性包含`JObject`表示从存储中接收的数据的 EF Core 和将存储的数据：

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=21-23&name=Unmapped)]

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
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
> 该`"__jObject"`属性是 EF Core 基础结构的一部分，只应用作最后的手段，因为在将来的版本中可能会有不同的行为。

> [!NOTE]
> 对实体所做的更改将覆盖在`"__jObject"`过程`SaveChanges`中存储的值。

## <a name="using-cosmosclient"></a>使用 CosmosClient

若要完全分离 EF Core 从获取`CosmosClient` `DbContext` [Azure Cosmos DB SDK 的一部分](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-get-started)的对象：

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>缺少属性值

在上面的示例中，我们`"TrackingNumber"`从顺序中删除了属性。 由于在 Cosmos DB 中索引的工作原理，引用缺少的属性的其他位置的查询可能会返回意外的结果。 例如:

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

排序的查询实际上不返回任何结果。 这意味着，当直接使用存储时，应注意始终填充由 EF Core 映射的属性。

> [!NOTE]
> 在未来版本的 Cosmos 中，此行为可能会发生变化。 例如，当前如果索引策略定义了复合索引 {Id/？ ASC，TrackingNumber/？ ASC）}，then "ORDER BY c.Id asc，.c asc" 的查询__将__返回缺少属性的`"TrackingNumber"`项。