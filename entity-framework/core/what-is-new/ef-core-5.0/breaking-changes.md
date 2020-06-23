---
title: EF Core 5.0 中的中断性变更 - EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666167"
---
# <a name="breaking-changes-in-ef-core-50"></a>EF Core 5.0 中的中断性变更

API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。

## <a name="summary"></a>摘要

| **中断性变更**                                                                                                               | **影响** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | 低        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法

[跟踪问题 #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**旧行为**

HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。 但是，之前它只影响数据库创建。 不需要指定它来查询具有其他维度的值。 之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。

**新行为**

要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。 这与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。

**为什么**

在列类型中指定维度后，不需要使用 HasGeometricDimension，该方法也很多余，因此我们已将它彻底删除。

**缓解措施**

使用 `HasColumnType` 指定维度：

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
