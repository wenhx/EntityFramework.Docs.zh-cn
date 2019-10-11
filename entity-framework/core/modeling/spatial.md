---
title: 空间数据-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: cced53edadb890e4e86753ec2628218ffc4d1d5b
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181382"
---
# <a name="spatial-data"></a><span data-ttu-id="d891c-102">空间数据</span><span class="sxs-lookup"><span data-stu-id="d891c-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="d891c-103">此功能是在 EF Core 2.2 中添加的。</span><span class="sxs-lookup"><span data-stu-id="d891c-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="d891c-104">空间数据表示对象的物理位置和形状。</span><span class="sxs-lookup"><span data-stu-id="d891c-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="d891c-105">许多数据库提供对此类数据的支持，以便能够与其他数据一起进行索引和查询。</span><span class="sxs-lookup"><span data-stu-id="d891c-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="d891c-106">常见方案包括从位置在给定距离内查询对象，或选择其边框包含给定位置的对象。</span><span class="sxs-lookup"><span data-stu-id="d891c-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="d891c-107">EF Core 支持使用[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空间库映射到空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="d891c-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="d891c-108">安装</span><span class="sxs-lookup"><span data-stu-id="d891c-108">Installing</span></span>

<span data-ttu-id="d891c-109">若要在 EF Core 中使用空间数据，需要安装相应的支持 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d891c-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="d891c-110">需要安装哪个包取决于所使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="d891c-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="d891c-111">EF Core 提供程序</span><span class="sxs-lookup"><span data-stu-id="d891c-111">EF Core Provider</span></span>                        | <span data-ttu-id="d891c-112">空间 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="d891c-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="d891c-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="d891c-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="d891c-114">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="d891c-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="d891c-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="d891c-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="d891c-116">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="d891c-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="d891c-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="d891c-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="d891c-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="d891c-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="d891c-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="d891c-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="d891c-120">Npgsql. Microsoft.entityframeworkcore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="d891c-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="d891c-121">反向工程</span><span class="sxs-lookup"><span data-stu-id="d891c-121">Reverse engineering</span></span>

