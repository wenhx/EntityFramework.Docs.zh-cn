---
title: 空间数据-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 335d4f3a601624f7c994b7dcacefe4ef6798beb3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655601"
---
# <a name="spatial-data"></a><span data-ttu-id="e48d5-102">空间数据</span><span class="sxs-lookup"><span data-stu-id="e48d5-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="e48d5-103">此功能是在 EF Core 2.2 中添加的。</span><span class="sxs-lookup"><span data-stu-id="e48d5-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="e48d5-104">空间数据表示对象的物理位置和形状。</span><span class="sxs-lookup"><span data-stu-id="e48d5-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="e48d5-105">许多数据库提供对此类数据的支持，以便能够与其他数据一起进行索引和查询。</span><span class="sxs-lookup"><span data-stu-id="e48d5-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="e48d5-106">常见方案包括从位置在给定距离内查询对象，或选择其边框包含给定位置的对象。</span><span class="sxs-lookup"><span data-stu-id="e48d5-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="e48d5-107">EF Core 支持使用[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空间库映射到空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="e48d5-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="e48d5-108">安装</span><span class="sxs-lookup"><span data-stu-id="e48d5-108">Installing</span></span>

<span data-ttu-id="e48d5-109">若要在 EF Core 中使用空间数据，需要安装相应的支持 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="e48d5-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="e48d5-110">需要安装哪个包取决于所使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="e48d5-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="e48d5-111">EF Core 提供程序</span><span class="sxs-lookup"><span data-stu-id="e48d5-111">EF Core Provider</span></span>                        | <span data-ttu-id="e48d5-112">空间 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="e48d5-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="e48d5-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e48d5-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="e48d5-114">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e48d5-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="e48d5-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e48d5-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="e48d5-116">Microsoft.entityframeworkcore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e48d5-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="e48d5-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="e48d5-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="e48d5-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e48d5-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="e48d5-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="e48d5-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="e48d5-120">Npgsql. Microsoft.entityframeworkcore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e48d5-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="e48d5-121">反向工程</span><span class="sxs-lookup"><span data-stu-id="e48d5-121">Reverse engineering</span></span>

