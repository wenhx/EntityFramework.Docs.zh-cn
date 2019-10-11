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
# <a name="querying-data"></a>查询数据

Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。 通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）编写强类型查询。 它使用你派生得到的上下文和实体类来引用数据库对象。 EF Core 将 LINQ 查询的表示形式传递给数据库提供程序。 反过来，数据库提供程序将其转换为数据库特定的查询语言（例如，用于关系数据库的 SQL）。

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

以下片段显示的几个示例展示了如何使用 Entity Framework Core 完成常见任务。

## <a name="loading-all-data"></a>加载所有数据

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>加载单个实体

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a>筛选

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a>延伸阅读

- 了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息
- 要更详细地了解如何在 EF Core 中处理查询，请参阅[查询的工作原理](xref:core/querying/how-query-works)。
