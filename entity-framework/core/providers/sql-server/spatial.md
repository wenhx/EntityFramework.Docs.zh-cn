---
title: Microsoft SQL Server 数据库提供程序-空间数据-EF Core
description: 将空间数据与 Entity Framework Core Microsoft SQL Server 数据库提供程序一起使用
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sql-server/spatial
ms.openlocfilehash: 1356d2d86a497f01c4eacca777a8a260f33c0e9b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066514"
---
# <a name="spatial-data-in-the-sql-server-ef-core-provider"></a>SQL Server EF Core 提供程序中的空间数据

此页包含有关将空间数据与 Microsoft SQL Server 数据库提供程序一起使用的其他信息。 有关在 EF Core 中使用空间数据的常规信息，请参阅主 [空间数据](xref:core/modeling/spatial) 文档。

## <a name="geography-or-geometry"></a>地理或几何图形

默认情况下，空间属性映射到 `geography` SQL Server 中的列。 若要使用 `geometry` ，请在模型中 [配置列类型](xref:core/modeling/entity-properties#column-data-types) 。

## <a name="geography-polygon-rings"></a>地理多边形环

当使用 `geography` 列类型时，SQL Server 对外环 (或 shell) 和内部环 (或孔) 施加附加要求。 外部环必须逆时针旋转，并顺时针旋转内部环。 [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 在将值发送到数据库之前对其进行验证。

## <a name="fullglobe"></a>FullGlobe

在使用列类型时，SQL Server 具有非标准几何类型来表示全地球 `geography` 。 它还可以基于全地球 (来表示多边形，而无需外环) 。 NTS 不支持这两种方法。

> [!WARNING]
> NTS 不支持基于 FullGlobe 和多边形。

## <a name="curves"></a>曲线

如主 [空间数据](xref:core/modeling/spatial) 文档中所述，NTS 当前无法表示曲线。 这意味着，在 EF Core 中使用 STCurveToLine 方法之前，需要使用[STCurveToLine](/sql/t-sql/spatial-geography/stcurvetoline-geography-data-type)方法转换 CircularString、CompoundCurve 和 CurePolygon 值。

> [!WARNING]
> NTS 不支持 CircularString、CompoundCurve 和 CurePolygon。

## <a name="spatial-function-mappings"></a>空间函数映射

此表显示将哪些 NTS 成员转换为哪些 SQL 函数。 请注意，根据列的类型为 geography 还是 geometry，翻译会有所不同。

.NET                                      | SQL (geography)                                               | SQL (几何) 
----------------------------------------- | ------------------------------------------------------------ | --------------
geometry.图                             | @geometry.STArea()                                           | @geometry.STArea()
geometry.AsBinary ( # A1                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
geometry.AsText ( # A1                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
geometry.边缘                         |                                                              | @geometry.STBoundary()
geometry.缓冲区 (距离)                  | @geometry.STBuffer(@distance)                                | @geometry.STBuffer(@distance)
geometry.质心                         |                                                              | @geometry.STCentroid()
geometry.包含 (g)                       | @geometry.STContains(@g)                                     | @geometry.STContains(@g)
geometry.ConvexHull ( # A1                     | @geometry.STConvexHull()                                     | @geometry.STConvexHull()
geometry.交叉 (g)                        |                                                              | @geometry.STCrosses(@g)
geometry. (其他) 的差异                | @geometry.STDifference(@other)                               | @geometry.STDifference(@other)
geometry.维数                        | @geometry.STDimension()                                      | @geometry.STDimension()
geometry.不连续的 (g)                       | @geometry.STDisjoint(@g)                                     | @geometry.STDisjoint(@g)
geometry.G (距离)                       | @geometry.STDistance(@g)                                     | @geometry.STDistance(@g)
geometry.号                         |                                                              | @geometry.STEnvelope()
geometry.EqualsTopologically (g)            | @geometry.STEquals(@g)                                       | @geometry.STEquals(@g)
geometry.GeometryType                     | @geometry.STGeometryType()                                   | @geometry.STGeometryType()
geometry.GetGeometryN (n)                   | @geometry.STGeometryN (@n + 1)                                 | @geometry.STGeometryN (@n + 1) 
geometry.InteriorPoint                    |                                                              | @geometry.STPointOnSurface()
geometry.其他) 的交集 (              | @geometry.STIntersection(@other)                             | @geometry.STIntersection(@other)
geometry. (g 的交集)                     | @geometry.STIntersects(@g)                                   | @geometry.STIntersects(@g)
geometry.IsEmpty                          | @geometry.STIsEmpty()                                        | @geometry.STIsEmpty()
geometry.IsSimple                         |                                                              | @geometry.STIsSimple()
geometry.IsValid                          | @geometry.STIsValid()                                        | @geometry.STIsValid()
geometry.IsWithinDistance (几何、距离)  | @geometry.STDistance (@geom) <= @distance                     | @geometry.STDistance (@geom) <= @distance
geometry.长短                           | @geometry.STLength()                                         | @geometry.STLength()
geometry.NumGeometries                    | @geometry.STNumGeometries()                                  | @geometry.STNumGeometries()
geometry.X.numpoints                        | @geometry.STNumPoints()                                      | @geometry.STNumPoints()
geometry.OgcGeometryType                  | CASE @geometry.STGeometryType ( "THEN 1 .。。端面 | CASE @geometry.STGeometryType ( "THEN 1 .。。端面
geometry. (g) 重叠                      | @geometry.STOverlaps(@g)                                     | @geometry.STOverlaps(@g)
geometry.PointOnSurface                   |                                                              | @geometry.STPointOnSurface()
geometry.关联 (g，intersectionPattern)    |                                                              | @geometry.STRelate(@g, @intersectionPattern)
geometry.SRID                             | @geometry.STSrid                                             | @geometry.STSrid
geometry.SymmetricDifference (其他)        | @geometry.STSymDifference(@other)                            | @geometry.STSymDifference(@other)
geometry.ToBinary ( # A1                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
geometry.ToText ( # A1                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
geometry. (g)                        |                                                              | @geometry.STTouches(@g)
geometry.联合 (其他)                      | @geometry.STUnion(@other)                                    | @geometry.STUnion(@other)
geometry. (g)                         | @geometry.STWithin(@g)                                       | @geometry.STWithin(@g)
geometryCollection [i]                     | @geometryCollection.STGeometryN (@i + 1)                       | @geometryCollection.STGeometryN (@i + 1) 
geometryCollection                  | @geometryCollection.STNumGeometries()                        | @geometryCollection.STNumGeometries()
lineString                          | @lineString.STNumPoints()                                    | @lineString.STNumPoints()
lineString 终结点                       | @lineString.STEndPoint()                                     | @lineString.STEndPoint()
lineString. GetPointN (n)                    | @lineString.STPointN (@n + 1)                                  | @lineString.STPointN (@n + 1) 
lineString. IsClosed                       | @lineString.STIsClosed()                                     | @lineString.STIsClosed()
lineString. IsRing                         |                                                              | @lineString.IsRing()
lineString. StartPoint                     | @lineString.STStartPoint()                                   | @lineString.STStartPoint()
multiLineString. IsClosed                  | @multiLineString.STIsClosed()                                | @multiLineString.STIsClosed()
情况.年                                   | @point.M                                                     | @point.M
情况.X-blade                                   | @point.Long                                                  | @point.STX
情况.误差                                   | @point.Lat                                                   | @point.STY
情况.Z                                   | @point.Z                                                     | @point.Z
各.ExteriorRing                      | @polygon.RingN (1)                                             | @polygon.STExteriorRing()
各.GetInteriorRingN (n)                | @polygon.RingN (@n + 2)                                        | @polygon.STInteriorRingN (@n + 1) 
各.NumInteriorRings                  | @polygon.NumRings ( # A1-1                                      | @polygon.STNumInteriorRing()

## <a name="additional-resources"></a>其他资源

* [SQL Server 中的空间数据](/sql/relational-databases/spatial/spatial-data-sql-server)
* [NetTopologySuite 文档](https://nettopologysuite.github.io/NetTopologySuite/)
