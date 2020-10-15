---
title: 函数映射-Azure Cosmos DB 提供程序-EF Core
description: Azure Cosmos DB EF Core 提供程序的函数映射
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066505"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Azure Cosmos DB EF Core 提供程序的函数映射

此页显示使用 Azure Cosmos DB 提供程序时，哪些 .NET 成员转换为哪些 SQL 函数。

.NET                          | SQL                              | 在
----------------------------- | -------------------------------- | --------
集合.包含 (项)      | @item 中 @collection
stringValue 包含 (值)    | 包含 (@stringValue 、 @value)    | EF Core 5.0
stringValue. EndsWith (值)    | ENDSWITH (@stringValue ， @value)    | EF Core 5.0
stringValue. FirstOrDefault ( # A1  | 左 (@stringValue ，1)             | EF Core 5.0
stringValue. LastOrDefault ( # A1   | RIGHT (@stringValue ，1)            | EF Core 5.0
stringValue. StartsWith (值)  | STARTSWITH (@stringValue ， @value)  | EF Core 5.0
