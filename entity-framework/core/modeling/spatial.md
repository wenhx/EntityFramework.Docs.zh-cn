---
title: 空间数据-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 5b45f83ca7f02665f52ccfe16b5af506a6046a62
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414703"
---
# <a name="spatial-data"></a>空间数据

> [!NOTE]
> 此功能是在 EF Core 2.2 中添加的。

空间数据表示对象的物理位置和形状。 许多数据库提供对此类数据的支持，以便能够与其他数据一起进行索引和查询。 常见方案包括从位置在给定距离内查询对象，或选择其边框包含给定位置的对象。 EF Core 支持使用[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空间库映射到空间数据类型。

## <a name="installing"></a>安装

若要在 EF Core 中使用空间数据，需要安装相应的支持 NuGet 包。 需要安装哪个包取决于所使用的提供程序。

EF Core 提供程序                        | 空间 NuGet 包
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft.entityframeworkcore. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft.entityframeworkcore. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. Microsoft.entityframeworkcore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>反向工程

空间 NuGet 包还启用具有空间属性的[反向工程](../managing-schemas/scaffolding.md)模型，但需要在运行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`***之前***安装包。 否则，你将收到有关找不到列的类型映射的警告，将跳过这些列。

## <a name="nettopologysuite-nts"></a>NetTopologySuite （NTS）

NetTopologySuite 是用于 .NET 的空间库。 EF Core 使用模型中的 NTS 类型启用映射到数据库中的空间数据类型。

若要通过 NTS 启用到空间类型的映射，请在提供程序的 DbContext 选项生成器上调用 UseNetTopologySuite 方法。 例如，对于 SQL Server，你应将其称为。

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

有几种空间数据类型。 使用哪种类型取决于您想要允许的形状的类型。 下面是可用于模型中的属性的 NTS 类型的层次结构。 它们位于 `NetTopologySuite.Geometries` 命名空间中。

* Geometry
  * 点
  * LineString
  * Polygon
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> NTS 不支持 CircularString、CompoundCurve 和 CurePolygon。

使用基本几何图形类型允许属性指定任意类型的形状。

以下实体类可用于映射到[广角导入示例数据库](https://go.microsoft.com/fwlink/?LinkID=800630)中的表。

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>创建值

您可以使用构造函数来创建 geometry 对象;但是，NTS 建议改为使用几何工厂。 这允许您指定默认 SRID （坐标使用的空间引用系统），并使您能够控制更高级的任务（例如，在计算过程中使用）和坐标序列（确定哪些坐标维度和度量值--可用）。

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> 4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。

### <a name="longitude-and-latitude"></a>经度和纬度

NTS 中的坐标采用 X 和 Y 值。 若要表示经度和纬度，请将 X 用于经度，将 Y 用于纬度。 请注意，这是从通常会看到这些值的 `latitude, longitude` 格式**反向**进行的。

### <a name="srid-ignored-during-client-operations"></a>在客户端操作过程中忽略 SRID

NTS 在操作过程中忽略 SRID 值。 它假定为平面坐标系统。 这意味着，如果在经度和纬度方面指定了坐标，则某些客户端计算的值（例如，距离、长度和区域）将为度数，而不是计量。 若要获得更有意义的值，首先需要使用库（如[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) ）在计算这些值之前投影到另一个坐标系统的坐标。

如果通过 EF Core 通过 SQL 对操作进行服务器计算，则该结果的单元将由数据库确定。

下面是一个示例，说明如何使用 ProjNet4GeoAPI 来计算两个城市之间的距离。

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>查询数据

在 LINQ 中，可用作数据库函数的 NTS 方法和属性将转换为 SQL。 例如，在以下查询中转换距离和包含方法。 本文末尾的表格显示了不同 EF Core 提供商支持哪些成员。

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

如果你正在使用 SQL Server，你还应该注意一些其他问题。

### <a name="geography-or-geometry"></a>地理或几何图形

默认情况下，空间属性映射到 SQL Server 中的 `geography` 列。 若要使用 `geometry`，请在模型中[配置列类型](xref:core/modeling/entity-properties#column-data-types)。

### <a name="geography-polygon-rings"></a>地理多边形环

使用 `geography` 列类型时，SQL Server 对外环（或外壳）和内部环（或孔）施加附加要求。 外部环必须逆时针旋转，并顺时针旋转内部环。 NTS 在将值发送到数据库之前对其进行验证。

### <a name="fullglobe"></a>FullGlobe

使用 `geography` 列类型时，SQL Server 具有非标准几何类型来表示全地球。 它还提供了一种方法，用于根据全地球（无外部环）来表示多边形。 NTS 不支持这两种方法。

> [!WARNING]
> NTS 不支持基于 FullGlobe 和多边形。

## <a name="sqlite"></a>SQLite

下面是使用 SQLite 的一些其他信息。

### <a name="installing-spatialite"></a>安装 SpatiaLite

在 Windows 上，本机 mod_spatialite 库以 NuGet 包的依赖项的形式分发。 其他平台需要单独安装它。 通常使用软件程序包管理器完成此操作。 例如，可以在 Ubuntu 上使用 APT 和 MacOS 上的 Homebrew。

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

遗憾的是，较新版本的 PROJ （SpatiaLite 的依赖项）与 EF 的默认[SQLitePCLRaw 绑定](/dotnet/standard/data/sqlite/custom-versions#bundles)不兼容。 若要解决此情况，可以创建使用系统 SQLite 库的自定义[SQLitePCLRaw 提供程序](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers)，也可以安装 SPATIALITE 禁用 PROJ 支持的自定义生成。

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a>配置 SRID

在 SpatiaLite 中，列需要为每个列指定一个 SRID。 默认的 SRID 是 `0`。 使用 ForSqliteHasSrid 方法指定其他 SRID。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>Dimension

类似于 SRID，列的维度（或坐标）也被指定为列的一部分。 默认坐标为 X 和 Y。使用 ForSqliteHasDimension 方法启用其他坐标（Z 和 M）。

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>转换的操作

此表显示每个 EF Core 提供程序将哪些 NTS 成员转换为 SQL。

NetTopologySuite | SQL Server （geometry） | SQL Server （geography） | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry | ✔ | ✔ | ✔ | ✔
AsBinary （） | ✔ | ✔ | ✔ | ✔
AsText （） | ✔ | ✔ | ✔ | ✔
Geometry | ✔ | | ✔ | ✔
Geometry （双精度型） | ✔ | ✔ | ✔ | ✔
Geometry （double，int） | | | ✔ | ✔
质心 | ✔ | | ✔ | ✔
Geometry。 Contains （Geometry） | ✔ | ✔ | ✔ | ✔
ConvexHull （） | ✔ | ✔ | ✔ | ✔
CoveredBy （Geometry） | | | ✔ | ✔
Geometry （Geometry） | | | ✔ | ✔
几何。交叉（几何） | ✔ | | ✔ | ✔
几何差（几何） | ✔ | ✔ | ✔ | ✔
Geometry。维度 | ✔ | ✔ | ✔ | ✔
不连续（Geometry） | ✔ | ✔ | ✔ | ✔
Geometry （Geometry） | ✔ | ✔ | ✔ | ✔
Geometry 信封 | ✔ | | ✔ | ✔
EqualsExact （Geometry） | | | | ✔
EqualsTopologically （Geometry） | ✔ | ✔ | ✔ | ✔
GeometryType | ✔ | ✔ | ✔ | ✔
GetGeometryN （int） | ✔ | | ✔ | ✔
InteriorPoint | ✔ | | ✔ | ✔
几何交集（Geometry） | ✔ | ✔ | ✔ | ✔
几何和交集（Geometry） | ✔ | ✔ | ✔ | ✔
IsEmpty | ✔ | ✔ | ✔ | ✔
IsSimple | ✔ | | ✔ | ✔
Geometry | ✔ | ✔ | ✔ | ✔
IsWithinDistance （Geometry，double） | ✔ | | ✔ | ✔
Geometry。长度 | ✔ | ✔ | ✔ | ✔
NumGeometries | ✔ | ✔ | ✔ | ✔
X.numpoints | ✔ | ✔ | ✔ | ✔
OgcGeometryType | ✔ | ✔ | ✔ | ✔
Geometry 重叠（Geometry） | ✔ | ✔ | ✔ | ✔
PointOnSurface | ✔ | | ✔ | ✔
Geometry （Geometry，string） | ✔ | | ✔ | ✔
Geometry 反向（） | | | ✔ | ✔
SRID | ✔ | ✔ | ✔ | ✔
SymmetricDifference （Geometry） | ✔ | ✔ | ✔ | ✔
ToBinary （） | ✔ | ✔ | ✔ | ✔
ToText （） | ✔ | ✔ | ✔ | ✔
几何图形（几何） | ✔ | | ✔ | ✔
Geometry （） | | | ✔ | ✔
Geometry （Geometry） | ✔ | ✔ | ✔ | ✔
几何图形。内（Geometry） | ✔ | ✔ | ✔ | ✔
GeometryCollection | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString | ✔ | ✔ | ✔ | ✔
LineString 终结点 | ✔ | ✔ | ✔ | ✔
LineString. GetPointN （int） | ✔ | ✔ | ✔ | ✔
LineString. IsClosed | ✔ | ✔ | ✔ | ✔
LineString. IsRing | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. IsClosed | ✔ | ✔ | ✔ | ✔
点 M | ✔ | ✔ | ✔ | ✔
点 X | ✔ | ✔ | ✔ | ✔
Point。 Y | ✔ | ✔ | ✔ | ✔
点 Z | ✔ | ✔ | ✔ | ✔
多边形。 ExteriorRing | ✔ | ✔ | ✔ | ✔
GetInteriorRingN （int） | ✔ | ✔ | ✔ | ✔
多边形。 NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>其他资源

* [SQL Server 中的空间数据](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [SpatiaLite 主页](https://www.gaia-gis.it/fossil/libspatialite)
* [Npgsql 空间文档](https://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS 文档](https://postgis.net/documentation/)
