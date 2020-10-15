---
title: Azure Cosmos DB 提供程序-使用非结构化数据-EF Core
description: 如何使用 Entity Framework Core 处理 Azure Cosmos DB 非结构化数据
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: ac497c9f5540557b931db935f4f3ca480edf010d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064019"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>使用 EF Core Azure Cosmos DB 提供程序中的非结构化数据

EF Core 旨在使使用在模型中定义的架构的数据变得简单。 但 Azure Cosmos DB 的优点之一是存储数据形状的灵活性。

## <a name="accessing-the-raw-json"></a>访问原始 JSON

可以通过名为的 [卷影状态](xref:core/modeling/shadow-properties) 中的一个特殊属性来访问未跟踪的属性，该属性 `"__jObject"` 包含 `JObject` 表示从存储中接收的数据的 EF Core 和将存储的数据：

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

```json
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
> 该 `"__jObject"` 属性是 EF Core 基础结构的一部分，只应用作最后的手段，因为在将来的版本中可能会有不同的行为。

> [!NOTE]
> 对实体所做的更改将覆盖在过程中存储的值 `"__jObject"` `SaveChanges` 。

## <a name="using-cosmosclient"></a>使用 CosmosClient

若要完全分离 EF Core 获取作为[AZURE COSMOS DB SDK 的一部分](/azure/cosmos-db/sql-api-get-started)的[CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient)对象 `DbContext` ：

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>缺少属性值

在上面的示例中，我们 `"TrackingNumber"` 从顺序中删除了属性。 由于在 Cosmos DB 中索引的工作原理，引用缺少的属性的其他位置的查询可能会返回意外的结果。 例如：

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

排序的查询实际上不返回任何结果。 这意味着，当直接使用存储时，应注意始终填充由 EF Core 映射的属性。

> [!NOTE]
> 在未来版本的 Cosmos 中，此行为可能会发生变化。 例如，当前如果索引策略定义了复合索引 {Id/？ ASC，TrackingNumber/？ ASC) }，然后是一个包含 "ORDER BY c.Id ASC，ASC" 的查询 __将__ 返回缺少该属性的项 `"TrackingNumber"` 。
