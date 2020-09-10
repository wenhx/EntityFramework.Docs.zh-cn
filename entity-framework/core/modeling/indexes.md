---
title: 索引-EF Core
description: 在 Entity Framework Core 模型中配置索引
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 1ce40a9219dde56478b1ff8891841b53d9ba1934
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619350"
---
# <a name="indexes"></a>索引

索引是跨多个数据存储区的常见概念。 尽管它们在数据存储中的实现可能会有所不同，但也可用于基于列 (或一组列来进行查找，) 效率更高。

不能使用数据批注创建索引。 您可以使用 "熟知 API" 按如下方式为单个列指定索引：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

您还可以为多个列指定索引：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> 按照约定，会在每个属性中创建一个索引 (或一组用作外键的属性) 。
>
> EF Core 每个不同的属性集仅支持一个索引。 如果使用 "熟知 API" 来配置已定义索引的属性集的索引（按照约定或以前的配置），则会更改该索引的定义。 如果要进一步配置由约定创建的索引，则此操作非常有用。

## <a name="index-uniqueness"></a>索引唯一性

默认情况下，索引不唯一：对于索引的列集，允许多行具有相同的值 (s) 。 可以使索引唯一，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

尝试为索引的列集插入多个具有相同值的实体将导致引发异常。

## <a name="index-name"></a>索引名称

按照约定，在关系数据库中创建的索引命名为 `IX_<type name>_<property name>` 。 对于复合索引， `<property name>` 将成为以下划线分隔的属性名称列表。

您可以使用 "熟知 API" 设置在数据库中创建的索引的名称：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a>索引筛选器

某些关系数据库允许您指定筛选索引或部分索引。 这使您可以只为列的值的一个子集编制索引，从而减少索引的大小并改善性能和磁盘空间的使用情况。 有关 SQL Server 筛选索引的详细信息， [请参阅文档](/sql/relational-databases/indexes/create-filtered-indexes)。

您可以使用熟知的 API 来指定索引的筛选器，作为 SQL 表达式提供：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

当使用 SQL Server 提供程序 EF 时 `'IS NOT NULL'` ，将为唯一索引中包含的所有可以为 null 的列添加筛选器。 若要重写此约定，可以提供一个 `null` 值。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>包含列

某些关系数据库允许配置一组列，这些列包含在索引中，但不是其 "键" 的一部分。 当查询中的所有列都作为键列或非键列包含在索引中时，这可以显著提高查询性能，因为表本身无需访问。 有关 SQL Server 包含列的详细信息， [请参阅文档](/sql/relational-databases/indexes/create-indexes-with-included-columns)。

在下面的示例中， `Url` 列是索引键的一部分，因此对该列的任何查询筛选都可以使用索引。 但此外，仅访问 `Title` 和列的查询 `PublishedOn` 将不需要访问表并更有效地运行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
