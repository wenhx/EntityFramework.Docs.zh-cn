---
title: 空间数据-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 026df735473e31f1c1463c1fbc6f46c4fd6dfd4f
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921724"
---
# <a name="spatial-data"></a><span data-ttu-id="09713-102">空间数据</span><span class="sxs-lookup"><span data-stu-id="09713-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="09713-103">此功能是在 EF Core 2.2 中添加的。</span><span class="sxs-lookup"><span data-stu-id="09713-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="09713-104">空间数据表示对象的物理位置和形状。</span><span class="sxs-lookup"><span data-stu-id="09713-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="09713-105">许多数据库提供对此类数据的支持，以便能够与其他数据一起进行索引和查询。</span><span class="sxs-lookup"><span data-stu-id="09713-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="09713-106">常见方案包括从位置在给定距离内查询对象，或选择其边框包含给定位置的对象。</span><span class="sxs-lookup"><span data-stu-id="09713-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="09713-107">EF Core 支持使用[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空间库映射到空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="09713-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="09713-108">安装</span><span class="sxs-lookup"><span data-stu-id="09713-108">Installing</span></span>

<span data-ttu-id="09713-109">若要在 EF Core 中使用空间数据，需要安装相应的支持 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="09713-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="09713-110">需要安装哪个包取决于所使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="09713-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="09713-111">EF Core 提供程序</span><span class="sxs-lookup"><span data-stu-id="09713-111">EF Core Provider</span></span>                        | <span data-ttu-id="09713-112">空间 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="09713-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="09713-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="09713-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="09713-114">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="09713-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="09713-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="09713-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="09713-116">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="09713-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="09713-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="09713-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="09713-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="09713-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="09713-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="09713-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="09713-120">Npgsql. Microsoft.entityframeworkcore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="09713-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="09713-121">反向工程</span><span class="sxs-lookup"><span data-stu-id="09713-121">Reverse engineering</span></span>

