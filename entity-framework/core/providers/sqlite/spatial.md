---
title: SQLite 数据库提供程序-空间数据-EF Core
description: 将空间数据与 Entity Framework Core SQLite 数据库提供程序一起使用
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066509"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>SQLite EF Core 提供程序中的空间数据

此页包含有关将空间数据与 SQLite 数据库提供程序一起使用的其他信息。 有关在 EF Core 中使用空间数据的常规信息，请参阅主 [空间数据](xref:core/modeling/spatial) 文档。

## <a name="installing-spatialite"></a>安装 SpatiaLite

在 Windows 上，本机 mod_spatialite 库以 NuGet 包的依赖项的形式分发。 其他平台需要单独安装它。 通常使用软件程序包管理器完成此操作。 例如，可以在 Debian 和 Ubuntu 上使用 APT;MacOS 上的和 Homebrew。

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

遗憾的是，较新版本的 PROJ (SpatiaLite) 的依赖项与 EF 的默认 [SQLitePCLRaw 绑定](/dotnet/standard/data/sqlite/custom-versions#bundles)不兼容。 您可以通过使用系统 SQLite 库来解决此情况。

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

在 **macOS**上，你还需要在运行应用之前设置环境变量，使其使用 Homebrew 版本的 SQLite。 在 Visual Studio for Mac 中，你可以在项目 > 项目选项下设置此项， **> 运行 > 配置 > 默认值**

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>配置 SRID

在 SpatiaLite 中，列需要为每个列指定一个 SRID。 默认的 SRID 为 `0` 。 使用 ForSqliteHasSrid 方法指定其他 SRID。

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> 4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。

## <a name="dimension"></a>维度

列 (或坐标) 的默认维度为 X 和 Y。若要启用其他坐标，如 Z 或 M，请配置列类型。

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>空间函数映射

此表显示哪些 [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 成员转换为哪些 SQL 函数。

.NET                                        | SQL
------------------------------------------- | ---
geometry.图                               | 区域 (@geometry) 
geometry.AsBinary ( # A1                         | AsBinary (@geometry) 
geometry.AsText ( # A1                           | AsText (@geometry) 
geometry.边缘                           | 边界 (@geometry) 
geometry.缓冲区 (距离)                    | 缓冲区 (@geometry ， @distance) 
geometry.缓冲区 (距离，quadrantSegments)  | Buffer (@geometry 、 @distance @quadrantSegments) 
geometry.质心                           | 质心 (@geometry) 
geometry.包含 (g)                         | 包含 (@geometry 、 @g) 
geometry.ConvexHull ( # A1                       | ConvexHull (@geometry) 
geometry.CoveredBy (g)                        | CoveredBy (@geometry ， @g) 
geometry.涵盖 (g)                           | 介绍 (@geometry 、 @g) 
geometry.交叉 (g)                          | 跨 (@geometry ， @g) 
geometry. (其他) 的差异                  | 差异 (@geometry ， @other) 
geometry.维数                          | 维度 (@geometry) 
geometry.不连续的 (g)                         | 不连续 @geometry 的 (， @g) 
geometry.G (距离)                         | 距离 (@geometry ， @g) 
geometry.号                           | 信封 (@geometry) 
geometry.EqualsTopologically (g)              | 等于 (@geometry ， @g) 
geometry.GeometryType                       | GeometryType (@geometry) 
geometry.GetGeometryN (n)                     | GeometryN (@geometry ， @n + 1) 
geometry.InteriorPoint                      | PointOnSurface (@geometry) 
geometry.其他) 的交集 (                | 交集 (@geometry ， @other) 
geometry. (g 的交集)                       |  (@geometry 的交集， @g) 
geometry.IsEmpty                            | IsEmpty (@geometry) 
geometry.IsSimple                           | IsSimple (@geometry) 
geometry.IsValid                            | IsValid (@geometry) 
geometry.IsWithinDistance (几何、距离)    | 距离 (@geometry ， @geom) <= @distance
geometry.长短                             | GLength (@geometry) 
geometry.NumGeometries                      | NumGeometries (@geometry) 
geometry.X.numpoints                          | X.numpoints (@geometry) 
geometry.OgcGeometryType                    | CASE GeometryType (@geometry) "POINT" THEN .。。端面
geometry. (g) 重叠                        | 重叠 (@geometry ， @g) 
geometry.PointOnSurface                     | PointOnSurface (@geometry) 
geometry.关联 (g，intersectionPattern)      | 关联 (@geometry 、 @g @intersectionPattern) 
geometry.反向 ( # A1                          | ST_Reverse (@geometry) 
geometry.SRID                               | SRID (@geometry) 
geometry.SymmetricDifference (其他)          | SymDifference (@geometry ， @other) 
geometry.ToBinary ( # A1                         | AsBinary (@geometry) 
geometry.ToText ( # A1                           | AsText (@geometry) 
geometry. (g)                          |  (@geometry ， @g) 
geometry.联合 ( # A1                            | UnaryUnion (@geometry) 
geometry.联合 (其他)                        | GUnion (@geometry ， @other) 
geometry. (g)                           | 在 (中 @geometry ， @g) 
geometryCollection [i]                       | GeometryN (@geometryCollection ， @i + 1) 
geometryCollection                    | NumGeometries (@geometryCollection) 
lineString                            | X.numpoints (@lineString) 
lineString 终结点                         | 终结点 (@lineString) 
lineString. GetPointN (n)                      | PointN (@lineString ， @n + 1) 
lineString. IsClosed                         | IsClosed (@lineString) 
lineString. IsRing                           | IsRing (@lineString) 
lineString. StartPoint                       | StartPoint (@lineString) 
multiLineString. IsClosed                    | IsClosed (@multiLineString) 
情况.年                                     | M (@point) 
情况.X-blade                                     | X (@point) 
情况.误差                                     | Y (@point) 
情况.Z                                     | Z (@point) 
各.ExteriorRing                        | ExteriorRing (@polygon) 
各.GetInteriorRingN (n)                  | InteriorRingN (@polygon ， @n + 1) 
各.NumInteriorRings                    | NumInteriorRing (@polygon) 

## <a name="additional-resources"></a>其他资源

* [SpatiaLite 主页](https://www.gaia-gis.it/fossil/libspatialite)
* [NetTopologySuite 文档](https://nettopologysuite.github.io/NetTopologySuite/)
