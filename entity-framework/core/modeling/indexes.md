---
title: 索引-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: d1b5cd6853cd24f7e1aa3aace2f0a3455c657cc1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655700"
---
# <a name="indexes"></a>索引

索引是跨多个数据存储区的常见概念。 尽管它们在数据存储中的实现可能会有所不同，但也可用于基于列（或一组列）更高效地进行查找。

## <a name="conventions"></a>约定

按照约定，将在用作外键的每个属性（或一组属性）中创建索引。

## <a name="data-annotations"></a>数据注释

不能使用数据批注创建索引。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来指定单个属性的索引。 默认情况下，索引不是唯一的。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=7,8)]

您还可以指定索引应是唯一的，这意味着对于给定的属性，不能有两个实体具有相同的值。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=ModelBuilder&highlight=3)]

您还可以为多个列指定索引。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=7,8)]

> [!TIP]  
> 每个不同的属性集只有一个索引。 如果使用 "熟知 API" 来配置已定义索引的属性集的索引（按照约定或以前的配置），则会更改该索引的定义。 如果要进一步配置由约定创建的索引，则此操作非常有用。
