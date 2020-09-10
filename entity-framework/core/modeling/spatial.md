---
title: 空间数据-EF Core
description: 在 Entity Framework Core 模型中使用空间数据
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 42386fb132f135d725a2068d91dc49c7f613e277
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616695"
---
# <a name="spatial-data"></a><span data-ttu-id="8e44e-103">空间数据</span><span class="sxs-lookup"><span data-stu-id="8e44e-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="8e44e-104">此功能是在 EF Core 2.2 中添加的。</span><span class="sxs-lookup"><span data-stu-id="8e44e-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="8e44e-105">空间数据表示对象的物理位置和形状。</span><span class="sxs-lookup"><span data-stu-id="8e44e-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="8e44e-106">许多数据库提供对此类数据的支持，以便能够与其他数据一起进行索引和查询。</span><span class="sxs-lookup"><span data-stu-id="8e44e-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="8e44e-107">常见方案包括从位置在给定距离内查询对象，或选择其边框包含给定位置的对象。</span><span class="sxs-lookup"><span data-stu-id="8e44e-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="8e44e-108">EF Core 支持使用 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) 空间库映射到空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="8e44e-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="8e44e-109">安装</span><span class="sxs-lookup"><span data-stu-id="8e44e-109">Installing</span></span>

<span data-ttu-id="8e44e-110">若要在 EF Core 中使用空间数据，需要安装相应的支持 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8e44e-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="8e44e-111">需要安装哪个包取决于所使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="8e44e-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="8e44e-112">EF Core 提供程序</span><span class="sxs-lookup"><span data-stu-id="8e44e-112">EF Core Provider</span></span>                        | <span data-ttu-id="8e44e-113">空间 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="8e44e-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="8e44e-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="8e44e-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="8e44e-115">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8e44e-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="8e44e-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="8e44e-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="8e44e-117">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8e44e-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="8e44e-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="8e44e-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="8e44e-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8e44e-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="8e44e-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="8e44e-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="8e44e-121">Npgsql. Microsoft.entityframeworkcore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8e44e-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="8e44e-122">反向工程</span><span class="sxs-lookup"><span data-stu-id="8e44e-122">Reverse engineering</span></span>