<span data-ttu-id="e48d5-122">空间 NuGet 包还启用具有空间属性的[反向工程](../managing-schemas/scaffolding.md)模型，但需要在运行 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`***之前***安装包。</span><span class="sxs-lookup"><span data-stu-id="e48d5-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="e48d5-123">否则，你将收到有关找不到列的类型映射的警告，将跳过这些列。</span><span class="sxs-lookup"><span data-stu-id="e48d5-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="e48d5-124">NetTopologySuite （NTS）</span><span class="sxs-lookup"><span data-stu-id="e48d5-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="e48d5-125">NetTopologySuite 是用于 .NET 的空间库。</span><span class="sxs-lookup"><span data-stu-id="e48d5-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="e48d5-126">EF Core 使用模型中的 NTS 类型启用映射到数据库中的空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="e48d5-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="e48d5-127">若要通过 NTS 启用到空间类型的映射，请在提供程序的 DbContext 选项生成器上调用 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="e48d5-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="e48d5-128">例如，对于 SQL Server，你应将其称为。</span><span class="sxs-lookup"><span data-stu-id="e48d5-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="e48d5-129">有几种空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="e48d5-129">There are several spatial data types.</span></span> <span data-ttu-id="e48d5-130">使用哪种类型取决于您想要允许的形状的类型。</span><span class="sxs-lookup"><span data-stu-id="e48d5-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="e48d5-131">下面是可用于模型中的属性的 NTS 类型的层次结构。</span><span class="sxs-lookup"><span data-stu-id="e48d5-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="e48d5-132">它们位于 `NetTopologySuite.Geometries` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="e48d5-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="e48d5-133">Geometry</span><span class="sxs-lookup"><span data-stu-id="e48d5-133">Geometry</span></span>
  * <span data-ttu-id="e48d5-134">点</span><span class="sxs-lookup"><span data-stu-id="e48d5-134">Point</span></span>
  * <span data-ttu-id="e48d5-135">LineString</span><span class="sxs-lookup"><span data-stu-id="e48d5-135">LineString</span></span>
  * <span data-ttu-id="e48d5-136">多边形</span><span class="sxs-lookup"><span data-stu-id="e48d5-136">Polygon</span></span>
  * <span data-ttu-id="e48d5-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e48d5-137">GeometryCollection</span></span>
    * <span data-ttu-id="e48d5-138">单</span><span class="sxs-lookup"><span data-stu-id="e48d5-138">MultiPoint</span></span>
    * <span data-ttu-id="e48d5-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="e48d5-139">MultiLineString</span></span>
    * <span data-ttu-id="e48d5-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="e48d5-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="e48d5-141">NTS 不支持 CircularString、CompoundCurve 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="e48d5-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="e48d5-142">使用基本几何图形类型允许属性指定任意类型的形状。</span><span class="sxs-lookup"><span data-stu-id="e48d5-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="e48d5-143">以下实体类可用于映射到[广角导入示例数据库](https://go.microsoft.com/fwlink/?LinkID=800630)中的表。</span><span class="sxs-lookup"><span data-stu-id="e48d5-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="e48d5-144">创建值</span><span class="sxs-lookup"><span data-stu-id="e48d5-144">Creating values</span></span>

<span data-ttu-id="e48d5-145">您可以使用构造函数来创建 geometry 对象;但是，NTS 建议改为使用几何工厂。</span><span class="sxs-lookup"><span data-stu-id="e48d5-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="e48d5-146">这允许您指定默认 SRID （坐标使用的空间引用系统），并使您能够控制更高级的任务（例如，在计算过程中使用）和坐标序列（确定哪些坐标维度和度量值--可用）。</span><span class="sxs-lookup"><span data-stu-id="e48d5-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="e48d5-147">4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。</span><span class="sxs-lookup"><span data-stu-id="e48d5-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="e48d5-148">经度和纬度</span><span class="sxs-lookup"><span data-stu-id="e48d5-148">Longitude and Latitude</span></span>

<span data-ttu-id="e48d5-149">NTS 中的坐标采用 X 和 Y 值。</span><span class="sxs-lookup"><span data-stu-id="e48d5-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="e48d5-150">若要表示经度和纬度，请将 X 用于经度，将 Y 用于纬度。</span><span class="sxs-lookup"><span data-stu-id="e48d5-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="e48d5-151">请注意，这是从通常会看到这些值的 `latitude, longitude` 格式**反向**进行的。</span><span class="sxs-lookup"><span data-stu-id="e48d5-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="e48d5-152">在客户端操作过程中忽略 SRID</span><span class="sxs-lookup"><span data-stu-id="e48d5-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="e48d5-153">NTS 在操作过程中忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="e48d5-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="e48d5-154">它假定为平面坐标系统。</span><span class="sxs-lookup"><span data-stu-id="e48d5-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="e48d5-155">这意味着，如果在经度和纬度方面指定了坐标，则某些客户端计算的值（例如，距离、长度和区域）将为度数，而不是计量。</span><span class="sxs-lookup"><span data-stu-id="e48d5-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="e48d5-156">若要获得更有意义的值，首先需要使用库（如[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) ）在计算这些值之前投影到另一个坐标系统的坐标。</span><span class="sxs-lookup"><span data-stu-id="e48d5-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="e48d5-157">如果通过 EF Core 通过 SQL 对操作进行服务器计算，则该结果的单元将由数据库确定。</span><span class="sxs-lookup"><span data-stu-id="e48d5-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="e48d5-158">下面是一个示例，说明如何使用 ProjNet4GeoAPI 来计算两个城市之间的距离。</span><span class="sxs-lookup"><span data-stu-id="e48d5-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="e48d5-159">查询数据</span><span class="sxs-lookup"><span data-stu-id="e48d5-159">Querying Data</span></span>

<span data-ttu-id="e48d5-160">在 LINQ 中，可用作数据库函数的 NTS 方法和属性将转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="e48d5-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="e48d5-161">例如，在以下查询中转换距离和包含方法。</span><span class="sxs-lookup"><span data-stu-id="e48d5-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="e48d5-162">本文末尾的表格显示了不同 EF Core 提供商支持哪些成员。</span><span class="sxs-lookup"><span data-stu-id="e48d5-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="e48d5-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e48d5-163">SQL Server</span></span>

<span data-ttu-id="e48d5-164">如果你正在使用 SQL Server，你还应该注意一些其他问题。</span><span class="sxs-lookup"><span data-stu-id="e48d5-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="e48d5-165">地理或几何图形</span><span class="sxs-lookup"><span data-stu-id="e48d5-165">Geography or geometry</span></span>

<span data-ttu-id="e48d5-166">默认情况下，空间属性映射到 SQL Server 中的 `geography` 列。</span><span class="sxs-lookup"><span data-stu-id="e48d5-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="e48d5-167">若要使用 `geometry`，请在模型中[配置列类型](xref:core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="e48d5-167">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="e48d5-168">地理多边形环</span><span class="sxs-lookup"><span data-stu-id="e48d5-168">Geography polygon rings</span></span>

<span data-ttu-id="e48d5-169">使用 `geography` 列类型时，SQL Server 对外环（或外壳）和内部环（或孔）施加附加要求。</span><span class="sxs-lookup"><span data-stu-id="e48d5-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="e48d5-170">外部环必须逆时针旋转，并顺时针旋转内部环。</span><span class="sxs-lookup"><span data-stu-id="e48d5-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="e48d5-171">NTS 在将值发送到数据库之前对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="e48d5-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="e48d5-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="e48d5-172">FullGlobe</span></span>

<span data-ttu-id="e48d5-173">使用 `geography` 列类型时，SQL Server 具有非标准几何类型来表示全地球。</span><span class="sxs-lookup"><span data-stu-id="e48d5-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="e48d5-174">它还提供了一种方法，用于根据全地球（无外部环）来表示多边形。</span><span class="sxs-lookup"><span data-stu-id="e48d5-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="e48d5-175">NTS 不支持这两种方法。</span><span class="sxs-lookup"><span data-stu-id="e48d5-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="e48d5-176">NTS 不支持基于 FullGlobe 和多边形。</span><span class="sxs-lookup"><span data-stu-id="e48d5-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="e48d5-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="e48d5-177">SQLite</span></span>

<span data-ttu-id="e48d5-178">下面是使用 SQLite 的一些其他信息。</span><span class="sxs-lookup"><span data-stu-id="e48d5-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="e48d5-179">安装 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="e48d5-179">Installing SpatiaLite</span></span>

<span data-ttu-id="e48d5-180">在 Windows 上，本机 mod_spatialite 库以 NuGet 包依赖关系的形式分发。</span><span class="sxs-lookup"><span data-stu-id="e48d5-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="e48d5-181">其他平台需要单独安装它。</span><span class="sxs-lookup"><span data-stu-id="e48d5-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="e48d5-182">通常使用软件程序包管理器完成此操作。</span><span class="sxs-lookup"><span data-stu-id="e48d5-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="e48d5-183">例如，可以在 Ubuntu 上使用 APT 和 MacOS 上的 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="e48d5-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="e48d5-184">配置 SRID</span><span class="sxs-lookup"><span data-stu-id="e48d5-184">Configuring SRID</span></span>

<span data-ttu-id="e48d5-185">在 SpatiaLite 中，列需要为每个列指定一个 SRID。</span><span class="sxs-lookup"><span data-stu-id="e48d5-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="e48d5-186">默认的 SRID 是 `0`。</span><span class="sxs-lookup"><span data-stu-id="e48d5-186">The default SRID is `0`.</span></span> <span data-ttu-id="e48d5-187">使用 ForSqliteHasSrid 方法指定其他 SRID。</span><span class="sxs-lookup"><span data-stu-id="e48d5-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="e48d5-188">维度</span><span class="sxs-lookup"><span data-stu-id="e48d5-188">Dimension</span></span>

<span data-ttu-id="e48d5-189">类似于 SRID，列的维度（或坐标）也被指定为列的一部分。</span><span class="sxs-lookup"><span data-stu-id="e48d5-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="e48d5-190">默认坐标为 X 和 Y。使用 ForSqliteHasDimension 方法启用其他坐标（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="e48d5-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="e48d5-191">转换的操作</span><span class="sxs-lookup"><span data-stu-id="e48d5-191">Translated Operations</span></span>

<span data-ttu-id="e48d5-192">此表显示每个 EF Core 提供程序将哪些 NTS 成员转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="e48d5-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="e48d5-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e48d5-193">NetTopologySuite</span></span> | <span data-ttu-id="e48d5-194">SQL Server （geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-194">SQL Server (geometry)</span></span> | <span data-ttu-id="e48d5-195">SQL Server （geography）</span><span class="sxs-lookup"><span data-stu-id="e48d5-195">SQL Server (geography)</span></span> | <span data-ttu-id="e48d5-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="e48d5-196">SQLite</span></span> | <span data-ttu-id="e48d5-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="e48d5-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="e48d5-198">Geometry</span><span class="sxs-lookup"><span data-stu-id="e48d5-198">Geometry.Area</span></span> | <span data-ttu-id="e48d5-199">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-199">✔</span></span> | <span data-ttu-id="e48d5-200">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-200">✔</span></span> | <span data-ttu-id="e48d5-201">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-201">✔</span></span> | <span data-ttu-id="e48d5-202">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-202">✔</span></span>
<span data-ttu-id="e48d5-203">AsBinary （）</span><span class="sxs-lookup"><span data-stu-id="e48d5-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="e48d5-204">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-204">✔</span></span> | <span data-ttu-id="e48d5-205">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-205">✔</span></span> | <span data-ttu-id="e48d5-206">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-206">✔</span></span> | <span data-ttu-id="e48d5-207">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-207">✔</span></span>
<span data-ttu-id="e48d5-208">AsText （）</span><span class="sxs-lookup"><span data-stu-id="e48d5-208">Geometry.AsText()</span></span> | <span data-ttu-id="e48d5-209">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-209">✔</span></span> | <span data-ttu-id="e48d5-210">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-210">✔</span></span> | <span data-ttu-id="e48d5-211">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-211">✔</span></span> | <span data-ttu-id="e48d5-212">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-212">✔</span></span>
<span data-ttu-id="e48d5-213">Geometry</span><span class="sxs-lookup"><span data-stu-id="e48d5-213">Geometry.Boundary</span></span> | <span data-ttu-id="e48d5-214">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-214">✔</span></span> | | <span data-ttu-id="e48d5-215">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-215">✔</span></span> | <span data-ttu-id="e48d5-216">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-216">✔</span></span>
<span data-ttu-id="e48d5-217">Geometry （双精度型）</span><span class="sxs-lookup"><span data-stu-id="e48d5-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="e48d5-218">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-218">✔</span></span> | <span data-ttu-id="e48d5-219">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-219">✔</span></span> | <span data-ttu-id="e48d5-220">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-220">✔</span></span> | <span data-ttu-id="e48d5-221">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-221">✔</span></span>
<span data-ttu-id="e48d5-222">Geometry （double，int）</span><span class="sxs-lookup"><span data-stu-id="e48d5-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="e48d5-223">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-223">✔</span></span> | <span data-ttu-id="e48d5-224">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-224">✔</span></span>
<span data-ttu-id="e48d5-225">质心</span><span class="sxs-lookup"><span data-stu-id="e48d5-225">Geometry.Centroid</span></span> | <span data-ttu-id="e48d5-226">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-226">✔</span></span> | | <span data-ttu-id="e48d5-227">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-227">✔</span></span> | <span data-ttu-id="e48d5-228">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-228">✔</span></span>
<span data-ttu-id="e48d5-229">Geometry。 Contains （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="e48d5-230">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-230">✔</span></span> | <span data-ttu-id="e48d5-231">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-231">✔</span></span> | <span data-ttu-id="e48d5-232">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-232">✔</span></span> | <span data-ttu-id="e48d5-233">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-233">✔</span></span>
<span data-ttu-id="e48d5-234">ConvexHull （）</span><span class="sxs-lookup"><span data-stu-id="e48d5-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="e48d5-235">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-235">✔</span></span> | <span data-ttu-id="e48d5-236">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-236">✔</span></span> | <span data-ttu-id="e48d5-237">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-237">✔</span></span> | <span data-ttu-id="e48d5-238">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-238">✔</span></span>
<span data-ttu-id="e48d5-239">CoveredBy （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="e48d5-240">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-240">✔</span></span> | <span data-ttu-id="e48d5-241">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-241">✔</span></span>
<span data-ttu-id="e48d5-242">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="e48d5-243">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-243">✔</span></span> | <span data-ttu-id="e48d5-244">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-244">✔</span></span>
<span data-ttu-id="e48d5-245">几何。交叉（几何）</span><span class="sxs-lookup"><span data-stu-id="e48d5-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="e48d5-246">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-246">✔</span></span> | | <span data-ttu-id="e48d5-247">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-247">✔</span></span> | <span data-ttu-id="e48d5-248">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-248">✔</span></span>
<span data-ttu-id="e48d5-249">几何差（几何）</span><span class="sxs-lookup"><span data-stu-id="e48d5-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="e48d5-250">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-250">✔</span></span> | <span data-ttu-id="e48d5-251">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-251">✔</span></span> | <span data-ttu-id="e48d5-252">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-252">✔</span></span> | <span data-ttu-id="e48d5-253">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-253">✔</span></span>
<span data-ttu-id="e48d5-254">Geometry。维度</span><span class="sxs-lookup"><span data-stu-id="e48d5-254">Geometry.Dimension</span></span> | <span data-ttu-id="e48d5-255">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-255">✔</span></span> | <span data-ttu-id="e48d5-256">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-256">✔</span></span> | <span data-ttu-id="e48d5-257">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-257">✔</span></span> | <span data-ttu-id="e48d5-258">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-258">✔</span></span>
<span data-ttu-id="e48d5-259">不连续（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="e48d5-260">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-260">✔</span></span> | <span data-ttu-id="e48d5-261">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-261">✔</span></span> | <span data-ttu-id="e48d5-262">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-262">✔</span></span> | <span data-ttu-id="e48d5-263">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-263">✔</span></span>
<span data-ttu-id="e48d5-264">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="e48d5-265">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-265">✔</span></span> | <span data-ttu-id="e48d5-266">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-266">✔</span></span> | <span data-ttu-id="e48d5-267">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-267">✔</span></span> | <span data-ttu-id="e48d5-268">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-268">✔</span></span>
<span data-ttu-id="e48d5-269">Geometry 信封</span><span class="sxs-lookup"><span data-stu-id="e48d5-269">Geometry.Envelope</span></span> | <span data-ttu-id="e48d5-270">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-270">✔</span></span> | | <span data-ttu-id="e48d5-271">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-271">✔</span></span> | <span data-ttu-id="e48d5-272">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-272">✔</span></span>
<span data-ttu-id="e48d5-273">EqualsExact （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="e48d5-274">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-274">✔</span></span>
<span data-ttu-id="e48d5-275">EqualsTopologically （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="e48d5-276">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-276">✔</span></span> | <span data-ttu-id="e48d5-277">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-277">✔</span></span> | <span data-ttu-id="e48d5-278">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-278">✔</span></span> | <span data-ttu-id="e48d5-279">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-279">✔</span></span>
<span data-ttu-id="e48d5-280">GeometryType</span><span class="sxs-lookup"><span data-stu-id="e48d5-280">Geometry.GeometryType</span></span> | <span data-ttu-id="e48d5-281">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-281">✔</span></span> | <span data-ttu-id="e48d5-282">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-282">✔</span></span> | <span data-ttu-id="e48d5-283">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-283">✔</span></span> | <span data-ttu-id="e48d5-284">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-284">✔</span></span>
<span data-ttu-id="e48d5-285">GetGeometryN （int）</span><span class="sxs-lookup"><span data-stu-id="e48d5-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="e48d5-286">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-286">✔</span></span> | | <span data-ttu-id="e48d5-287">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-287">✔</span></span> | <span data-ttu-id="e48d5-288">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-288">✔</span></span>
<span data-ttu-id="e48d5-289">InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="e48d5-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="e48d5-290">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-290">✔</span></span> | | <span data-ttu-id="e48d5-291">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-291">✔</span></span> | <span data-ttu-id="e48d5-292">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-292">✔</span></span>
<span data-ttu-id="e48d5-293">几何交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-293">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="e48d5-294">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-294">✔</span></span> | <span data-ttu-id="e48d5-295">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-295">✔</span></span> | <span data-ttu-id="e48d5-296">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-296">✔</span></span> | <span data-ttu-id="e48d5-297">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-297">✔</span></span>
<span data-ttu-id="e48d5-298">几何和交集（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-298">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="e48d5-299">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-299">✔</span></span> | <span data-ttu-id="e48d5-300">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-300">✔</span></span> | <span data-ttu-id="e48d5-301">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-301">✔</span></span> | <span data-ttu-id="e48d5-302">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-302">✔</span></span>
<span data-ttu-id="e48d5-303">IsEmpty</span><span class="sxs-lookup"><span data-stu-id="e48d5-303">Geometry.IsEmpty</span></span> | <span data-ttu-id="e48d5-304">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-304">✔</span></span> | <span data-ttu-id="e48d5-305">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-305">✔</span></span> | <span data-ttu-id="e48d5-306">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-306">✔</span></span> | <span data-ttu-id="e48d5-307">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-307">✔</span></span>
<span data-ttu-id="e48d5-308">IsSimple</span><span class="sxs-lookup"><span data-stu-id="e48d5-308">Geometry.IsSimple</span></span> | <span data-ttu-id="e48d5-309">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-309">✔</span></span> | | <span data-ttu-id="e48d5-310">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-310">✔</span></span> | <span data-ttu-id="e48d5-311">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-311">✔</span></span>
<span data-ttu-id="e48d5-312">Geometry</span><span class="sxs-lookup"><span data-stu-id="e48d5-312">Geometry.IsValid</span></span> | <span data-ttu-id="e48d5-313">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-313">✔</span></span> | <span data-ttu-id="e48d5-314">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-314">✔</span></span> | <span data-ttu-id="e48d5-315">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-315">✔</span></span> | <span data-ttu-id="e48d5-316">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-316">✔</span></span>
<span data-ttu-id="e48d5-317">IsWithinDistance （Geometry，double）</span><span class="sxs-lookup"><span data-stu-id="e48d5-317">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="e48d5-318">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-318">✔</span></span> | | <span data-ttu-id="e48d5-319">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-319">✔</span></span> | <span data-ttu-id="e48d5-320">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-320">✔</span></span>
<span data-ttu-id="e48d5-321">Geometry。长度</span><span class="sxs-lookup"><span data-stu-id="e48d5-321">Geometry.Length</span></span> | <span data-ttu-id="e48d5-322">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-322">✔</span></span> | <span data-ttu-id="e48d5-323">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-323">✔</span></span> | <span data-ttu-id="e48d5-324">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-324">✔</span></span> | <span data-ttu-id="e48d5-325">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-325">✔</span></span>
<span data-ttu-id="e48d5-326">NumGeometries</span><span class="sxs-lookup"><span data-stu-id="e48d5-326">Geometry.NumGeometries</span></span> | <span data-ttu-id="e48d5-327">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-327">✔</span></span> | <span data-ttu-id="e48d5-328">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-328">✔</span></span> | <span data-ttu-id="e48d5-329">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-329">✔</span></span> | <span data-ttu-id="e48d5-330">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-330">✔</span></span>
<span data-ttu-id="e48d5-331">X.numpoints</span><span class="sxs-lookup"><span data-stu-id="e48d5-331">Geometry.NumPoints</span></span> | <span data-ttu-id="e48d5-332">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-332">✔</span></span> | <span data-ttu-id="e48d5-333">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-333">✔</span></span> | <span data-ttu-id="e48d5-334">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-334">✔</span></span> | <span data-ttu-id="e48d5-335">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-335">✔</span></span>
<span data-ttu-id="e48d5-336">OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="e48d5-336">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="e48d5-337">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-337">✔</span></span> | <span data-ttu-id="e48d5-338">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-338">✔</span></span> | <span data-ttu-id="e48d5-339">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-339">✔</span></span> | <span data-ttu-id="e48d5-340">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-340">✔</span></span>
<span data-ttu-id="e48d5-341">Geometry 重叠（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-341">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="e48d5-342">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-342">✔</span></span> | <span data-ttu-id="e48d5-343">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-343">✔</span></span> | <span data-ttu-id="e48d5-344">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-344">✔</span></span> | <span data-ttu-id="e48d5-345">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-345">✔</span></span>
<span data-ttu-id="e48d5-346">PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="e48d5-346">Geometry.PointOnSurface</span></span> | <span data-ttu-id="e48d5-347">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-347">✔</span></span> | | <span data-ttu-id="e48d5-348">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-348">✔</span></span> | <span data-ttu-id="e48d5-349">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-349">✔</span></span>
<span data-ttu-id="e48d5-350">Geometry （Geometry，string）</span><span class="sxs-lookup"><span data-stu-id="e48d5-350">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="e48d5-351">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-351">✔</span></span> | | <span data-ttu-id="e48d5-352">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-352">✔</span></span> | <span data-ttu-id="e48d5-353">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-353">✔</span></span>
<span data-ttu-id="e48d5-354">Geometry 反向（）</span><span class="sxs-lookup"><span data-stu-id="e48d5-354">Geometry.Reverse()</span></span> | | | <span data-ttu-id="e48d5-355">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-355">✔</span></span> | <span data-ttu-id="e48d5-356">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-356">✔</span></span>
<span data-ttu-id="e48d5-357">SRID</span><span class="sxs-lookup"><span data-stu-id="e48d5-357">Geometry.SRID</span></span> | <span data-ttu-id="e48d5-358">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-358">✔</span></span> | <span data-ttu-id="e48d5-359">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-359">✔</span></span> | <span data-ttu-id="e48d5-360">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-360">✔</span></span> | <span data-ttu-id="e48d5-361">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-361">✔</span></span>
<span data-ttu-id="e48d5-362">SymmetricDifference （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-362">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="e48d5-363">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-363">✔</span></span> | <span data-ttu-id="e48d5-364">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-364">✔</span></span> | <span data-ttu-id="e48d5-365">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-365">✔</span></span> | <span data-ttu-id="e48d5-366">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-366">✔</span></span>
<span data-ttu-id="e48d5-367">ToBinary （）</span><span class="sxs-lookup"><span data-stu-id="e48d5-367">Geometry.ToBinary()</span></span> | <span data-ttu-id="e48d5-368">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-368">✔</span></span> | <span data-ttu-id="e48d5-369">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-369">✔</span></span> | <span data-ttu-id="e48d5-370">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-370">✔</span></span> | <span data-ttu-id="e48d5-371">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-371">✔</span></span>
<span data-ttu-id="e48d5-372">ToText （）</span><span class="sxs-lookup"><span data-stu-id="e48d5-372">Geometry.ToText()</span></span> | <span data-ttu-id="e48d5-373">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-373">✔</span></span> | <span data-ttu-id="e48d5-374">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-374">✔</span></span> | <span data-ttu-id="e48d5-375">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-375">✔</span></span> | <span data-ttu-id="e48d5-376">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-376">✔</span></span>
<span data-ttu-id="e48d5-377">几何图形（几何）</span><span class="sxs-lookup"><span data-stu-id="e48d5-377">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="e48d5-378">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-378">✔</span></span> | | <span data-ttu-id="e48d5-379">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-379">✔</span></span> | <span data-ttu-id="e48d5-380">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-380">✔</span></span>
<span data-ttu-id="e48d5-381">Geometry （）</span><span class="sxs-lookup"><span data-stu-id="e48d5-381">Geometry.Union()</span></span> | | | <span data-ttu-id="e48d5-382">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-382">✔</span></span> | <span data-ttu-id="e48d5-383">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-383">✔</span></span>
<span data-ttu-id="e48d5-384">Geometry （Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-384">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="e48d5-385">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-385">✔</span></span> | <span data-ttu-id="e48d5-386">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-386">✔</span></span> | <span data-ttu-id="e48d5-387">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-387">✔</span></span> | <span data-ttu-id="e48d5-388">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-388">✔</span></span>
<span data-ttu-id="e48d5-389">几何图形。内（Geometry）</span><span class="sxs-lookup"><span data-stu-id="e48d5-389">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="e48d5-390">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-390">✔</span></span> | <span data-ttu-id="e48d5-391">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-391">✔</span></span> | <span data-ttu-id="e48d5-392">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-392">✔</span></span> | <span data-ttu-id="e48d5-393">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-393">✔</span></span>
<span data-ttu-id="e48d5-394">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e48d5-394">GeometryCollection.Count</span></span> | <span data-ttu-id="e48d5-395">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-395">✔</span></span> | <span data-ttu-id="e48d5-396">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-396">✔</span></span> | <span data-ttu-id="e48d5-397">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-397">✔</span></span> | <span data-ttu-id="e48d5-398">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-398">✔</span></span>
<span data-ttu-id="e48d5-399">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="e48d5-399">GeometryCollection[int]</span></span> | <span data-ttu-id="e48d5-400">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-400">✔</span></span> | <span data-ttu-id="e48d5-401">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-401">✔</span></span> | <span data-ttu-id="e48d5-402">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-402">✔</span></span> | <span data-ttu-id="e48d5-403">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-403">✔</span></span>
<span data-ttu-id="e48d5-404">LineString</span><span class="sxs-lookup"><span data-stu-id="e48d5-404">LineString.Count</span></span> | <span data-ttu-id="e48d5-405">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-405">✔</span></span> | <span data-ttu-id="e48d5-406">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-406">✔</span></span> | <span data-ttu-id="e48d5-407">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-407">✔</span></span> | <span data-ttu-id="e48d5-408">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-408">✔</span></span>
<span data-ttu-id="e48d5-409">LineString 终结点</span><span class="sxs-lookup"><span data-stu-id="e48d5-409">LineString.EndPoint</span></span> | <span data-ttu-id="e48d5-410">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-410">✔</span></span> | <span data-ttu-id="e48d5-411">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-411">✔</span></span> | <span data-ttu-id="e48d5-412">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-412">✔</span></span> | <span data-ttu-id="e48d5-413">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-413">✔</span></span>
<span data-ttu-id="e48d5-414">LineString. GetPointN （int）</span><span class="sxs-lookup"><span data-stu-id="e48d5-414">LineString.GetPointN(int)</span></span> | <span data-ttu-id="e48d5-415">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-415">✔</span></span> | <span data-ttu-id="e48d5-416">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-416">✔</span></span> | <span data-ttu-id="e48d5-417">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-417">✔</span></span> | <span data-ttu-id="e48d5-418">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-418">✔</span></span>
<span data-ttu-id="e48d5-419">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="e48d5-419">LineString.IsClosed</span></span> | <span data-ttu-id="e48d5-420">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-420">✔</span></span> | <span data-ttu-id="e48d5-421">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-421">✔</span></span> | <span data-ttu-id="e48d5-422">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-422">✔</span></span> | <span data-ttu-id="e48d5-423">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-423">✔</span></span>
<span data-ttu-id="e48d5-424">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="e48d5-424">LineString.IsRing</span></span> | <span data-ttu-id="e48d5-425">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-425">✔</span></span> | | <span data-ttu-id="e48d5-426">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-426">✔</span></span> | <span data-ttu-id="e48d5-427">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-427">✔</span></span>
<span data-ttu-id="e48d5-428">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="e48d5-428">LineString.StartPoint</span></span> | <span data-ttu-id="e48d5-429">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-429">✔</span></span> | <span data-ttu-id="e48d5-430">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-430">✔</span></span> | <span data-ttu-id="e48d5-431">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-431">✔</span></span> | <span data-ttu-id="e48d5-432">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-432">✔</span></span>
<span data-ttu-id="e48d5-433">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="e48d5-433">MultiLineString.IsClosed</span></span> | <span data-ttu-id="e48d5-434">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-434">✔</span></span> | <span data-ttu-id="e48d5-435">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-435">✔</span></span> | <span data-ttu-id="e48d5-436">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-436">✔</span></span> | <span data-ttu-id="e48d5-437">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-437">✔</span></span>
<span data-ttu-id="e48d5-438">点 M</span><span class="sxs-lookup"><span data-stu-id="e48d5-438">Point.M</span></span> | <span data-ttu-id="e48d5-439">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-439">✔</span></span> | <span data-ttu-id="e48d5-440">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-440">✔</span></span> | <span data-ttu-id="e48d5-441">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-441">✔</span></span> | <span data-ttu-id="e48d5-442">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-442">✔</span></span>
<span data-ttu-id="e48d5-443">点 X</span><span class="sxs-lookup"><span data-stu-id="e48d5-443">Point.X</span></span> | <span data-ttu-id="e48d5-444">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-444">✔</span></span> | <span data-ttu-id="e48d5-445">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-445">✔</span></span> | <span data-ttu-id="e48d5-446">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-446">✔</span></span> | <span data-ttu-id="e48d5-447">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-447">✔</span></span>
<span data-ttu-id="e48d5-448">Point。 Y</span><span class="sxs-lookup"><span data-stu-id="e48d5-448">Point.Y</span></span> | <span data-ttu-id="e48d5-449">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-449">✔</span></span> | <span data-ttu-id="e48d5-450">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-450">✔</span></span> | <span data-ttu-id="e48d5-451">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-451">✔</span></span> | <span data-ttu-id="e48d5-452">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-452">✔</span></span>
<span data-ttu-id="e48d5-453">点 Z</span><span class="sxs-lookup"><span data-stu-id="e48d5-453">Point.Z</span></span> | <span data-ttu-id="e48d5-454">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-454">✔</span></span> | <span data-ttu-id="e48d5-455">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-455">✔</span></span> | <span data-ttu-id="e48d5-456">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-456">✔</span></span> | <span data-ttu-id="e48d5-457">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-457">✔</span></span>
<span data-ttu-id="e48d5-458">多边形。 ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="e48d5-458">Polygon.ExteriorRing</span></span> | <span data-ttu-id="e48d5-459">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-459">✔</span></span> | <span data-ttu-id="e48d5-460">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-460">✔</span></span> | <span data-ttu-id="e48d5-461">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-461">✔</span></span> | <span data-ttu-id="e48d5-462">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-462">✔</span></span>
<span data-ttu-id="e48d5-463">GetInteriorRingN （int）</span><span class="sxs-lookup"><span data-stu-id="e48d5-463">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="e48d5-464">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-464">✔</span></span> | <span data-ttu-id="e48d5-465">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-465">✔</span></span> | <span data-ttu-id="e48d5-466">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-466">✔</span></span> | <span data-ttu-id="e48d5-467">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-467">✔</span></span>
<span data-ttu-id="e48d5-468">多边形。 NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="e48d5-468">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="e48d5-469">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-469">✔</span></span> | <span data-ttu-id="e48d5-470">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-470">✔</span></span> | <span data-ttu-id="e48d5-471">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-471">✔</span></span> | <span data-ttu-id="e48d5-472">✔</span><span class="sxs-lookup"><span data-stu-id="e48d5-472">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e48d5-473">其他资源</span><span class="sxs-lookup"><span data-stu-id="e48d5-473">Additional resources</span></span>

* [<span data-ttu-id="e48d5-474">SQL Server 中的空间数据</span><span class="sxs-lookup"><span data-stu-id="e48d5-474">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="e48d5-475">SpatiaLite 主页</span><span class="sxs-lookup"><span data-stu-id="e48d5-475">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="e48d5-476">Npgsql 空间文档</span><span class="sxs-lookup"><span data-stu-id="e48d5-476">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="e48d5-477">PostGIS 文档</span><span class="sxs-lookup"><span data-stu-id="e48d5-477">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
