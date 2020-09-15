---
title: 查询数据 - EF Core
description: 有关在 Entity Framework Core 中查询的信息的概述
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 0bc1ef7d5595d5ed534a118fdc33e1a78648582e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071116"
---
# <a name="querying-data"></a>查询数据

Entity Framework Core 使用语言集成查询 (LINQ) 来查询数据库中的数据。 通过 LINQ 可使用 C#（或你选择的其他 .NET 语言）编写强类型查询。 它使用你派生得到的上下文和实体类来引用数据库对象。 EF Core 将 LINQ 查询的表示形式传递给数据库提供程序。 反过来，数据库提供程序将其转换为数据库特定的查询语言（例如，用于关系数据库的 SQL）。

> [!TIP]
> 可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)。

以下片段显示的几个示例展示了如何使用 Entity Framework Core 完成常见任务。

## <a name="loading-all-data"></a>加载所有数据

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>加载单个实体

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a>筛选

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a>延伸阅读

- 了解有关 [LINQ 查询表达式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)的详细信息
- 若要更详细地了解 EF Core 中的查询处理方式，请参阅[查询的工作原理](xref:core/querying/how-query-works)。