<span data-ttu-id="8e44e-123">空间 NuGet 包还启用具有空间属性的 [反向工程](xref:core/managing-schemas/scaffolding) 模型，但需要在运行或 ***之前*** 安装包 `Scaffold-DbContext` `dotnet ef dbcontext scaffold` 。</span><span class="sxs-lookup"><span data-stu-id="8e44e-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="8e44e-124">否则，你将收到有关找不到列的类型映射的警告，将跳过这些列。</span><span class="sxs-lookup"><span data-stu-id="8e44e-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="8e44e-125">NetTopologySuite (NTS) </span><span class="sxs-lookup"><span data-stu-id="8e44e-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="8e44e-126">NetTopologySuite 是用于 .NET 的空间库。</span><span class="sxs-lookup"><span data-stu-id="8e44e-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="8e44e-127">EF Core 使用模型中的 NTS 类型启用映射到数据库中的空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="8e44e-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="8e44e-128">若要通过 NTS 启用到空间类型的映射，请在提供程序的 DbContext 选项生成器上调用 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="8e44e-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="8e44e-129">例如，对于 SQL Server，你应将其称为。</span><span class="sxs-lookup"><span data-stu-id="8e44e-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="8e44e-130">有几种空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="8e44e-130">There are several spatial data types.</span></span> <span data-ttu-id="8e44e-131">使用哪种类型取决于您想要允许的形状的类型。</span><span class="sxs-lookup"><span data-stu-id="8e44e-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="8e44e-132">下面是可用于模型中的属性的 NTS 类型的层次结构。</span><span class="sxs-lookup"><span data-stu-id="8e44e-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="8e44e-133">它们位于 `NetTopologySuite.Geometries` 命名空间内。</span><span class="sxs-lookup"><span data-stu-id="8e44e-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="8e44e-134">几何结构</span><span class="sxs-lookup"><span data-stu-id="8e44e-134">Geometry</span></span>
  * <span data-ttu-id="8e44e-135">点</span><span class="sxs-lookup"><span data-stu-id="8e44e-135">Point</span></span>
  * <span data-ttu-id="8e44e-136">LineString</span><span class="sxs-lookup"><span data-stu-id="8e44e-136">LineString</span></span>
  * <span data-ttu-id="8e44e-137">Polygon</span><span class="sxs-lookup"><span data-stu-id="8e44e-137">Polygon</span></span>
  * <span data-ttu-id="8e44e-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="8e44e-138">GeometryCollection</span></span>
    * <span data-ttu-id="8e44e-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="8e44e-139">MultiPoint</span></span>
    * <span data-ttu-id="8e44e-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="8e44e-140">MultiLineString</span></span>
    * <span data-ttu-id="8e44e-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="8e44e-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="8e44e-142">NTS 不支持 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="8e44e-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="8e44e-143">使用基本几何图形类型允许属性指定任意类型的形状。</span><span class="sxs-lookup"><span data-stu-id="8e44e-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="8e44e-144">以下实体类可用于映射到 [广角导入示例数据库](https://go.microsoft.com/fwlink/?LinkID=800630)中的表。</span><span class="sxs-lookup"><span data-stu-id="8e44e-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="8e44e-145">创建值</span><span class="sxs-lookup"><span data-stu-id="8e44e-145">Creating values</span></span>

<span data-ttu-id="8e44e-146">您可以使用构造函数来创建 geometry 对象;但是，NTS 建议改为使用几何工厂。</span><span class="sxs-lookup"><span data-stu-id="8e44e-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="8e44e-147">这使你可以指定默认的 SRID (坐标使用的空间引用系统) 并使你能够控制更高级的功能，如) 计算期间使用的精度模型 (，并且坐标序列 (确定哪些坐标维度和度量值可用。</span><span class="sxs-lookup"><span data-stu-id="8e44e-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="8e44e-148">4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。</span><span class="sxs-lookup"><span data-stu-id="8e44e-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="8e44e-149">经度和纬度</span><span class="sxs-lookup"><span data-stu-id="8e44e-149">Longitude and Latitude</span></span>

<span data-ttu-id="8e44e-150">NTS 中的坐标采用 X 和 Y 值。</span><span class="sxs-lookup"><span data-stu-id="8e44e-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="8e44e-151">若要表示经度和纬度，请将 X 用于经度，将 Y 用于纬度。</span><span class="sxs-lookup"><span data-stu-id="8e44e-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="8e44e-152">请注意，这**backwards**是从 `latitude, longitude` 通常会看到这些值的格式反向进行的。</span><span class="sxs-lookup"><span data-stu-id="8e44e-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="8e44e-153">在客户端操作过程中忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="8e44e-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="8e44e-154">NTS 在操作过程中忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="8e44e-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="8e44e-155">它假定为平面坐标系统。</span><span class="sxs-lookup"><span data-stu-id="8e44e-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="8e44e-156">这意味着，如果在经度和纬度方面指定了坐标，则某些客户端计算的值（例如，距离、长度和区域）将为度数，而不是计量。</span><span class="sxs-lookup"><span data-stu-id="8e44e-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="8e44e-157">若要获得更有意义的值，首先需要使用库（如 [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) ）在计算这些值之前投影到另一个坐标系统的坐标。</span><span class="sxs-lookup"><span data-stu-id="8e44e-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="8e44e-158">如果通过 EF Core 通过 SQL 对操作进行服务器计算，则该结果的单元将由数据库确定。</span><span class="sxs-lookup"><span data-stu-id="8e44e-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="8e44e-159">下面是一个示例，说明如何使用 ProjNet4GeoAPI 来计算两个城市之间的距离。</span><span class="sxs-lookup"><span data-stu-id="8e44e-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="8e44e-160">查询数据</span><span class="sxs-lookup"><span data-stu-id="8e44e-160">Querying Data</span></span>

<span data-ttu-id="8e44e-161">在 LINQ 中，可用作数据库函数的 NTS 方法和属性将转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="8e44e-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="8e44e-162">例如，在以下查询中转换距离和包含方法。</span><span class="sxs-lookup"><span data-stu-id="8e44e-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="8e44e-163">本文末尾的表格显示了不同 EF Core 提供商支持哪些成员。</span><span class="sxs-lookup"><span data-stu-id="8e44e-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="8e44e-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8e44e-164">SQL Server</span></span>

<span data-ttu-id="8e44e-165">如果你正在使用 SQL Server，你还应该注意一些其他问题。</span><span class="sxs-lookup"><span data-stu-id="8e44e-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="8e44e-166">地理或几何图形</span><span class="sxs-lookup"><span data-stu-id="8e44e-166">Geography or geometry</span></span>

<span data-ttu-id="8e44e-167">默认情况下，空间属性映射到 `geography` SQL Server 中的列。</span><span class="sxs-lookup"><span data-stu-id="8e44e-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="8e44e-168">若要使用 `geometry` ，请在模型中 [配置列类型](xref:core/modeling/entity-properties#column-data-types) 。</span><span class="sxs-lookup"><span data-stu-id="8e44e-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="8e44e-169">地理多边形环</span><span class="sxs-lookup"><span data-stu-id="8e44e-169">Geography polygon rings</span></span>

<span data-ttu-id="8e44e-170">当使用 `geography` 列类型时，SQL Server 对外环 (或 shell) 和内部环 (或孔) 施加附加要求。</span><span class="sxs-lookup"><span data-stu-id="8e44e-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="8e44e-171">外部环必须逆时针旋转，并顺时针旋转内部环。</span><span class="sxs-lookup"><span data-stu-id="8e44e-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="8e44e-172">NTS 在将值发送到数据库之前对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="8e44e-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="8e44e-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="8e44e-173">FullGlobe</span></span>

<span data-ttu-id="8e44e-174">在使用列类型时，SQL Server 具有非标准几何类型来表示全地球 `geography` 。</span><span class="sxs-lookup"><span data-stu-id="8e44e-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="8e44e-175">它还可以基于全地球 (来表示多边形，而无需外环) 。</span><span class="sxs-lookup"><span data-stu-id="8e44e-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="8e44e-176">NTS 不支持这两种方法。</span><span class="sxs-lookup"><span data-stu-id="8e44e-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="8e44e-177">NTS 不支持基于 FullGlobe 和多边形。</span><span class="sxs-lookup"><span data-stu-id="8e44e-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="8e44e-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="8e44e-178">SQLite</span></span>

<span data-ttu-id="8e44e-179">下面是使用 SQLite 的一些其他信息。</span><span class="sxs-lookup"><span data-stu-id="8e44e-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="8e44e-180">安装 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="8e44e-180">Installing SpatiaLite</span></span>

<span data-ttu-id="8e44e-181">在 Windows 上，本机 mod_spatialite 库以 NuGet 包的依赖项的形式分发。</span><span class="sxs-lookup"><span data-stu-id="8e44e-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="8e44e-182">其他平台需要单独安装它。</span><span class="sxs-lookup"><span data-stu-id="8e44e-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="8e44e-183">通常使用软件程序包管理器完成此操作。</span><span class="sxs-lookup"><span data-stu-id="8e44e-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="8e44e-184">例如，可以在 Ubuntu 上使用 APT 和 MacOS 上的 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="8e44e-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="8e44e-185">遗憾的是，较新版本的 PROJ (SpatiaLite) 的依赖项与 EF 的默认 [SQLitePCLRaw 绑定](/dotnet/standard/data/sqlite/custom-versions#bundles)不兼容。</span><span class="sxs-lookup"><span data-stu-id="8e44e-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="8e44e-186">若要解决此情况，可以创建使用系统 SQLite 库的自定义 [SQLitePCLRaw 提供程序](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) ，也可以安装 SPATIALITE 禁用 PROJ 支持的自定义生成。</span><span class="sxs-lookup"><span data-stu-id="8e44e-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="8e44e-187">配置 SRID</span><span class="sxs-lookup"><span data-stu-id="8e44e-187">Configuring SRID</span></span>

<span data-ttu-id="8e44e-188">在 SpatiaLite 中，列需要为每个列指定一个 SRID。</span><span class="sxs-lookup"><span data-stu-id="8e44e-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="8e44e-189">默认的 SRID 为 `0` 。</span><span class="sxs-lookup"><span data-stu-id="8e44e-189">The default SRID is `0`.</span></span> <span data-ttu-id="8e44e-190">使用 ForSqliteHasSrid 方法指定其他 SRID。</span><span class="sxs-lookup"><span data-stu-id="8e44e-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="8e44e-191">维度</span><span class="sxs-lookup"><span data-stu-id="8e44e-191">Dimension</span></span>

<span data-ttu-id="8e44e-192">类似于 SRID，列的维度 (或坐标) 也被指定为列的一部分。</span><span class="sxs-lookup"><span data-stu-id="8e44e-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="8e44e-193">默认坐标为 X 和 Y。使用 ForSqliteHasDimension 方法启用其他坐标 (Z 和 M) 。</span><span class="sxs-lookup"><span data-stu-id="8e44e-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="8e44e-194">转换的操作</span><span class="sxs-lookup"><span data-stu-id="8e44e-194">Translated Operations</span></span>

<span data-ttu-id="8e44e-195">此表显示每个 EF Core 提供程序将哪些 NTS 成员转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="8e44e-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="8e44e-196">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="8e44e-196">NetTopologySuite</span></span> | <span data-ttu-id="8e44e-197">SQL Server (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-197">SQL Server (geometry)</span></span> | <span data-ttu-id="8e44e-198">SQL Server (地域) </span><span class="sxs-lookup"><span data-stu-id="8e44e-198">SQL Server (geography)</span></span> | <span data-ttu-id="8e44e-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="8e44e-199">SQLite</span></span> | <span data-ttu-id="8e44e-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="8e44e-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="8e44e-201">Geometry</span><span class="sxs-lookup"><span data-stu-id="8e44e-201">Geometry.Area</span></span> | <span data-ttu-id="8e44e-202">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-202">✔</span></span> | <span data-ttu-id="8e44e-203">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-203">✔</span></span> | <span data-ttu-id="8e44e-204">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-204">✔</span></span> | <span data-ttu-id="8e44e-205">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-205">✔</span></span>
<span data-ttu-id="8e44e-206">AsBinary ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="8e44e-207">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-207">✔</span></span> | <span data-ttu-id="8e44e-208">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-208">✔</span></span> | <span data-ttu-id="8e44e-209">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-209">✔</span></span> | <span data-ttu-id="8e44e-210">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-210">✔</span></span>
<span data-ttu-id="8e44e-211">AsText ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-211">Geometry.AsText()</span></span> | <span data-ttu-id="8e44e-212">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-212">✔</span></span> | <span data-ttu-id="8e44e-213">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-213">✔</span></span> | <span data-ttu-id="8e44e-214">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-214">✔</span></span> | <span data-ttu-id="8e44e-215">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-215">✔</span></span>
<span data-ttu-id="8e44e-216">Geometry</span><span class="sxs-lookup"><span data-stu-id="8e44e-216">Geometry.Boundary</span></span> | <span data-ttu-id="8e44e-217">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-217">✔</span></span> | | <span data-ttu-id="8e44e-218">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-218">✔</span></span> | <span data-ttu-id="8e44e-219">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-219">✔</span></span>
<span data-ttu-id="8e44e-220">Geometry (双) </span><span class="sxs-lookup"><span data-stu-id="8e44e-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="8e44e-221">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-221">✔</span></span> | <span data-ttu-id="8e44e-222">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-222">✔</span></span> | <span data-ttu-id="8e44e-223">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-223">✔</span></span> | <span data-ttu-id="8e44e-224">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-224">✔</span></span>
<span data-ttu-id="8e44e-225"> (double、int) 的 Geometry</span><span class="sxs-lookup"><span data-stu-id="8e44e-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="8e44e-226">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-226">✔</span></span> | <span data-ttu-id="8e44e-227">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-227">✔</span></span>
<span data-ttu-id="8e44e-228">质心</span><span class="sxs-lookup"><span data-stu-id="8e44e-228">Geometry.Centroid</span></span> | <span data-ttu-id="8e44e-229">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-229">✔</span></span> | | <span data-ttu-id="8e44e-230">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-230">✔</span></span> | <span data-ttu-id="8e44e-231">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-231">✔</span></span>
<span data-ttu-id="8e44e-232">Geometry：包含 (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="8e44e-233">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-233">✔</span></span> | <span data-ttu-id="8e44e-234">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-234">✔</span></span> | <span data-ttu-id="8e44e-235">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-235">✔</span></span> | <span data-ttu-id="8e44e-236">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-236">✔</span></span>
<span data-ttu-id="8e44e-237">ConvexHull ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="8e44e-238">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-238">✔</span></span> | <span data-ttu-id="8e44e-239">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-239">✔</span></span> | <span data-ttu-id="8e44e-240">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-240">✔</span></span> | <span data-ttu-id="8e44e-241">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-241">✔</span></span>
<span data-ttu-id="8e44e-242">CoveredBy (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="8e44e-243">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-243">✔</span></span> | <span data-ttu-id="8e44e-244">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-244">✔</span></span>
<span data-ttu-id="8e44e-245">几何。覆盖 (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="8e44e-246">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-246">✔</span></span> | <span data-ttu-id="8e44e-247">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-247">✔</span></span>
<span data-ttu-id="8e44e-248">Geometry (几何交叉) </span><span class="sxs-lookup"><span data-stu-id="8e44e-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="8e44e-249">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-249">✔</span></span> | | <span data-ttu-id="8e44e-250">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-250">✔</span></span> | <span data-ttu-id="8e44e-251">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-251">✔</span></span>
<span data-ttu-id="8e44e-252">Geometry (几何) 的差异</span><span class="sxs-lookup"><span data-stu-id="8e44e-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="8e44e-253">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-253">✔</span></span> | <span data-ttu-id="8e44e-254">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-254">✔</span></span> | <span data-ttu-id="8e44e-255">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-255">✔</span></span> | <span data-ttu-id="8e44e-256">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-256">✔</span></span>
<span data-ttu-id="8e44e-257">Geometry。维度</span><span class="sxs-lookup"><span data-stu-id="8e44e-257">Geometry.Dimension</span></span> | <span data-ttu-id="8e44e-258">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-258">✔</span></span> | <span data-ttu-id="8e44e-259">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-259">✔</span></span> | <span data-ttu-id="8e44e-260">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-260">✔</span></span> | <span data-ttu-id="8e44e-261">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-261">✔</span></span>
<span data-ttu-id="8e44e-262">不连续的 (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="8e44e-263">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-263">✔</span></span> | <span data-ttu-id="8e44e-264">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-264">✔</span></span> | <span data-ttu-id="8e44e-265">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-265">✔</span></span> | <span data-ttu-id="8e44e-266">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-266">✔</span></span>
<span data-ttu-id="8e44e-267">Geometry (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="8e44e-268">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-268">✔</span></span> | <span data-ttu-id="8e44e-269">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-269">✔</span></span> | <span data-ttu-id="8e44e-270">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-270">✔</span></span> | <span data-ttu-id="8e44e-271">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-271">✔</span></span>
<span data-ttu-id="8e44e-272">Geometry 信封</span><span class="sxs-lookup"><span data-stu-id="8e44e-272">Geometry.Envelope</span></span> | <span data-ttu-id="8e44e-273">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-273">✔</span></span> | | <span data-ttu-id="8e44e-274">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-274">✔</span></span> | <span data-ttu-id="8e44e-275">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-275">✔</span></span>
<span data-ttu-id="8e44e-276">EqualsExact (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="8e44e-277">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-277">✔</span></span>
<span data-ttu-id="8e44e-278">EqualsTopologically (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="8e44e-279">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-279">✔</span></span> | <span data-ttu-id="8e44e-280">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-280">✔</span></span> | <span data-ttu-id="8e44e-281">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-281">✔</span></span> | <span data-ttu-id="8e44e-282">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-282">✔</span></span>
<span data-ttu-id="8e44e-283">GeometryType</span><span class="sxs-lookup"><span data-stu-id="8e44e-283">Geometry.GeometryType</span></span> | <span data-ttu-id="8e44e-284">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-284">✔</span></span> | <span data-ttu-id="8e44e-285">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-285">✔</span></span> | <span data-ttu-id="8e44e-286">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-286">✔</span></span> | <span data-ttu-id="8e44e-287">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-287">✔</span></span>
<span data-ttu-id="8e44e-288">GetGeometryN (int) </span><span class="sxs-lookup"><span data-stu-id="8e44e-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="8e44e-289">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-289">✔</span></span> | | <span data-ttu-id="8e44e-290">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-290">✔</span></span> | <span data-ttu-id="8e44e-291">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-291">✔</span></span>
<span data-ttu-id="8e44e-292">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="8e44e-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="8e44e-293">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-293">✔</span></span> | | <span data-ttu-id="8e44e-294">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-294">✔</span></span> | <span data-ttu-id="8e44e-295">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-295">✔</span></span>
<span data-ttu-id="8e44e-296">几何图形 (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="8e44e-297">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-297">✔</span></span> | <span data-ttu-id="8e44e-298">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-298">✔</span></span> | <span data-ttu-id="8e44e-299">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-299">✔</span></span> | <span data-ttu-id="8e44e-300">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-300">✔</span></span>
<span data-ttu-id="8e44e-301">Geometry (几何的交集) </span><span class="sxs-lookup"><span data-stu-id="8e44e-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="8e44e-302">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-302">✔</span></span> | <span data-ttu-id="8e44e-303">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-303">✔</span></span> | <span data-ttu-id="8e44e-304">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-304">✔</span></span> | <span data-ttu-id="8e44e-305">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-305">✔</span></span>
<span data-ttu-id="8e44e-306">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="8e44e-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="8e44e-307">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-307">✔</span></span> | <span data-ttu-id="8e44e-308">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-308">✔</span></span> | <span data-ttu-id="8e44e-309">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-309">✔</span></span> | <span data-ttu-id="8e44e-310">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-310">✔</span></span>
<span data-ttu-id="8e44e-311">IsSimple</span><span class="sxs-lookup"><span data-stu-id="8e44e-311">Geometry.IsSimple</span></span> | <span data-ttu-id="8e44e-312">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-312">✔</span></span> | | <span data-ttu-id="8e44e-313">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-313">✔</span></span> | <span data-ttu-id="8e44e-314">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-314">✔</span></span>
<span data-ttu-id="8e44e-315">Geometry</span><span class="sxs-lookup"><span data-stu-id="8e44e-315">Geometry.IsValid</span></span> | <span data-ttu-id="8e44e-316">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-316">✔</span></span> | <span data-ttu-id="8e44e-317">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-317">✔</span></span> | <span data-ttu-id="8e44e-318">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-318">✔</span></span> | <span data-ttu-id="8e44e-319">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-319">✔</span></span>
<span data-ttu-id="8e44e-320">IsWithinDistance (Geometry、double) </span><span class="sxs-lookup"><span data-stu-id="8e44e-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="8e44e-321">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-321">✔</span></span> | | <span data-ttu-id="8e44e-322">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-322">✔</span></span> | <span data-ttu-id="8e44e-323">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-323">✔</span></span>
<span data-ttu-id="8e44e-324">Geometry。长度</span><span class="sxs-lookup"><span data-stu-id="8e44e-324">Geometry.Length</span></span> | <span data-ttu-id="8e44e-325">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-325">✔</span></span> | <span data-ttu-id="8e44e-326">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-326">✔</span></span> | <span data-ttu-id="8e44e-327">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-327">✔</span></span> | <span data-ttu-id="8e44e-328">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-328">✔</span></span>
<span data-ttu-id="8e44e-329">NumGeometries</span><span class="sxs-lookup"><span data-stu-id="8e44e-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="8e44e-330">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-330">✔</span></span> | <span data-ttu-id="8e44e-331">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-331">✔</span></span> | <span data-ttu-id="8e44e-332">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-332">✔</span></span> | <span data-ttu-id="8e44e-333">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-333">✔</span></span>
<span data-ttu-id="8e44e-334">X.numpoints</span><span class="sxs-lookup"><span data-stu-id="8e44e-334">Geometry.NumPoints</span></span> | <span data-ttu-id="8e44e-335">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-335">✔</span></span> | <span data-ttu-id="8e44e-336">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-336">✔</span></span> | <span data-ttu-id="8e44e-337">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-337">✔</span></span> | <span data-ttu-id="8e44e-338">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-338">✔</span></span>
<span data-ttu-id="8e44e-339">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="8e44e-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="8e44e-340">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-340">✔</span></span> | <span data-ttu-id="8e44e-341">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-341">✔</span></span> | <span data-ttu-id="8e44e-342">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-342">✔</span></span> | <span data-ttu-id="8e44e-343">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-343">✔</span></span>
<span data-ttu-id="8e44e-344">Geometry (几何重叠) </span><span class="sxs-lookup"><span data-stu-id="8e44e-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="8e44e-345">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-345">✔</span></span> | <span data-ttu-id="8e44e-346">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-346">✔</span></span> | <span data-ttu-id="8e44e-347">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-347">✔</span></span> | <span data-ttu-id="8e44e-348">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-348">✔</span></span>
<span data-ttu-id="8e44e-349">PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="8e44e-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="8e44e-350">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-350">✔</span></span> | | <span data-ttu-id="8e44e-351">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-351">✔</span></span> | <span data-ttu-id="8e44e-352">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-352">✔</span></span>
<span data-ttu-id="8e44e-353">Geometry (Geometry、string) 相关</span><span class="sxs-lookup"><span data-stu-id="8e44e-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="8e44e-354">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-354">✔</span></span> | | <span data-ttu-id="8e44e-355">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-355">✔</span></span> | <span data-ttu-id="8e44e-356">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-356">✔</span></span>
<span data-ttu-id="8e44e-357">Geometry ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="8e44e-358">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-358">✔</span></span> | <span data-ttu-id="8e44e-359">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-359">✔</span></span>
<span data-ttu-id="8e44e-360">SRID</span><span class="sxs-lookup"><span data-stu-id="8e44e-360">Geometry.SRID</span></span> | <span data-ttu-id="8e44e-361">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-361">✔</span></span> | <span data-ttu-id="8e44e-362">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-362">✔</span></span> | <span data-ttu-id="8e44e-363">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-363">✔</span></span> | <span data-ttu-id="8e44e-364">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-364">✔</span></span>
<span data-ttu-id="8e44e-365">SymmetricDifference (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="8e44e-366">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-366">✔</span></span> | <span data-ttu-id="8e44e-367">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-367">✔</span></span> | <span data-ttu-id="8e44e-368">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-368">✔</span></span> | <span data-ttu-id="8e44e-369">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-369">✔</span></span>
<span data-ttu-id="8e44e-370">ToBinary ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="8e44e-371">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-371">✔</span></span> | <span data-ttu-id="8e44e-372">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-372">✔</span></span> | <span data-ttu-id="8e44e-373">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-373">✔</span></span> | <span data-ttu-id="8e44e-374">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-374">✔</span></span>
<span data-ttu-id="8e44e-375">ToText ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-375">Geometry.ToText()</span></span> | <span data-ttu-id="8e44e-376">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-376">✔</span></span> | <span data-ttu-id="8e44e-377">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-377">✔</span></span> | <span data-ttu-id="8e44e-378">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-378">✔</span></span> | <span data-ttu-id="8e44e-379">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-379">✔</span></span>
<span data-ttu-id="8e44e-380">几何图形 (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="8e44e-381">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-381">✔</span></span> | | <span data-ttu-id="8e44e-382">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-382">✔</span></span> | <span data-ttu-id="8e44e-383">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-383">✔</span></span>
<span data-ttu-id="8e44e-384">Geometry ( # A1</span><span class="sxs-lookup"><span data-stu-id="8e44e-384">Geometry.Union()</span></span> | | | <span data-ttu-id="8e44e-385">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-385">✔</span></span> | <span data-ttu-id="8e44e-386">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-386">✔</span></span>
<span data-ttu-id="8e44e-387">Geometry (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="8e44e-388">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-388">✔</span></span> | <span data-ttu-id="8e44e-389">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-389">✔</span></span> | <span data-ttu-id="8e44e-390">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-390">✔</span></span> | <span data-ttu-id="8e44e-391">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-391">✔</span></span>
<span data-ttu-id="8e44e-392">Geometry (几何) </span><span class="sxs-lookup"><span data-stu-id="8e44e-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="8e44e-393">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-393">✔</span></span> | <span data-ttu-id="8e44e-394">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-394">✔</span></span> | <span data-ttu-id="8e44e-395">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-395">✔</span></span> | <span data-ttu-id="8e44e-396">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-396">✔</span></span>
<span data-ttu-id="8e44e-397">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="8e44e-397">GeometryCollection.Count</span></span> | <span data-ttu-id="8e44e-398">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-398">✔</span></span> | <span data-ttu-id="8e44e-399">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-399">✔</span></span> | <span data-ttu-id="8e44e-400">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-400">✔</span></span> | <span data-ttu-id="8e44e-401">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-401">✔</span></span>
<span data-ttu-id="8e44e-402">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="8e44e-402">GeometryCollection[int]</span></span> | <span data-ttu-id="8e44e-403">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-403">✔</span></span> | <span data-ttu-id="8e44e-404">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-404">✔</span></span> | <span data-ttu-id="8e44e-405">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-405">✔</span></span> | <span data-ttu-id="8e44e-406">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-406">✔</span></span>
<span data-ttu-id="8e44e-407">LineString</span><span class="sxs-lookup"><span data-stu-id="8e44e-407">LineString.Count</span></span> | <span data-ttu-id="8e44e-408">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-408">✔</span></span> | <span data-ttu-id="8e44e-409">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-409">✔</span></span> | <span data-ttu-id="8e44e-410">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-410">✔</span></span> | <span data-ttu-id="8e44e-411">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-411">✔</span></span>
<span data-ttu-id="8e44e-412">LineString 终结点</span><span class="sxs-lookup"><span data-stu-id="8e44e-412">LineString.EndPoint</span></span> | <span data-ttu-id="8e44e-413">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-413">✔</span></span> | <span data-ttu-id="8e44e-414">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-414">✔</span></span> | <span data-ttu-id="8e44e-415">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-415">✔</span></span> | <span data-ttu-id="8e44e-416">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-416">✔</span></span>
<span data-ttu-id="8e44e-417">LineString. GetPointN (int) </span><span class="sxs-lookup"><span data-stu-id="8e44e-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="8e44e-418">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-418">✔</span></span> | <span data-ttu-id="8e44e-419">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-419">✔</span></span> | <span data-ttu-id="8e44e-420">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-420">✔</span></span> | <span data-ttu-id="8e44e-421">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-421">✔</span></span>
<span data-ttu-id="8e44e-422">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="8e44e-422">LineString.IsClosed</span></span> | <span data-ttu-id="8e44e-423">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-423">✔</span></span> | <span data-ttu-id="8e44e-424">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-424">✔</span></span> | <span data-ttu-id="8e44e-425">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-425">✔</span></span> | <span data-ttu-id="8e44e-426">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-426">✔</span></span>
<span data-ttu-id="8e44e-427">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="8e44e-427">LineString.IsRing</span></span> | <span data-ttu-id="8e44e-428">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-428">✔</span></span> | | <span data-ttu-id="8e44e-429">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-429">✔</span></span> | <span data-ttu-id="8e44e-430">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-430">✔</span></span>
<span data-ttu-id="8e44e-431">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="8e44e-431">LineString.StartPoint</span></span> | <span data-ttu-id="8e44e-432">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-432">✔</span></span> | <span data-ttu-id="8e44e-433">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-433">✔</span></span> | <span data-ttu-id="8e44e-434">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-434">✔</span></span> | <span data-ttu-id="8e44e-435">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-435">✔</span></span>
<span data-ttu-id="8e44e-436">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="8e44e-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="8e44e-437">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-437">✔</span></span> | <span data-ttu-id="8e44e-438">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-438">✔</span></span> | <span data-ttu-id="8e44e-439">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-439">✔</span></span> | <span data-ttu-id="8e44e-440">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-440">✔</span></span>
<span data-ttu-id="8e44e-441">点 M</span><span class="sxs-lookup"><span data-stu-id="8e44e-441">Point.M</span></span> | <span data-ttu-id="8e44e-442">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-442">✔</span></span> | <span data-ttu-id="8e44e-443">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-443">✔</span></span> | <span data-ttu-id="8e44e-444">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-444">✔</span></span> | <span data-ttu-id="8e44e-445">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-445">✔</span></span>
<span data-ttu-id="8e44e-446">点 X</span><span class="sxs-lookup"><span data-stu-id="8e44e-446">Point.X</span></span> | <span data-ttu-id="8e44e-447">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-447">✔</span></span> | <span data-ttu-id="8e44e-448">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-448">✔</span></span> | <span data-ttu-id="8e44e-449">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-449">✔</span></span> | <span data-ttu-id="8e44e-450">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-450">✔</span></span>
<span data-ttu-id="8e44e-451">Point。 Y</span><span class="sxs-lookup"><span data-stu-id="8e44e-451">Point.Y</span></span> | <span data-ttu-id="8e44e-452">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-452">✔</span></span> | <span data-ttu-id="8e44e-453">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-453">✔</span></span> | <span data-ttu-id="8e44e-454">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-454">✔</span></span> | <span data-ttu-id="8e44e-455">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-455">✔</span></span>
<span data-ttu-id="8e44e-456">点 Z</span><span class="sxs-lookup"><span data-stu-id="8e44e-456">Point.Z</span></span> | <span data-ttu-id="8e44e-457">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-457">✔</span></span> | <span data-ttu-id="8e44e-458">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-458">✔</span></span> | <span data-ttu-id="8e44e-459">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-459">✔</span></span> | <span data-ttu-id="8e44e-460">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-460">✔</span></span>
<span data-ttu-id="8e44e-461">多边形。 ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="8e44e-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="8e44e-462">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-462">✔</span></span> | <span data-ttu-id="8e44e-463">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-463">✔</span></span> | <span data-ttu-id="8e44e-464">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-464">✔</span></span> | <span data-ttu-id="8e44e-465">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-465">✔</span></span>
<span data-ttu-id="8e44e-466">GetInteriorRingN (int) </span><span class="sxs-lookup"><span data-stu-id="8e44e-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="8e44e-467">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-467">✔</span></span> | <span data-ttu-id="8e44e-468">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-468">✔</span></span> | <span data-ttu-id="8e44e-469">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-469">✔</span></span> | <span data-ttu-id="8e44e-470">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-470">✔</span></span>
<span data-ttu-id="8e44e-471">多边形。 NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="8e44e-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="8e44e-472">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-472">✔</span></span> | <span data-ttu-id="8e44e-473">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-473">✔</span></span> | <span data-ttu-id="8e44e-474">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-474">✔</span></span> | <span data-ttu-id="8e44e-475">✔</span><span class="sxs-lookup"><span data-stu-id="8e44e-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e44e-476">其他资源</span><span class="sxs-lookup"><span data-stu-id="8e44e-476">Additional resources</span></span>

* [<span data-ttu-id="8e44e-477">SQL Server 中的空间数据</span><span class="sxs-lookup"><span data-stu-id="8e44e-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="8e44e-478">SpatiaLite 主页</span><span class="sxs-lookup"><span data-stu-id="8e44e-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="8e44e-479">Npgsql 空间文档</span><span class="sxs-lookup"><span data-stu-id="8e44e-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="8e44e-480">PostGIS 文档</span><span class="sxs-lookup"><span data-stu-id="8e44e-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