<span data-ttu-id="09713-122">空间 NuGet 包还启用具有空间属性的[反向工程](../managing-schemas/scaffolding.md)模型，但需要在运行`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`***之前***安装包。</span><span class="sxs-lookup"><span data-stu-id="09713-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="09713-123">否则，你将收到有关找不到列的类型映射的警告，将跳过这些列。</span><span class="sxs-lookup"><span data-stu-id="09713-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="09713-124">NetTopologySuite （NTS）</span><span class="sxs-lookup"><span data-stu-id="09713-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="09713-125">NetTopologySuite 是用于 .NET 的空间库。</span><span class="sxs-lookup"><span data-stu-id="09713-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="09713-126">EF Core 使用模型中的 NTS 类型启用映射到数据库中的空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="09713-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="09713-127">若要通过 NTS 启用到空间类型的映射，请在提供程序的 DbContext 选项生成器上调用 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="09713-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="09713-128">例如，对于 SQL Server，你应将其称为。</span><span class="sxs-lookup"><span data-stu-id="09713-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="09713-129">有几种空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="09713-129">There are several spatial data types.</span></span> <span data-ttu-id="09713-130">使用哪种类型取决于您想要允许的形状的类型。</span><span class="sxs-lookup"><span data-stu-id="09713-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="09713-131">下面是可用于模型中的属性的 NTS 类型的层次结构。</span><span class="sxs-lookup"><span data-stu-id="09713-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="09713-132">它们位于`NetTopologySuite.Geometries`命名空间内。</span><span class="sxs-lookup"><span data-stu-id="09713-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="09713-133">geometry</span><span class="sxs-lookup"><span data-stu-id="09713-133">Geometry</span></span>
  * <span data-ttu-id="09713-134">点</span><span class="sxs-lookup"><span data-stu-id="09713-134">Point</span></span>
  * <span data-ttu-id="09713-135">LineString</span><span class="sxs-lookup"><span data-stu-id="09713-135">LineString</span></span>
  * <span data-ttu-id="09713-136">多边形</span><span class="sxs-lookup"><span data-stu-id="09713-136">Polygon</span></span>
  * <span data-ttu-id="09713-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="09713-137">GeometryCollection</span></span>
    * <span data-ttu-id="09713-138">单</span><span class="sxs-lookup"><span data-stu-id="09713-138">MultiPoint</span></span>
    * <span data-ttu-id="09713-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="09713-139">MultiLineString</span></span>
    * <span data-ttu-id="09713-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="09713-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="09713-141">NTS 不支持 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="09713-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="09713-142">使用基本几何图形类型允许属性指定任意类型的形状。</span><span class="sxs-lookup"><span data-stu-id="09713-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="09713-143">以下实体类可用于映射到[广角导入示例数据库](http://go.microsoft.com/fwlink/?LinkID=800630)中的表。</span><span class="sxs-lookup"><span data-stu-id="09713-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="09713-144">创建值</span><span class="sxs-lookup"><span data-stu-id="09713-144">Creating values</span></span>

<span data-ttu-id="09713-145">您可以使用构造函数来创建 geometry 对象;但是，NTS 建议改为使用几何工厂。</span><span class="sxs-lookup"><span data-stu-id="09713-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="09713-146">这允许您指定默认 SRID （坐标使用的空间引用系统），并使您能够控制更高级的任务（例如，在计算过程中使用）和坐标序列（确定哪些坐标维度和度量值--可用）。</span><span class="sxs-lookup"><span data-stu-id="09713-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="09713-147">4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。</span><span class="sxs-lookup"><span data-stu-id="09713-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="09713-148">经度和纬度</span><span class="sxs-lookup"><span data-stu-id="09713-148">Longitude and Latitude</span></span>

<span data-ttu-id="09713-149">NTS 中的坐标采用 X 和 Y 值。</span><span class="sxs-lookup"><span data-stu-id="09713-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="09713-150">若要表示经度和纬度，请将 X 用于经度，将 Y 用于纬度。</span><span class="sxs-lookup"><span data-stu-id="09713-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="09713-151">请注意，这是从通常`latitude, longitude`会看到这些值的格式反向进行的。</span><span class="sxs-lookup"><span data-stu-id="09713-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="09713-152">在客户端操作过程中忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="09713-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="09713-153">NTS 在操作过程中忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="09713-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="09713-154">它假定为平面坐标系统。</span><span class="sxs-lookup"><span data-stu-id="09713-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="09713-155">这意味着，如果在经度和纬度方面指定了坐标，则某些客户端计算的值（例如，距离、长度和区域）将为度数，而不是计量。</span><span class="sxs-lookup"><span data-stu-id="09713-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="09713-156">若要获得更有意义的值，首先需要使用库（如[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) ）在计算这些值之前投影到另一个坐标系统的坐标。</span><span class="sxs-lookup"><span data-stu-id="09713-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="09713-157">如果通过 EF Core 通过 SQL 对操作进行服务器计算，则该结果的单元将由数据库确定。</span><span class="sxs-lookup"><span data-stu-id="09713-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="09713-158">下面是一个示例，说明如何使用 ProjNet4GeoAPI 来计算两个城市之间的距离。</span><span class="sxs-lookup"><span data-stu-id="09713-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="09713-159">查询数据</span><span class="sxs-lookup"><span data-stu-id="09713-159">Querying Data</span></span>

<span data-ttu-id="09713-160">在 LINQ 中，可用作数据库函数的 NTS 方法和属性将转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="09713-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="09713-161">例如，在以下查询中转换距离和包含方法。</span><span class="sxs-lookup"><span data-stu-id="09713-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="09713-162">本文末尾的表格显示了不同 EF Core 提供商支持哪些成员。</span><span class="sxs-lookup"><span data-stu-id="09713-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="09713-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="09713-163">SQL Server</span></span>

<span data-ttu-id="09713-164">如果你正在使用 SQL Server，你还应该注意一些其他问题。</span><span class="sxs-lookup"><span data-stu-id="09713-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="09713-165">地理或几何图形</span><span class="sxs-lookup"><span data-stu-id="09713-165">Geography or geometry</span></span>

<span data-ttu-id="09713-166">默认情况下，空间属性映射到`geography` SQL Server 中的列。</span><span class="sxs-lookup"><span data-stu-id="09713-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="09713-167">若要`geometry`使用，请在模型中[配置列类型](xref:core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="09713-167">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="09713-168">地理多边形环</span><span class="sxs-lookup"><span data-stu-id="09713-168">Geography polygon rings</span></span>

<span data-ttu-id="09713-169">当使用`geography`列类型时，SQL Server 会对外部环（或外壳）和内部环（或孔）施加附加要求。</span><span class="sxs-lookup"><span data-stu-id="09713-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="09713-170">外部环必须逆时针旋转，并顺时针旋转内部环。</span><span class="sxs-lookup"><span data-stu-id="09713-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="09713-171">NTS 在将值发送到数据库之前对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="09713-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="09713-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="09713-172">FullGlobe</span></span>

<span data-ttu-id="09713-173">在使用`geography`列类型时，SQL Server 具有非标准几何类型来表示全地球。</span><span class="sxs-lookup"><span data-stu-id="09713-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="09713-174">它还提供了一种方法，用于根据全地球（无外部环）来表示多边形。</span><span class="sxs-lookup"><span data-stu-id="09713-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="09713-175">NTS 不支持这两种方法。</span><span class="sxs-lookup"><span data-stu-id="09713-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="09713-176">NTS 不支持基于 FullGlobe 和多边形。</span><span class="sxs-lookup"><span data-stu-id="09713-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="09713-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="09713-177">SQLite</span></span>

<span data-ttu-id="09713-178">下面是使用 SQLite 的一些其他信息。</span><span class="sxs-lookup"><span data-stu-id="09713-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="09713-179">安装 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="09713-179">Installing SpatiaLite</span></span>

<span data-ttu-id="09713-180">在 Windows 上，本机 mod_spatialite 库以 NuGet 包依赖关系的形式分发。</span><span class="sxs-lookup"><span data-stu-id="09713-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="09713-181">其他平台需要单独安装它。</span><span class="sxs-lookup"><span data-stu-id="09713-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="09713-182">通常使用软件程序包管理器完成此操作。</span><span class="sxs-lookup"><span data-stu-id="09713-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="09713-183">例如，可以在 Ubuntu 上使用 APT 和 MacOS 上的 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="09713-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="09713-184">配置 SRID</span><span class="sxs-lookup"><span data-stu-id="09713-184">Configuring SRID</span></span>

<span data-ttu-id="09713-185">在 SpatiaLite 中，列需要为每个列指定一个 SRID。</span><span class="sxs-lookup"><span data-stu-id="09713-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="09713-186">默认的 SRID 为`0`。</span><span class="sxs-lookup"><span data-stu-id="09713-186">The default SRID is `0`.</span></span> <span data-ttu-id="09713-187">使用 ForSqliteHasSrid 方法指定其他 SRID。</span><span class="sxs-lookup"><span data-stu-id="09713-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="09713-188">维度</span><span class="sxs-lookup"><span data-stu-id="09713-188">Dimension</span></span>

<span data-ttu-id="09713-189">类似于 SRID，列的维度（或坐标）也被指定为列的一部分。</span><span class="sxs-lookup"><span data-stu-id="09713-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="09713-190">默认坐标为 X 和 Y。使用 ForSqliteHasDimension 方法启用其他坐标（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="09713-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="09713-191">转换的操作</span><span class="sxs-lookup"><span data-stu-id="09713-191">Translated Operations</span></span>

<span data-ttu-id="09713-192">此表显示每个 EF Core 提供程序将哪些 NTS 成员转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="09713-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="09713-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="09713-193">NetTopologySuite</span></span> | <span data-ttu-id="09713-194">SQL Server （geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-194">SQL Server (geometry)</span></span> | <span data-ttu-id="09713-195">SQL Server （geography）</span><span class="sxs-lookup"><span data-stu-id="09713-195">SQL Server (geography)</span></span> | <span data-ttu-id="09713-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="09713-196">SQLite</span></span> | <span data-ttu-id="09713-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="09713-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="09713-198">Geometry</span><span class="sxs-lookup"><span data-stu-id="09713-198">Geometry.Area</span></span> | <span data-ttu-id="09713-199">✔</span><span class="sxs-lookup"><span data-stu-id="09713-199">✔</span></span> | <span data-ttu-id="09713-200">✔</span><span class="sxs-lookup"><span data-stu-id="09713-200">✔</span></span> | <span data-ttu-id="09713-201">✔</span><span class="sxs-lookup"><span data-stu-id="09713-201">✔</span></span> | <span data-ttu-id="09713-202">✔</span><span class="sxs-lookup"><span data-stu-id="09713-202">✔</span></span>
<span data-ttu-id="09713-203">AsBinary （）</span><span class="sxs-lookup"><span data-stu-id="09713-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="09713-204">✔</span><span class="sxs-lookup"><span data-stu-id="09713-204">✔</span></span> | <span data-ttu-id="09713-205">✔</span><span class="sxs-lookup"><span data-stu-id="09713-205">✔</span></span> | <span data-ttu-id="09713-206">✔</span><span class="sxs-lookup"><span data-stu-id="09713-206">✔</span></span> | <span data-ttu-id="09713-207">✔</span><span class="sxs-lookup"><span data-stu-id="09713-207">✔</span></span>
<span data-ttu-id="09713-208">AsText （）</span><span class="sxs-lookup"><span data-stu-id="09713-208">Geometry.AsText()</span></span> | <span data-ttu-id="09713-209">✔</span><span class="sxs-lookup"><span data-stu-id="09713-209">✔</span></span> | <span data-ttu-id="09713-210">✔</span><span class="sxs-lookup"><span data-stu-id="09713-210">✔</span></span> | <span data-ttu-id="09713-211">✔</span><span class="sxs-lookup"><span data-stu-id="09713-211">✔</span></span> | <span data-ttu-id="09713-212">✔</span><span class="sxs-lookup"><span data-stu-id="09713-212">✔</span></span>
<span data-ttu-id="09713-213">Geometry</span><span class="sxs-lookup"><span data-stu-id="09713-213">Geometry.Boundary</span></span> | <span data-ttu-id="09713-214">✔</span><span class="sxs-lookup"><span data-stu-id="09713-214">✔</span></span> | | <span data-ttu-id="09713-215">✔</span><span class="sxs-lookup"><span data-stu-id="09713-215">✔</span></span> | <span data-ttu-id="09713-216">✔</span><span class="sxs-lookup"><span data-stu-id="09713-216">✔</span></span>
<span data-ttu-id="09713-217">Geometry （双精度型）</span><span class="sxs-lookup"><span data-stu-id="09713-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="09713-218">✔</span><span class="sxs-lookup"><span data-stu-id="09713-218">✔</span></span> | <span data-ttu-id="09713-219">✔</span><span class="sxs-lookup"><span data-stu-id="09713-219">✔</span></span> | <span data-ttu-id="09713-220">✔</span><span class="sxs-lookup"><span data-stu-id="09713-220">✔</span></span> | <span data-ttu-id="09713-221">✔</span><span class="sxs-lookup"><span data-stu-id="09713-221">✔</span></span>
<span data-ttu-id="09713-222">Geometry （double，int）</span><span class="sxs-lookup"><span data-stu-id="09713-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="09713-223">✔</span><span class="sxs-lookup"><span data-stu-id="09713-223">✔</span></span>
<span data-ttu-id="09713-224">质心</span><span class="sxs-lookup"><span data-stu-id="09713-224">Geometry.Centroid</span></span> | <span data-ttu-id="09713-225">✔</span><span class="sxs-lookup"><span data-stu-id="09713-225">✔</span></span> | | <span data-ttu-id="09713-226">✔</span><span class="sxs-lookup"><span data-stu-id="09713-226">✔</span></span> | <span data-ttu-id="09713-227">✔</span><span class="sxs-lookup"><span data-stu-id="09713-227">✔</span></span>
<span data-ttu-id="09713-228">Geometry。 Contains （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-228">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="09713-229">✔</span><span class="sxs-lookup"><span data-stu-id="09713-229">✔</span></span> | <span data-ttu-id="09713-230">✔</span><span class="sxs-lookup"><span data-stu-id="09713-230">✔</span></span> | <span data-ttu-id="09713-231">✔</span><span class="sxs-lookup"><span data-stu-id="09713-231">✔</span></span> | <span data-ttu-id="09713-232">✔</span><span class="sxs-lookup"><span data-stu-id="09713-232">✔</span></span>
<span data-ttu-id="09713-233">ConvexHull （）</span><span class="sxs-lookup"><span data-stu-id="09713-233">Geometry.ConvexHull()</span></span> | <span data-ttu-id="09713-234">✔</span><span class="sxs-lookup"><span data-stu-id="09713-234">✔</span></span> | <span data-ttu-id="09713-235">✔</span><span class="sxs-lookup"><span data-stu-id="09713-235">✔</span></span> | <span data-ttu-id="09713-236">✔</span><span class="sxs-lookup"><span data-stu-id="09713-236">✔</span></span> | <span data-ttu-id="09713-237">✔</span><span class="sxs-lookup"><span data-stu-id="09713-237">✔</span></span>
<span data-ttu-id="09713-238">CoveredBy （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-238">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="09713-239">✔</span><span class="sxs-lookup"><span data-stu-id="09713-239">✔</span></span> | <span data-ttu-id="09713-240">✔</span><span class="sxs-lookup"><span data-stu-id="09713-240">✔</span></span>
<span data-ttu-id="09713-241">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-241">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="09713-242">✔</span><span class="sxs-lookup"><span data-stu-id="09713-242">✔</span></span> | <span data-ttu-id="09713-243">✔</span><span class="sxs-lookup"><span data-stu-id="09713-243">✔</span></span>
<span data-ttu-id="09713-244">几何。交叉（几何）</span><span class="sxs-lookup"><span data-stu-id="09713-244">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="09713-245">✔</span><span class="sxs-lookup"><span data-stu-id="09713-245">✔</span></span> | | <span data-ttu-id="09713-246">✔</span><span class="sxs-lookup"><span data-stu-id="09713-246">✔</span></span> | <span data-ttu-id="09713-247">✔</span><span class="sxs-lookup"><span data-stu-id="09713-247">✔</span></span>
<span data-ttu-id="09713-248">几何差（几何）</span><span class="sxs-lookup"><span data-stu-id="09713-248">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="09713-249">✔</span><span class="sxs-lookup"><span data-stu-id="09713-249">✔</span></span> | <span data-ttu-id="09713-250">✔</span><span class="sxs-lookup"><span data-stu-id="09713-250">✔</span></span> | <span data-ttu-id="09713-251">✔</span><span class="sxs-lookup"><span data-stu-id="09713-251">✔</span></span> | <span data-ttu-id="09713-252">✔</span><span class="sxs-lookup"><span data-stu-id="09713-252">✔</span></span>
<span data-ttu-id="09713-253">Geometry。维度</span><span class="sxs-lookup"><span data-stu-id="09713-253">Geometry.Dimension</span></span> | <span data-ttu-id="09713-254">✔</span><span class="sxs-lookup"><span data-stu-id="09713-254">✔</span></span> | <span data-ttu-id="09713-255">✔</span><span class="sxs-lookup"><span data-stu-id="09713-255">✔</span></span> | <span data-ttu-id="09713-256">✔</span><span class="sxs-lookup"><span data-stu-id="09713-256">✔</span></span> | <span data-ttu-id="09713-257">✔</span><span class="sxs-lookup"><span data-stu-id="09713-257">✔</span></span>
<span data-ttu-id="09713-258">不连续（Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-258">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="09713-259">✔</span><span class="sxs-lookup"><span data-stu-id="09713-259">✔</span></span> | <span data-ttu-id="09713-260">✔</span><span class="sxs-lookup"><span data-stu-id="09713-260">✔</span></span> | <span data-ttu-id="09713-261">✔</span><span class="sxs-lookup"><span data-stu-id="09713-261">✔</span></span> | <span data-ttu-id="09713-262">✔</span><span class="sxs-lookup"><span data-stu-id="09713-262">✔</span></span>
<span data-ttu-id="09713-263">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-263">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="09713-264">✔</span><span class="sxs-lookup"><span data-stu-id="09713-264">✔</span></span> | <span data-ttu-id="09713-265">✔</span><span class="sxs-lookup"><span data-stu-id="09713-265">✔</span></span> | <span data-ttu-id="09713-266">✔</span><span class="sxs-lookup"><span data-stu-id="09713-266">✔</span></span> | <span data-ttu-id="09713-267">✔</span><span class="sxs-lookup"><span data-stu-id="09713-267">✔</span></span>
<span data-ttu-id="09713-268">Geometry 信封</span><span class="sxs-lookup"><span data-stu-id="09713-268">Geometry.Envelope</span></span> | <span data-ttu-id="09713-269">✔</span><span class="sxs-lookup"><span data-stu-id="09713-269">✔</span></span> | | <span data-ttu-id="09713-270">✔</span><span class="sxs-lookup"><span data-stu-id="09713-270">✔</span></span> | <span data-ttu-id="09713-271">✔</span><span class="sxs-lookup"><span data-stu-id="09713-271">✔</span></span>
<span data-ttu-id="09713-272">EqualsExact （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-272">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="09713-273">✔</span><span class="sxs-lookup"><span data-stu-id="09713-273">✔</span></span>
<span data-ttu-id="09713-274">EqualsTopologically （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-274">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="09713-275">✔</span><span class="sxs-lookup"><span data-stu-id="09713-275">✔</span></span> | <span data-ttu-id="09713-276">✔</span><span class="sxs-lookup"><span data-stu-id="09713-276">✔</span></span> | <span data-ttu-id="09713-277">✔</span><span class="sxs-lookup"><span data-stu-id="09713-277">✔</span></span> | <span data-ttu-id="09713-278">✔</span><span class="sxs-lookup"><span data-stu-id="09713-278">✔</span></span>
<span data-ttu-id="09713-279">GeometryType</span><span class="sxs-lookup"><span data-stu-id="09713-279">Geometry.GeometryType</span></span> | <span data-ttu-id="09713-280">✔</span><span class="sxs-lookup"><span data-stu-id="09713-280">✔</span></span> | <span data-ttu-id="09713-281">✔</span><span class="sxs-lookup"><span data-stu-id="09713-281">✔</span></span> | <span data-ttu-id="09713-282">✔</span><span class="sxs-lookup"><span data-stu-id="09713-282">✔</span></span> | <span data-ttu-id="09713-283">✔</span><span class="sxs-lookup"><span data-stu-id="09713-283">✔</span></span>
<span data-ttu-id="09713-284">GetGeometryN （int）</span><span class="sxs-lookup"><span data-stu-id="09713-284">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="09713-285">✔</span><span class="sxs-lookup"><span data-stu-id="09713-285">✔</span></span> | | <span data-ttu-id="09713-286">✔</span><span class="sxs-lookup"><span data-stu-id="09713-286">✔</span></span> | <span data-ttu-id="09713-287">✔</span><span class="sxs-lookup"><span data-stu-id="09713-287">✔</span></span>
<span data-ttu-id="09713-288">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="09713-288">Geometry.InteriorPoint</span></span> | <span data-ttu-id="09713-289">✔</span><span class="sxs-lookup"><span data-stu-id="09713-289">✔</span></span> | | <span data-ttu-id="09713-290">✔</span><span class="sxs-lookup"><span data-stu-id="09713-290">✔</span></span>
<span data-ttu-id="09713-291">几何交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-291">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="09713-292">✔</span><span class="sxs-lookup"><span data-stu-id="09713-292">✔</span></span> | <span data-ttu-id="09713-293">✔</span><span class="sxs-lookup"><span data-stu-id="09713-293">✔</span></span> | <span data-ttu-id="09713-294">✔</span><span class="sxs-lookup"><span data-stu-id="09713-294">✔</span></span> | <span data-ttu-id="09713-295">✔</span><span class="sxs-lookup"><span data-stu-id="09713-295">✔</span></span>
<span data-ttu-id="09713-296">几何和交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-296">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="09713-297">✔</span><span class="sxs-lookup"><span data-stu-id="09713-297">✔</span></span> | <span data-ttu-id="09713-298">✔</span><span class="sxs-lookup"><span data-stu-id="09713-298">✔</span></span> | <span data-ttu-id="09713-299">✔</span><span class="sxs-lookup"><span data-stu-id="09713-299">✔</span></span> | <span data-ttu-id="09713-300">✔</span><span class="sxs-lookup"><span data-stu-id="09713-300">✔</span></span>
<span data-ttu-id="09713-301">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="09713-301">Geometry.IsEmpty</span></span> | <span data-ttu-id="09713-302">✔</span><span class="sxs-lookup"><span data-stu-id="09713-302">✔</span></span> | <span data-ttu-id="09713-303">✔</span><span class="sxs-lookup"><span data-stu-id="09713-303">✔</span></span> | <span data-ttu-id="09713-304">✔</span><span class="sxs-lookup"><span data-stu-id="09713-304">✔</span></span> | <span data-ttu-id="09713-305">✔</span><span class="sxs-lookup"><span data-stu-id="09713-305">✔</span></span>
<span data-ttu-id="09713-306">IsSimple</span><span class="sxs-lookup"><span data-stu-id="09713-306">Geometry.IsSimple</span></span> | <span data-ttu-id="09713-307">✔</span><span class="sxs-lookup"><span data-stu-id="09713-307">✔</span></span> | | <span data-ttu-id="09713-308">✔</span><span class="sxs-lookup"><span data-stu-id="09713-308">✔</span></span> | <span data-ttu-id="09713-309">✔</span><span class="sxs-lookup"><span data-stu-id="09713-309">✔</span></span>
<span data-ttu-id="09713-310">Geometry</span><span class="sxs-lookup"><span data-stu-id="09713-310">Geometry.IsValid</span></span> | <span data-ttu-id="09713-311">✔</span><span class="sxs-lookup"><span data-stu-id="09713-311">✔</span></span> | <span data-ttu-id="09713-312">✔</span><span class="sxs-lookup"><span data-stu-id="09713-312">✔</span></span> | <span data-ttu-id="09713-313">✔</span><span class="sxs-lookup"><span data-stu-id="09713-313">✔</span></span> | <span data-ttu-id="09713-314">✔</span><span class="sxs-lookup"><span data-stu-id="09713-314">✔</span></span>
<span data-ttu-id="09713-315">IsWithinDistance （Geometry，double）</span><span class="sxs-lookup"><span data-stu-id="09713-315">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="09713-316">✔</span><span class="sxs-lookup"><span data-stu-id="09713-316">✔</span></span> | | <span data-ttu-id="09713-317">✔</span><span class="sxs-lookup"><span data-stu-id="09713-317">✔</span></span>
<span data-ttu-id="09713-318">Geometry。长度</span><span class="sxs-lookup"><span data-stu-id="09713-318">Geometry.Length</span></span> | <span data-ttu-id="09713-319">✔</span><span class="sxs-lookup"><span data-stu-id="09713-319">✔</span></span> | <span data-ttu-id="09713-320">✔</span><span class="sxs-lookup"><span data-stu-id="09713-320">✔</span></span> | <span data-ttu-id="09713-321">✔</span><span class="sxs-lookup"><span data-stu-id="09713-321">✔</span></span> | <span data-ttu-id="09713-322">✔</span><span class="sxs-lookup"><span data-stu-id="09713-322">✔</span></span>
<span data-ttu-id="09713-323">NumGeometries</span><span class="sxs-lookup"><span data-stu-id="09713-323">Geometry.NumGeometries</span></span> | <span data-ttu-id="09713-324">✔</span><span class="sxs-lookup"><span data-stu-id="09713-324">✔</span></span> | <span data-ttu-id="09713-325">✔</span><span class="sxs-lookup"><span data-stu-id="09713-325">✔</span></span> | <span data-ttu-id="09713-326">✔</span><span class="sxs-lookup"><span data-stu-id="09713-326">✔</span></span> | <span data-ttu-id="09713-327">✔</span><span class="sxs-lookup"><span data-stu-id="09713-327">✔</span></span>
<span data-ttu-id="09713-328">X.numpoints</span><span class="sxs-lookup"><span data-stu-id="09713-328">Geometry.NumPoints</span></span> | <span data-ttu-id="09713-329">✔</span><span class="sxs-lookup"><span data-stu-id="09713-329">✔</span></span> | <span data-ttu-id="09713-330">✔</span><span class="sxs-lookup"><span data-stu-id="09713-330">✔</span></span> | <span data-ttu-id="09713-331">✔</span><span class="sxs-lookup"><span data-stu-id="09713-331">✔</span></span> | <span data-ttu-id="09713-332">✔</span><span class="sxs-lookup"><span data-stu-id="09713-332">✔</span></span>
<span data-ttu-id="09713-333">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="09713-333">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="09713-334">✔</span><span class="sxs-lookup"><span data-stu-id="09713-334">✔</span></span> | <span data-ttu-id="09713-335">✔</span><span class="sxs-lookup"><span data-stu-id="09713-335">✔</span></span> | <span data-ttu-id="09713-336">✔</span><span class="sxs-lookup"><span data-stu-id="09713-336">✔</span></span>
<span data-ttu-id="09713-337">Geometry 重叠（Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-337">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="09713-338">✔</span><span class="sxs-lookup"><span data-stu-id="09713-338">✔</span></span> | <span data-ttu-id="09713-339">✔</span><span class="sxs-lookup"><span data-stu-id="09713-339">✔</span></span> | <span data-ttu-id="09713-340">✔</span><span class="sxs-lookup"><span data-stu-id="09713-340">✔</span></span> | <span data-ttu-id="09713-341">✔</span><span class="sxs-lookup"><span data-stu-id="09713-341">✔</span></span>
<span data-ttu-id="09713-342">PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="09713-342">Geometry.PointOnSurface</span></span> | <span data-ttu-id="09713-343">✔</span><span class="sxs-lookup"><span data-stu-id="09713-343">✔</span></span> | | <span data-ttu-id="09713-344">✔</span><span class="sxs-lookup"><span data-stu-id="09713-344">✔</span></span> | <span data-ttu-id="09713-345">✔</span><span class="sxs-lookup"><span data-stu-id="09713-345">✔</span></span>
<span data-ttu-id="09713-346">Geometry （Geometry，string）</span><span class="sxs-lookup"><span data-stu-id="09713-346">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="09713-347">✔</span><span class="sxs-lookup"><span data-stu-id="09713-347">✔</span></span> | | <span data-ttu-id="09713-348">✔</span><span class="sxs-lookup"><span data-stu-id="09713-348">✔</span></span> | <span data-ttu-id="09713-349">✔</span><span class="sxs-lookup"><span data-stu-id="09713-349">✔</span></span>
<span data-ttu-id="09713-350">Geometry 反向（）</span><span class="sxs-lookup"><span data-stu-id="09713-350">Geometry.Reverse()</span></span> | | | <span data-ttu-id="09713-351">✔</span><span class="sxs-lookup"><span data-stu-id="09713-351">✔</span></span> | <span data-ttu-id="09713-352">✔</span><span class="sxs-lookup"><span data-stu-id="09713-352">✔</span></span>
<span data-ttu-id="09713-353">SRID</span><span class="sxs-lookup"><span data-stu-id="09713-353">Geometry.SRID</span></span> | <span data-ttu-id="09713-354">✔</span><span class="sxs-lookup"><span data-stu-id="09713-354">✔</span></span> | <span data-ttu-id="09713-355">✔</span><span class="sxs-lookup"><span data-stu-id="09713-355">✔</span></span> | <span data-ttu-id="09713-356">✔</span><span class="sxs-lookup"><span data-stu-id="09713-356">✔</span></span> | <span data-ttu-id="09713-357">✔</span><span class="sxs-lookup"><span data-stu-id="09713-357">✔</span></span>
<span data-ttu-id="09713-358">SymmetricDifference （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-358">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="09713-359">✔</span><span class="sxs-lookup"><span data-stu-id="09713-359">✔</span></span> | <span data-ttu-id="09713-360">✔</span><span class="sxs-lookup"><span data-stu-id="09713-360">✔</span></span> | <span data-ttu-id="09713-361">✔</span><span class="sxs-lookup"><span data-stu-id="09713-361">✔</span></span> | <span data-ttu-id="09713-362">✔</span><span class="sxs-lookup"><span data-stu-id="09713-362">✔</span></span>
<span data-ttu-id="09713-363">ToBinary （）</span><span class="sxs-lookup"><span data-stu-id="09713-363">Geometry.ToBinary()</span></span> | <span data-ttu-id="09713-364">✔</span><span class="sxs-lookup"><span data-stu-id="09713-364">✔</span></span> | <span data-ttu-id="09713-365">✔</span><span class="sxs-lookup"><span data-stu-id="09713-365">✔</span></span> | <span data-ttu-id="09713-366">✔</span><span class="sxs-lookup"><span data-stu-id="09713-366">✔</span></span> | <span data-ttu-id="09713-367">✔</span><span class="sxs-lookup"><span data-stu-id="09713-367">✔</span></span>
<span data-ttu-id="09713-368">ToText （）</span><span class="sxs-lookup"><span data-stu-id="09713-368">Geometry.ToText()</span></span> | <span data-ttu-id="09713-369">✔</span><span class="sxs-lookup"><span data-stu-id="09713-369">✔</span></span> | <span data-ttu-id="09713-370">✔</span><span class="sxs-lookup"><span data-stu-id="09713-370">✔</span></span> | <span data-ttu-id="09713-371">✔</span><span class="sxs-lookup"><span data-stu-id="09713-371">✔</span></span> | <span data-ttu-id="09713-372">✔</span><span class="sxs-lookup"><span data-stu-id="09713-372">✔</span></span>
<span data-ttu-id="09713-373">几何图形（几何）</span><span class="sxs-lookup"><span data-stu-id="09713-373">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="09713-374">✔</span><span class="sxs-lookup"><span data-stu-id="09713-374">✔</span></span> | | <span data-ttu-id="09713-375">✔</span><span class="sxs-lookup"><span data-stu-id="09713-375">✔</span></span> | <span data-ttu-id="09713-376">✔</span><span class="sxs-lookup"><span data-stu-id="09713-376">✔</span></span>
<span data-ttu-id="09713-377">Geometry （）</span><span class="sxs-lookup"><span data-stu-id="09713-377">Geometry.Union()</span></span> | | | <span data-ttu-id="09713-378">✔</span><span class="sxs-lookup"><span data-stu-id="09713-378">✔</span></span>
<span data-ttu-id="09713-379">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-379">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="09713-380">✔</span><span class="sxs-lookup"><span data-stu-id="09713-380">✔</span></span> | <span data-ttu-id="09713-381">✔</span><span class="sxs-lookup"><span data-stu-id="09713-381">✔</span></span> | <span data-ttu-id="09713-382">✔</span><span class="sxs-lookup"><span data-stu-id="09713-382">✔</span></span> | <span data-ttu-id="09713-383">✔</span><span class="sxs-lookup"><span data-stu-id="09713-383">✔</span></span>
<span data-ttu-id="09713-384">几何图形。内（Geometry）</span><span class="sxs-lookup"><span data-stu-id="09713-384">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="09713-385">✔</span><span class="sxs-lookup"><span data-stu-id="09713-385">✔</span></span> | <span data-ttu-id="09713-386">✔</span><span class="sxs-lookup"><span data-stu-id="09713-386">✔</span></span> | <span data-ttu-id="09713-387">✔</span><span class="sxs-lookup"><span data-stu-id="09713-387">✔</span></span> | <span data-ttu-id="09713-388">✔</span><span class="sxs-lookup"><span data-stu-id="09713-388">✔</span></span>
<span data-ttu-id="09713-389">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="09713-389">GeometryCollection.Count</span></span> | <span data-ttu-id="09713-390">✔</span><span class="sxs-lookup"><span data-stu-id="09713-390">✔</span></span> | <span data-ttu-id="09713-391">✔</span><span class="sxs-lookup"><span data-stu-id="09713-391">✔</span></span> | <span data-ttu-id="09713-392">✔</span><span class="sxs-lookup"><span data-stu-id="09713-392">✔</span></span> | <span data-ttu-id="09713-393">✔</span><span class="sxs-lookup"><span data-stu-id="09713-393">✔</span></span>
<span data-ttu-id="09713-394">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="09713-394">GeometryCollection[int]</span></span> | <span data-ttu-id="09713-395">✔</span><span class="sxs-lookup"><span data-stu-id="09713-395">✔</span></span> | <span data-ttu-id="09713-396">✔</span><span class="sxs-lookup"><span data-stu-id="09713-396">✔</span></span> | <span data-ttu-id="09713-397">✔</span><span class="sxs-lookup"><span data-stu-id="09713-397">✔</span></span> | <span data-ttu-id="09713-398">✔</span><span class="sxs-lookup"><span data-stu-id="09713-398">✔</span></span>
<span data-ttu-id="09713-399">LineString</span><span class="sxs-lookup"><span data-stu-id="09713-399">LineString.Count</span></span> | <span data-ttu-id="09713-400">✔</span><span class="sxs-lookup"><span data-stu-id="09713-400">✔</span></span> | <span data-ttu-id="09713-401">✔</span><span class="sxs-lookup"><span data-stu-id="09713-401">✔</span></span> | <span data-ttu-id="09713-402">✔</span><span class="sxs-lookup"><span data-stu-id="09713-402">✔</span></span> | <span data-ttu-id="09713-403">✔</span><span class="sxs-lookup"><span data-stu-id="09713-403">✔</span></span>
<span data-ttu-id="09713-404">LineString 终结点</span><span class="sxs-lookup"><span data-stu-id="09713-404">LineString.EndPoint</span></span> | <span data-ttu-id="09713-405">✔</span><span class="sxs-lookup"><span data-stu-id="09713-405">✔</span></span> | <span data-ttu-id="09713-406">✔</span><span class="sxs-lookup"><span data-stu-id="09713-406">✔</span></span> | <span data-ttu-id="09713-407">✔</span><span class="sxs-lookup"><span data-stu-id="09713-407">✔</span></span> | <span data-ttu-id="09713-408">✔</span><span class="sxs-lookup"><span data-stu-id="09713-408">✔</span></span>
<span data-ttu-id="09713-409">LineString. GetPointN （int）</span><span class="sxs-lookup"><span data-stu-id="09713-409">LineString.GetPointN(int)</span></span> | <span data-ttu-id="09713-410">✔</span><span class="sxs-lookup"><span data-stu-id="09713-410">✔</span></span> | <span data-ttu-id="09713-411">✔</span><span class="sxs-lookup"><span data-stu-id="09713-411">✔</span></span> | <span data-ttu-id="09713-412">✔</span><span class="sxs-lookup"><span data-stu-id="09713-412">✔</span></span> | <span data-ttu-id="09713-413">✔</span><span class="sxs-lookup"><span data-stu-id="09713-413">✔</span></span>
<span data-ttu-id="09713-414">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="09713-414">LineString.IsClosed</span></span> | <span data-ttu-id="09713-415">✔</span><span class="sxs-lookup"><span data-stu-id="09713-415">✔</span></span> | <span data-ttu-id="09713-416">✔</span><span class="sxs-lookup"><span data-stu-id="09713-416">✔</span></span> | <span data-ttu-id="09713-417">✔</span><span class="sxs-lookup"><span data-stu-id="09713-417">✔</span></span> | <span data-ttu-id="09713-418">✔</span><span class="sxs-lookup"><span data-stu-id="09713-418">✔</span></span>
<span data-ttu-id="09713-419">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="09713-419">LineString.IsRing</span></span> | <span data-ttu-id="09713-420">✔</span><span class="sxs-lookup"><span data-stu-id="09713-420">✔</span></span> | | <span data-ttu-id="09713-421">✔</span><span class="sxs-lookup"><span data-stu-id="09713-421">✔</span></span> | <span data-ttu-id="09713-422">✔</span><span class="sxs-lookup"><span data-stu-id="09713-422">✔</span></span>
<span data-ttu-id="09713-423">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="09713-423">LineString.StartPoint</span></span> | <span data-ttu-id="09713-424">✔</span><span class="sxs-lookup"><span data-stu-id="09713-424">✔</span></span> | <span data-ttu-id="09713-425">✔</span><span class="sxs-lookup"><span data-stu-id="09713-425">✔</span></span> | <span data-ttu-id="09713-426">✔</span><span class="sxs-lookup"><span data-stu-id="09713-426">✔</span></span> | <span data-ttu-id="09713-427">✔</span><span class="sxs-lookup"><span data-stu-id="09713-427">✔</span></span>
<span data-ttu-id="09713-428">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="09713-428">MultiLineString.IsClosed</span></span> | <span data-ttu-id="09713-429">✔</span><span class="sxs-lookup"><span data-stu-id="09713-429">✔</span></span> | <span data-ttu-id="09713-430">✔</span><span class="sxs-lookup"><span data-stu-id="09713-430">✔</span></span> | <span data-ttu-id="09713-431">✔</span><span class="sxs-lookup"><span data-stu-id="09713-431">✔</span></span> | <span data-ttu-id="09713-432">✔</span><span class="sxs-lookup"><span data-stu-id="09713-432">✔</span></span>
<span data-ttu-id="09713-433">点 M</span><span class="sxs-lookup"><span data-stu-id="09713-433">Point.M</span></span> | <span data-ttu-id="09713-434">✔</span><span class="sxs-lookup"><span data-stu-id="09713-434">✔</span></span> | <span data-ttu-id="09713-435">✔</span><span class="sxs-lookup"><span data-stu-id="09713-435">✔</span></span> | <span data-ttu-id="09713-436">✔</span><span class="sxs-lookup"><span data-stu-id="09713-436">✔</span></span> | <span data-ttu-id="09713-437">✔</span><span class="sxs-lookup"><span data-stu-id="09713-437">✔</span></span>
<span data-ttu-id="09713-438">点 X</span><span class="sxs-lookup"><span data-stu-id="09713-438">Point.X</span></span> | <span data-ttu-id="09713-439">✔</span><span class="sxs-lookup"><span data-stu-id="09713-439">✔</span></span> | <span data-ttu-id="09713-440">✔</span><span class="sxs-lookup"><span data-stu-id="09713-440">✔</span></span> | <span data-ttu-id="09713-441">✔</span><span class="sxs-lookup"><span data-stu-id="09713-441">✔</span></span> | <span data-ttu-id="09713-442">✔</span><span class="sxs-lookup"><span data-stu-id="09713-442">✔</span></span>
<span data-ttu-id="09713-443">Point。 Y</span><span class="sxs-lookup"><span data-stu-id="09713-443">Point.Y</span></span> | <span data-ttu-id="09713-444">✔</span><span class="sxs-lookup"><span data-stu-id="09713-444">✔</span></span> | <span data-ttu-id="09713-445">✔</span><span class="sxs-lookup"><span data-stu-id="09713-445">✔</span></span> | <span data-ttu-id="09713-446">✔</span><span class="sxs-lookup"><span data-stu-id="09713-446">✔</span></span> | <span data-ttu-id="09713-447">✔</span><span class="sxs-lookup"><span data-stu-id="09713-447">✔</span></span>
<span data-ttu-id="09713-448">点 Z</span><span class="sxs-lookup"><span data-stu-id="09713-448">Point.Z</span></span> | <span data-ttu-id="09713-449">✔</span><span class="sxs-lookup"><span data-stu-id="09713-449">✔</span></span> | <span data-ttu-id="09713-450">✔</span><span class="sxs-lookup"><span data-stu-id="09713-450">✔</span></span> | <span data-ttu-id="09713-451">✔</span><span class="sxs-lookup"><span data-stu-id="09713-451">✔</span></span> | <span data-ttu-id="09713-452">✔</span><span class="sxs-lookup"><span data-stu-id="09713-452">✔</span></span>
<span data-ttu-id="09713-453">多边形。 ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="09713-453">Polygon.ExteriorRing</span></span> | <span data-ttu-id="09713-454">✔</span><span class="sxs-lookup"><span data-stu-id="09713-454">✔</span></span> | <span data-ttu-id="09713-455">✔</span><span class="sxs-lookup"><span data-stu-id="09713-455">✔</span></span> | <span data-ttu-id="09713-456">✔</span><span class="sxs-lookup"><span data-stu-id="09713-456">✔</span></span> | <span data-ttu-id="09713-457">✔</span><span class="sxs-lookup"><span data-stu-id="09713-457">✔</span></span>
<span data-ttu-id="09713-458">GetInteriorRingN （int）</span><span class="sxs-lookup"><span data-stu-id="09713-458">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="09713-459">✔</span><span class="sxs-lookup"><span data-stu-id="09713-459">✔</span></span> | <span data-ttu-id="09713-460">✔</span><span class="sxs-lookup"><span data-stu-id="09713-460">✔</span></span> | <span data-ttu-id="09713-461">✔</span><span class="sxs-lookup"><span data-stu-id="09713-461">✔</span></span> | <span data-ttu-id="09713-462">✔</span><span class="sxs-lookup"><span data-stu-id="09713-462">✔</span></span>
<span data-ttu-id="09713-463">多边形。 NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="09713-463">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="09713-464">✔</span><span class="sxs-lookup"><span data-stu-id="09713-464">✔</span></span> | <span data-ttu-id="09713-465">✔</span><span class="sxs-lookup"><span data-stu-id="09713-465">✔</span></span> | <span data-ttu-id="09713-466">✔</span><span class="sxs-lookup"><span data-stu-id="09713-466">✔</span></span> | <span data-ttu-id="09713-467">✔</span><span class="sxs-lookup"><span data-stu-id="09713-467">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09713-468">其他资源</span><span class="sxs-lookup"><span data-stu-id="09713-468">Additional resources</span></span>

* [<span data-ttu-id="09713-469">SQL Server 中的空间数据</span><span class="sxs-lookup"><span data-stu-id="09713-469">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="09713-470">SpatiaLite 主页</span><span class="sxs-lookup"><span data-stu-id="09713-470">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="09713-471">Npgsql 空间文档</span><span class="sxs-lookup"><span data-stu-id="09713-471">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="09713-472">PostGIS 文档</span><span class="sxs-lookup"><span data-stu-id="09713-472">PostGIS Documentation</span></span>](http://postgis.net/documentation/)