<span data-ttu-id="d891c-122">空间 NuGet 包还启用具有空间属性的[反向工程](../managing-schemas/scaffolding.md)模型，但需要在运行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`***之前***安装包。</span><span class="sxs-lookup"><span data-stu-id="d891c-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="d891c-123">否则，你将收到有关找不到列的类型映射的警告，将跳过这些列。</span><span class="sxs-lookup"><span data-stu-id="d891c-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="d891c-124">NetTopologySuite （NTS）</span><span class="sxs-lookup"><span data-stu-id="d891c-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="d891c-125">NetTopologySuite 是用于 .NET 的空间库。</span><span class="sxs-lookup"><span data-stu-id="d891c-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="d891c-126">EF Core 使用模型中的 NTS 类型启用映射到数据库中的空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="d891c-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="d891c-127">若要通过 NTS 启用到空间类型的映射，请在提供程序的 DbContext 选项生成器上调用 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="d891c-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="d891c-128">例如，对于 SQL Server，你应将其称为。</span><span class="sxs-lookup"><span data-stu-id="d891c-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="d891c-129">有几种空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="d891c-129">There are several spatial data types.</span></span> <span data-ttu-id="d891c-130">使用哪种类型取决于您想要允许的形状的类型。</span><span class="sxs-lookup"><span data-stu-id="d891c-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="d891c-131">下面是可用于模型中的属性的 NTS 类型的层次结构。</span><span class="sxs-lookup"><span data-stu-id="d891c-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="d891c-132">它们位于 `NetTopologySuite.Geometries` 命名空间内。</span><span class="sxs-lookup"><span data-stu-id="d891c-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="d891c-133">Geometry</span><span class="sxs-lookup"><span data-stu-id="d891c-133">Geometry</span></span>
  * <span data-ttu-id="d891c-134">点</span><span class="sxs-lookup"><span data-stu-id="d891c-134">Point</span></span>
  * <span data-ttu-id="d891c-135">LineString</span><span class="sxs-lookup"><span data-stu-id="d891c-135">LineString</span></span>
  * <span data-ttu-id="d891c-136">多边形</span><span class="sxs-lookup"><span data-stu-id="d891c-136">Polygon</span></span>
  * <span data-ttu-id="d891c-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="d891c-137">GeometryCollection</span></span>
    * <span data-ttu-id="d891c-138">单</span><span class="sxs-lookup"><span data-stu-id="d891c-138">MultiPoint</span></span>
    * <span data-ttu-id="d891c-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="d891c-139">MultiLineString</span></span>
    * <span data-ttu-id="d891c-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="d891c-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="d891c-141">NTS 不支持 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="d891c-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="d891c-142">使用基本几何图形类型允许属性指定任意类型的形状。</span><span class="sxs-lookup"><span data-stu-id="d891c-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="d891c-143">以下实体类可用于映射到[广角导入示例数据库](https://go.microsoft.com/fwlink/?LinkID=800630)中的表。</span><span class="sxs-lookup"><span data-stu-id="d891c-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="d891c-144">创建值</span><span class="sxs-lookup"><span data-stu-id="d891c-144">Creating values</span></span>

<span data-ttu-id="d891c-145">您可以使用构造函数来创建 geometry 对象;但是，NTS 建议改为使用几何工厂。</span><span class="sxs-lookup"><span data-stu-id="d891c-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="d891c-146">这允许您指定默认 SRID （坐标使用的空间引用系统），并使您能够控制更高级的任务（例如，在计算过程中使用）和坐标序列（确定哪些坐标维度和度量值--可用）。</span><span class="sxs-lookup"><span data-stu-id="d891c-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="d891c-147">4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。</span><span class="sxs-lookup"><span data-stu-id="d891c-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="d891c-148">经度和纬度</span><span class="sxs-lookup"><span data-stu-id="d891c-148">Longitude and Latitude</span></span>

<span data-ttu-id="d891c-149">NTS 中的坐标采用 X 和 Y 值。</span><span class="sxs-lookup"><span data-stu-id="d891c-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="d891c-150">若要表示经度和纬度，请将 X 用于经度，将 Y 用于纬度。</span><span class="sxs-lookup"><span data-stu-id="d891c-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="d891c-151">请注意，这是从 `latitude, longitude` 格式**反向**的，你通常会看到这些值。</span><span class="sxs-lookup"><span data-stu-id="d891c-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="d891c-152">在客户端操作过程中忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="d891c-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="d891c-153">NTS 在操作过程中忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="d891c-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="d891c-154">它假定为平面坐标系统。</span><span class="sxs-lookup"><span data-stu-id="d891c-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="d891c-155">这意味着，如果在经度和纬度方面指定了坐标，则某些客户端计算的值（例如，距离、长度和区域）将为度数，而不是计量。</span><span class="sxs-lookup"><span data-stu-id="d891c-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="d891c-156">若要获得更有意义的值，首先需要使用库（如[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) ）在计算这些值之前投影到另一个坐标系统的坐标。</span><span class="sxs-lookup"><span data-stu-id="d891c-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="d891c-157">如果通过 EF Core 通过 SQL 对操作进行服务器计算，则该结果的单元将由数据库确定。</span><span class="sxs-lookup"><span data-stu-id="d891c-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="d891c-158">下面是一个示例，说明如何使用 ProjNet4GeoAPI 来计算两个城市之间的距离。</span><span class="sxs-lookup"><span data-stu-id="d891c-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="d891c-159">查询数据</span><span class="sxs-lookup"><span data-stu-id="d891c-159">Querying Data</span></span>

<span data-ttu-id="d891c-160">在 LINQ 中，可用作数据库函数的 NTS 方法和属性将转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="d891c-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="d891c-161">例如，在以下查询中转换距离和包含方法。</span><span class="sxs-lookup"><span data-stu-id="d891c-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="d891c-162">本文末尾的表格显示了不同 EF Core 提供商支持哪些成员。</span><span class="sxs-lookup"><span data-stu-id="d891c-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="d891c-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d891c-163">SQL Server</span></span>

<span data-ttu-id="d891c-164">如果你正在使用 SQL Server，你还应该注意一些其他问题。</span><span class="sxs-lookup"><span data-stu-id="d891c-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="d891c-165">地理或几何图形</span><span class="sxs-lookup"><span data-stu-id="d891c-165">Geography or geometry</span></span>

<span data-ttu-id="d891c-166">默认情况下，空间属性映射到 SQL Server 中的 @no__t 列。</span><span class="sxs-lookup"><span data-stu-id="d891c-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="d891c-167">若要使用 `geometry`，请在模型中[配置列类型](xref:core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="d891c-167">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="d891c-168">地理多边形环</span><span class="sxs-lookup"><span data-stu-id="d891c-168">Geography polygon rings</span></span>

<span data-ttu-id="d891c-169">当使用 @no__t 0 列类型时，SQL Server 会对外部环（或外壳）和内部环（或孔）施加附加要求。</span><span class="sxs-lookup"><span data-stu-id="d891c-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="d891c-170">外部环必须逆时针旋转，并顺时针旋转内部环。</span><span class="sxs-lookup"><span data-stu-id="d891c-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="d891c-171">NTS 在将值发送到数据库之前对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="d891c-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="d891c-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="d891c-172">FullGlobe</span></span>

<span data-ttu-id="d891c-173">使用 @no__t 列类型时，SQL Server 具有非标准几何类型来表示全地球。</span><span class="sxs-lookup"><span data-stu-id="d891c-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="d891c-174">它还提供了一种方法，用于根据全地球（无外部环）来表示多边形。</span><span class="sxs-lookup"><span data-stu-id="d891c-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="d891c-175">NTS 不支持这两种方法。</span><span class="sxs-lookup"><span data-stu-id="d891c-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="d891c-176">NTS 不支持基于 FullGlobe 和多边形。</span><span class="sxs-lookup"><span data-stu-id="d891c-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="d891c-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="d891c-177">SQLite</span></span>

<span data-ttu-id="d891c-178">下面是使用 SQLite 的一些其他信息。</span><span class="sxs-lookup"><span data-stu-id="d891c-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="d891c-179">安装 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="d891c-179">Installing SpatiaLite</span></span>

<span data-ttu-id="d891c-180">在 Windows 上，本机 mod_spatialite 库以 NuGet 包依赖关系的形式分发。</span><span class="sxs-lookup"><span data-stu-id="d891c-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="d891c-181">其他平台需要单独安装它。</span><span class="sxs-lookup"><span data-stu-id="d891c-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="d891c-182">通常使用软件程序包管理器完成此操作。</span><span class="sxs-lookup"><span data-stu-id="d891c-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="d891c-183">例如，可以在 Ubuntu 上使用 APT 和 MacOS 上的 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="d891c-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="d891c-184">配置 SRID</span><span class="sxs-lookup"><span data-stu-id="d891c-184">Configuring SRID</span></span>

<span data-ttu-id="d891c-185">在 SpatiaLite 中，列需要为每个列指定一个 SRID。</span><span class="sxs-lookup"><span data-stu-id="d891c-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="d891c-186">默认的 SRID 为 `0`。</span><span class="sxs-lookup"><span data-stu-id="d891c-186">The default SRID is `0`.</span></span> <span data-ttu-id="d891c-187">使用 ForSqliteHasSrid 方法指定其他 SRID。</span><span class="sxs-lookup"><span data-stu-id="d891c-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="d891c-188">维度</span><span class="sxs-lookup"><span data-stu-id="d891c-188">Dimension</span></span>

<span data-ttu-id="d891c-189">类似于 SRID，列的维度（或坐标）也被指定为列的一部分。</span><span class="sxs-lookup"><span data-stu-id="d891c-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="d891c-190">默认坐标为 X 和 Y。使用 ForSqliteHasDimension 方法启用其他坐标（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="d891c-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="d891c-191">转换的操作</span><span class="sxs-lookup"><span data-stu-id="d891c-191">Translated Operations</span></span>

<span data-ttu-id="d891c-192">此表显示每个 EF Core 提供程序将哪些 NTS 成员转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="d891c-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="d891c-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="d891c-193">NetTopologySuite</span></span> | <span data-ttu-id="d891c-194">SQL Server （geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-194">SQL Server (geometry)</span></span> | <span data-ttu-id="d891c-195">SQL Server （geography）</span><span class="sxs-lookup"><span data-stu-id="d891c-195">SQL Server (geography)</span></span> | <span data-ttu-id="d891c-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="d891c-196">SQLite</span></span> | <span data-ttu-id="d891c-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="d891c-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="d891c-198">Geometry</span><span class="sxs-lookup"><span data-stu-id="d891c-198">Geometry.Area</span></span> | <span data-ttu-id="d891c-199">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-199">✔</span></span> | <span data-ttu-id="d891c-200">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-200">✔</span></span> | <span data-ttu-id="d891c-201">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-201">✔</span></span> | <span data-ttu-id="d891c-202">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-202">✔</span></span>
<span data-ttu-id="d891c-203">AsBinary （）</span><span class="sxs-lookup"><span data-stu-id="d891c-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="d891c-204">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-204">✔</span></span> | <span data-ttu-id="d891c-205">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-205">✔</span></span> | <span data-ttu-id="d891c-206">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-206">✔</span></span> | <span data-ttu-id="d891c-207">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-207">✔</span></span>
<span data-ttu-id="d891c-208">AsText （）</span><span class="sxs-lookup"><span data-stu-id="d891c-208">Geometry.AsText()</span></span> | <span data-ttu-id="d891c-209">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-209">✔</span></span> | <span data-ttu-id="d891c-210">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-210">✔</span></span> | <span data-ttu-id="d891c-211">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-211">✔</span></span> | <span data-ttu-id="d891c-212">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-212">✔</span></span>
<span data-ttu-id="d891c-213">Geometry</span><span class="sxs-lookup"><span data-stu-id="d891c-213">Geometry.Boundary</span></span> | <span data-ttu-id="d891c-214">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-214">✔</span></span> | | <span data-ttu-id="d891c-215">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-215">✔</span></span> | <span data-ttu-id="d891c-216">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-216">✔</span></span>
<span data-ttu-id="d891c-217">Geometry （双精度型）</span><span class="sxs-lookup"><span data-stu-id="d891c-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="d891c-218">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-218">✔</span></span> | <span data-ttu-id="d891c-219">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-219">✔</span></span> | <span data-ttu-id="d891c-220">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-220">✔</span></span> | <span data-ttu-id="d891c-221">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-221">✔</span></span>
<span data-ttu-id="d891c-222">Geometry （double，int）</span><span class="sxs-lookup"><span data-stu-id="d891c-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="d891c-223">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-223">✔</span></span>
<span data-ttu-id="d891c-224">质心</span><span class="sxs-lookup"><span data-stu-id="d891c-224">Geometry.Centroid</span></span> | <span data-ttu-id="d891c-225">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-225">✔</span></span> | | <span data-ttu-id="d891c-226">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-226">✔</span></span> | <span data-ttu-id="d891c-227">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-227">✔</span></span>
<span data-ttu-id="d891c-228">Geometry。 Contains （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-228">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="d891c-229">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-229">✔</span></span> | <span data-ttu-id="d891c-230">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-230">✔</span></span> | <span data-ttu-id="d891c-231">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-231">✔</span></span> | <span data-ttu-id="d891c-232">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-232">✔</span></span>
<span data-ttu-id="d891c-233">ConvexHull （）</span><span class="sxs-lookup"><span data-stu-id="d891c-233">Geometry.ConvexHull()</span></span> | <span data-ttu-id="d891c-234">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-234">✔</span></span> | <span data-ttu-id="d891c-235">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-235">✔</span></span> | <span data-ttu-id="d891c-236">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-236">✔</span></span> | <span data-ttu-id="d891c-237">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-237">✔</span></span>
<span data-ttu-id="d891c-238">CoveredBy （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-238">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="d891c-239">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-239">✔</span></span> | <span data-ttu-id="d891c-240">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-240">✔</span></span>
<span data-ttu-id="d891c-241">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-241">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="d891c-242">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-242">✔</span></span> | <span data-ttu-id="d891c-243">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-243">✔</span></span>
<span data-ttu-id="d891c-244">几何。交叉（几何）</span><span class="sxs-lookup"><span data-stu-id="d891c-244">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="d891c-245">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-245">✔</span></span> | | <span data-ttu-id="d891c-246">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-246">✔</span></span> | <span data-ttu-id="d891c-247">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-247">✔</span></span>
<span data-ttu-id="d891c-248">几何差（几何）</span><span class="sxs-lookup"><span data-stu-id="d891c-248">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="d891c-249">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-249">✔</span></span> | <span data-ttu-id="d891c-250">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-250">✔</span></span> | <span data-ttu-id="d891c-251">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-251">✔</span></span> | <span data-ttu-id="d891c-252">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-252">✔</span></span>
<span data-ttu-id="d891c-253">Geometry。维度</span><span class="sxs-lookup"><span data-stu-id="d891c-253">Geometry.Dimension</span></span> | <span data-ttu-id="d891c-254">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-254">✔</span></span> | <span data-ttu-id="d891c-255">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-255">✔</span></span> | <span data-ttu-id="d891c-256">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-256">✔</span></span> | <span data-ttu-id="d891c-257">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-257">✔</span></span>
<span data-ttu-id="d891c-258">不连续（Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-258">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="d891c-259">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-259">✔</span></span> | <span data-ttu-id="d891c-260">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-260">✔</span></span> | <span data-ttu-id="d891c-261">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-261">✔</span></span> | <span data-ttu-id="d891c-262">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-262">✔</span></span>
<span data-ttu-id="d891c-263">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-263">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="d891c-264">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-264">✔</span></span> | <span data-ttu-id="d891c-265">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-265">✔</span></span> | <span data-ttu-id="d891c-266">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-266">✔</span></span> | <span data-ttu-id="d891c-267">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-267">✔</span></span>
<span data-ttu-id="d891c-268">Geometry 信封</span><span class="sxs-lookup"><span data-stu-id="d891c-268">Geometry.Envelope</span></span> | <span data-ttu-id="d891c-269">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-269">✔</span></span> | | <span data-ttu-id="d891c-270">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-270">✔</span></span> | <span data-ttu-id="d891c-271">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-271">✔</span></span>
<span data-ttu-id="d891c-272">EqualsExact （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-272">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="d891c-273">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-273">✔</span></span>
<span data-ttu-id="d891c-274">EqualsTopologically （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-274">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="d891c-275">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-275">✔</span></span> | <span data-ttu-id="d891c-276">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-276">✔</span></span> | <span data-ttu-id="d891c-277">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-277">✔</span></span> | <span data-ttu-id="d891c-278">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-278">✔</span></span>
<span data-ttu-id="d891c-279">GeometryType</span><span class="sxs-lookup"><span data-stu-id="d891c-279">Geometry.GeometryType</span></span> | <span data-ttu-id="d891c-280">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-280">✔</span></span> | <span data-ttu-id="d891c-281">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-281">✔</span></span> | <span data-ttu-id="d891c-282">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-282">✔</span></span> | <span data-ttu-id="d891c-283">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-283">✔</span></span>
<span data-ttu-id="d891c-284">GetGeometryN （int）</span><span class="sxs-lookup"><span data-stu-id="d891c-284">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="d891c-285">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-285">✔</span></span> | | <span data-ttu-id="d891c-286">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-286">✔</span></span> | <span data-ttu-id="d891c-287">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-287">✔</span></span>
<span data-ttu-id="d891c-288">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="d891c-288">Geometry.InteriorPoint</span></span> | <span data-ttu-id="d891c-289">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-289">✔</span></span> | | <span data-ttu-id="d891c-290">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-290">✔</span></span>
<span data-ttu-id="d891c-291">几何交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-291">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="d891c-292">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-292">✔</span></span> | <span data-ttu-id="d891c-293">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-293">✔</span></span> | <span data-ttu-id="d891c-294">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-294">✔</span></span> | <span data-ttu-id="d891c-295">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-295">✔</span></span>
<span data-ttu-id="d891c-296">几何和交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-296">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="d891c-297">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-297">✔</span></span> | <span data-ttu-id="d891c-298">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-298">✔</span></span> | <span data-ttu-id="d891c-299">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-299">✔</span></span> | <span data-ttu-id="d891c-300">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-300">✔</span></span>
<span data-ttu-id="d891c-301">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="d891c-301">Geometry.IsEmpty</span></span> | <span data-ttu-id="d891c-302">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-302">✔</span></span> | <span data-ttu-id="d891c-303">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-303">✔</span></span> | <span data-ttu-id="d891c-304">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-304">✔</span></span> | <span data-ttu-id="d891c-305">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-305">✔</span></span>
<span data-ttu-id="d891c-306">IsSimple</span><span class="sxs-lookup"><span data-stu-id="d891c-306">Geometry.IsSimple</span></span> | <span data-ttu-id="d891c-307">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-307">✔</span></span> | | <span data-ttu-id="d891c-308">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-308">✔</span></span> | <span data-ttu-id="d891c-309">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-309">✔</span></span>
<span data-ttu-id="d891c-310">Geometry</span><span class="sxs-lookup"><span data-stu-id="d891c-310">Geometry.IsValid</span></span> | <span data-ttu-id="d891c-311">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-311">✔</span></span> | <span data-ttu-id="d891c-312">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-312">✔</span></span> | <span data-ttu-id="d891c-313">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-313">✔</span></span> | <span data-ttu-id="d891c-314">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-314">✔</span></span>
<span data-ttu-id="d891c-315">IsWithinDistance （Geometry，double）</span><span class="sxs-lookup"><span data-stu-id="d891c-315">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="d891c-316">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-316">✔</span></span> | | <span data-ttu-id="d891c-317">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-317">✔</span></span>
<span data-ttu-id="d891c-318">Geometry。长度</span><span class="sxs-lookup"><span data-stu-id="d891c-318">Geometry.Length</span></span> | <span data-ttu-id="d891c-319">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-319">✔</span></span> | <span data-ttu-id="d891c-320">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-320">✔</span></span> | <span data-ttu-id="d891c-321">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-321">✔</span></span> | <span data-ttu-id="d891c-322">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-322">✔</span></span>
<span data-ttu-id="d891c-323">NumGeometries</span><span class="sxs-lookup"><span data-stu-id="d891c-323">Geometry.NumGeometries</span></span> | <span data-ttu-id="d891c-324">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-324">✔</span></span> | <span data-ttu-id="d891c-325">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-325">✔</span></span> | <span data-ttu-id="d891c-326">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-326">✔</span></span> | <span data-ttu-id="d891c-327">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-327">✔</span></span>
<span data-ttu-id="d891c-328">X.numpoints</span><span class="sxs-lookup"><span data-stu-id="d891c-328">Geometry.NumPoints</span></span> | <span data-ttu-id="d891c-329">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-329">✔</span></span> | <span data-ttu-id="d891c-330">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-330">✔</span></span> | <span data-ttu-id="d891c-331">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-331">✔</span></span> | <span data-ttu-id="d891c-332">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-332">✔</span></span>
<span data-ttu-id="d891c-333">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="d891c-333">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="d891c-334">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-334">✔</span></span> | <span data-ttu-id="d891c-335">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-335">✔</span></span> | <span data-ttu-id="d891c-336">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-336">✔</span></span>
<span data-ttu-id="d891c-337">Geometry 重叠（Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-337">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="d891c-338">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-338">✔</span></span> | <span data-ttu-id="d891c-339">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-339">✔</span></span> | <span data-ttu-id="d891c-340">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-340">✔</span></span> | <span data-ttu-id="d891c-341">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-341">✔</span></span>
<span data-ttu-id="d891c-342">PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="d891c-342">Geometry.PointOnSurface</span></span> | <span data-ttu-id="d891c-343">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-343">✔</span></span> | | <span data-ttu-id="d891c-344">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-344">✔</span></span> | <span data-ttu-id="d891c-345">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-345">✔</span></span>
<span data-ttu-id="d891c-346">Geometry （Geometry，string）</span><span class="sxs-lookup"><span data-stu-id="d891c-346">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="d891c-347">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-347">✔</span></span> | | <span data-ttu-id="d891c-348">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-348">✔</span></span> | <span data-ttu-id="d891c-349">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-349">✔</span></span>
<span data-ttu-id="d891c-350">Geometry 反向（）</span><span class="sxs-lookup"><span data-stu-id="d891c-350">Geometry.Reverse()</span></span> | | | <span data-ttu-id="d891c-351">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-351">✔</span></span> | <span data-ttu-id="d891c-352">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-352">✔</span></span>
<span data-ttu-id="d891c-353">SRID</span><span class="sxs-lookup"><span data-stu-id="d891c-353">Geometry.SRID</span></span> | <span data-ttu-id="d891c-354">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-354">✔</span></span> | <span data-ttu-id="d891c-355">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-355">✔</span></span> | <span data-ttu-id="d891c-356">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-356">✔</span></span> | <span data-ttu-id="d891c-357">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-357">✔</span></span>
<span data-ttu-id="d891c-358">SymmetricDifference （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-358">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="d891c-359">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-359">✔</span></span> | <span data-ttu-id="d891c-360">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-360">✔</span></span> | <span data-ttu-id="d891c-361">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-361">✔</span></span> | <span data-ttu-id="d891c-362">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-362">✔</span></span>
<span data-ttu-id="d891c-363">ToBinary （）</span><span class="sxs-lookup"><span data-stu-id="d891c-363">Geometry.ToBinary()</span></span> | <span data-ttu-id="d891c-364">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-364">✔</span></span> | <span data-ttu-id="d891c-365">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-365">✔</span></span> | <span data-ttu-id="d891c-366">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-366">✔</span></span> | <span data-ttu-id="d891c-367">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-367">✔</span></span>
<span data-ttu-id="d891c-368">ToText （）</span><span class="sxs-lookup"><span data-stu-id="d891c-368">Geometry.ToText()</span></span> | <span data-ttu-id="d891c-369">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-369">✔</span></span> | <span data-ttu-id="d891c-370">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-370">✔</span></span> | <span data-ttu-id="d891c-371">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-371">✔</span></span> | <span data-ttu-id="d891c-372">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-372">✔</span></span>
<span data-ttu-id="d891c-373">几何图形（几何）</span><span class="sxs-lookup"><span data-stu-id="d891c-373">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="d891c-374">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-374">✔</span></span> | | <span data-ttu-id="d891c-375">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-375">✔</span></span> | <span data-ttu-id="d891c-376">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-376">✔</span></span>
<span data-ttu-id="d891c-377">Geometry （）</span><span class="sxs-lookup"><span data-stu-id="d891c-377">Geometry.Union()</span></span> | | | <span data-ttu-id="d891c-378">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-378">✔</span></span>
<span data-ttu-id="d891c-379">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-379">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="d891c-380">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-380">✔</span></span> | <span data-ttu-id="d891c-381">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-381">✔</span></span> | <span data-ttu-id="d891c-382">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-382">✔</span></span> | <span data-ttu-id="d891c-383">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-383">✔</span></span>
<span data-ttu-id="d891c-384">几何图形。内（Geometry）</span><span class="sxs-lookup"><span data-stu-id="d891c-384">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="d891c-385">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-385">✔</span></span> | <span data-ttu-id="d891c-386">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-386">✔</span></span> | <span data-ttu-id="d891c-387">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-387">✔</span></span> | <span data-ttu-id="d891c-388">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-388">✔</span></span>
<span data-ttu-id="d891c-389">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="d891c-389">GeometryCollection.Count</span></span> | <span data-ttu-id="d891c-390">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-390">✔</span></span> | <span data-ttu-id="d891c-391">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-391">✔</span></span> | <span data-ttu-id="d891c-392">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-392">✔</span></span> | <span data-ttu-id="d891c-393">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-393">✔</span></span>
<span data-ttu-id="d891c-394">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="d891c-394">GeometryCollection[int]</span></span> | <span data-ttu-id="d891c-395">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-395">✔</span></span> | <span data-ttu-id="d891c-396">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-396">✔</span></span> | <span data-ttu-id="d891c-397">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-397">✔</span></span> | <span data-ttu-id="d891c-398">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-398">✔</span></span>
<span data-ttu-id="d891c-399">LineString</span><span class="sxs-lookup"><span data-stu-id="d891c-399">LineString.Count</span></span> | <span data-ttu-id="d891c-400">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-400">✔</span></span> | <span data-ttu-id="d891c-401">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-401">✔</span></span> | <span data-ttu-id="d891c-402">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-402">✔</span></span> | <span data-ttu-id="d891c-403">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-403">✔</span></span>
<span data-ttu-id="d891c-404">LineString 终结点</span><span class="sxs-lookup"><span data-stu-id="d891c-404">LineString.EndPoint</span></span> | <span data-ttu-id="d891c-405">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-405">✔</span></span> | <span data-ttu-id="d891c-406">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-406">✔</span></span> | <span data-ttu-id="d891c-407">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-407">✔</span></span> | <span data-ttu-id="d891c-408">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-408">✔</span></span>
<span data-ttu-id="d891c-409">LineString. GetPointN （int）</span><span class="sxs-lookup"><span data-stu-id="d891c-409">LineString.GetPointN(int)</span></span> | <span data-ttu-id="d891c-410">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-410">✔</span></span> | <span data-ttu-id="d891c-411">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-411">✔</span></span> | <span data-ttu-id="d891c-412">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-412">✔</span></span> | <span data-ttu-id="d891c-413">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-413">✔</span></span>
<span data-ttu-id="d891c-414">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="d891c-414">LineString.IsClosed</span></span> | <span data-ttu-id="d891c-415">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-415">✔</span></span> | <span data-ttu-id="d891c-416">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-416">✔</span></span> | <span data-ttu-id="d891c-417">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-417">✔</span></span> | <span data-ttu-id="d891c-418">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-418">✔</span></span>
<span data-ttu-id="d891c-419">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="d891c-419">LineString.IsRing</span></span> | <span data-ttu-id="d891c-420">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-420">✔</span></span> | | <span data-ttu-id="d891c-421">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-421">✔</span></span> | <span data-ttu-id="d891c-422">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-422">✔</span></span>
<span data-ttu-id="d891c-423">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="d891c-423">LineString.StartPoint</span></span> | <span data-ttu-id="d891c-424">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-424">✔</span></span> | <span data-ttu-id="d891c-425">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-425">✔</span></span> | <span data-ttu-id="d891c-426">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-426">✔</span></span> | <span data-ttu-id="d891c-427">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-427">✔</span></span>
<span data-ttu-id="d891c-428">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="d891c-428">MultiLineString.IsClosed</span></span> | <span data-ttu-id="d891c-429">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-429">✔</span></span> | <span data-ttu-id="d891c-430">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-430">✔</span></span> | <span data-ttu-id="d891c-431">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-431">✔</span></span> | <span data-ttu-id="d891c-432">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-432">✔</span></span>
<span data-ttu-id="d891c-433">点 M</span><span class="sxs-lookup"><span data-stu-id="d891c-433">Point.M</span></span> | <span data-ttu-id="d891c-434">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-434">✔</span></span> | <span data-ttu-id="d891c-435">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-435">✔</span></span> | <span data-ttu-id="d891c-436">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-436">✔</span></span> | <span data-ttu-id="d891c-437">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-437">✔</span></span>
<span data-ttu-id="d891c-438">点 X</span><span class="sxs-lookup"><span data-stu-id="d891c-438">Point.X</span></span> | <span data-ttu-id="d891c-439">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-439">✔</span></span> | <span data-ttu-id="d891c-440">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-440">✔</span></span> | <span data-ttu-id="d891c-441">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-441">✔</span></span> | <span data-ttu-id="d891c-442">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-442">✔</span></span>
<span data-ttu-id="d891c-443">Point。 Y</span><span class="sxs-lookup"><span data-stu-id="d891c-443">Point.Y</span></span> | <span data-ttu-id="d891c-444">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-444">✔</span></span> | <span data-ttu-id="d891c-445">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-445">✔</span></span> | <span data-ttu-id="d891c-446">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-446">✔</span></span> | <span data-ttu-id="d891c-447">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-447">✔</span></span>
<span data-ttu-id="d891c-448">点 Z</span><span class="sxs-lookup"><span data-stu-id="d891c-448">Point.Z</span></span> | <span data-ttu-id="d891c-449">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-449">✔</span></span> | <span data-ttu-id="d891c-450">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-450">✔</span></span> | <span data-ttu-id="d891c-451">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-451">✔</span></span> | <span data-ttu-id="d891c-452">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-452">✔</span></span>
<span data-ttu-id="d891c-453">多边形。 ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="d891c-453">Polygon.ExteriorRing</span></span> | <span data-ttu-id="d891c-454">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-454">✔</span></span> | <span data-ttu-id="d891c-455">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-455">✔</span></span> | <span data-ttu-id="d891c-456">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-456">✔</span></span> | <span data-ttu-id="d891c-457">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-457">✔</span></span>
<span data-ttu-id="d891c-458">GetInteriorRingN （int）</span><span class="sxs-lookup"><span data-stu-id="d891c-458">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="d891c-459">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-459">✔</span></span> | <span data-ttu-id="d891c-460">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-460">✔</span></span> | <span data-ttu-id="d891c-461">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-461">✔</span></span> | <span data-ttu-id="d891c-462">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-462">✔</span></span>
<span data-ttu-id="d891c-463">多边形。 NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="d891c-463">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="d891c-464">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-464">✔</span></span> | <span data-ttu-id="d891c-465">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-465">✔</span></span> | <span data-ttu-id="d891c-466">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-466">✔</span></span> | <span data-ttu-id="d891c-467">✔</span><span class="sxs-lookup"><span data-stu-id="d891c-467">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d891c-468">其他资源</span><span class="sxs-lookup"><span data-stu-id="d891c-468">Additional resources</span></span>

* [<span data-ttu-id="d891c-469">SQL Server 中的空间数据</span><span class="sxs-lookup"><span data-stu-id="d891c-469">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="d891c-470">SpatiaLite 主页</span><span class="sxs-lookup"><span data-stu-id="d891c-470">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="d891c-471">Npgsql 空间文档</span><span class="sxs-lookup"><span data-stu-id="d891c-471">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="d891c-472">PostGIS 文档</span><span class="sxs-lookup"><span data-stu-id="d891c-472">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
