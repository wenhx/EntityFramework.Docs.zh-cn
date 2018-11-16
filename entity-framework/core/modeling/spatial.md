---
title: 空间数据-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 49c18758af2f2383ea082ead2f6df4c022152b36
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688781"
---
# <a name="spatial-data"></a><span data-ttu-id="48946-102">空间数据</span><span class="sxs-lookup"><span data-stu-id="48946-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="48946-103">此功能是 EF Core 2.2 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="48946-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="48946-104">空间数据表示的物理位置和形状的对象。</span><span class="sxs-lookup"><span data-stu-id="48946-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="48946-105">多个数据库对于此类型的数据提供支持，因此可编制索引和查询以及其他数据。</span><span class="sxs-lookup"><span data-stu-id="48946-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="48946-106">常见方案包括从一个位置，给定距离内的对象的查询或选择其边框包含给定的位置的对象。</span><span class="sxs-lookup"><span data-stu-id="48946-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="48946-107">EF Core 支持使用空间数据类型映射[NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)空间库。</span><span class="sxs-lookup"><span data-stu-id="48946-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="48946-108">安装</span><span class="sxs-lookup"><span data-stu-id="48946-108">Installing</span></span>

<span data-ttu-id="48946-109">若要使用 EF Core 使用空间数据，您需要安装相应的支持 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="48946-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="48946-110">需要安装哪些程序包依赖于正在使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="48946-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="48946-111">EF Core 提供程序</span><span class="sxs-lookup"><span data-stu-id="48946-111">EF Core Provider</span></span>                        | <span data-ttu-id="48946-112">空间 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="48946-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="48946-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="48946-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="48946-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="48946-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="48946-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="48946-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="48946-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="48946-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="48946-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="48946-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="48946-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="48946-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="48946-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="48946-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="48946-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="48946-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="48946-121">反向工程</span><span class="sxs-lookup"><span data-stu-id="48946-121">Reverse engineering</span></span>

<span data-ttu-id="48946-122">空间 NuGet 包还启用[反向工程](../managing-schemas/scaffolding.md)模型空间属性，但你需要安装包***之前***运行`Scaffold-DbContext`或`dotnet ef dbcontext scaffold`。</span><span class="sxs-lookup"><span data-stu-id="48946-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="48946-123">如果不这样做，你将收到有关未找到类型映射的列的警告，并将跳过列。</span><span class="sxs-lookup"><span data-stu-id="48946-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="48946-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="48946-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="48946-125">NetTopologySuite 是用于.NET 的空间库。</span><span class="sxs-lookup"><span data-stu-id="48946-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="48946-126">EF Core，映射到空间数据的数据库中的类型在模型中使用 NTS 类型。</span><span class="sxs-lookup"><span data-stu-id="48946-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="48946-127">若要启用通过 NTS 空间类型的映射，请对提供程序的 DbContext 选项生成器调用 UseNetTopologySuite 方法。</span><span class="sxs-lookup"><span data-stu-id="48946-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="48946-128">例如，与 SQL Server 您会这样调用它。</span><span class="sxs-lookup"><span data-stu-id="48946-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="48946-129">有几种空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="48946-129">There are several spatial data types.</span></span> <span data-ttu-id="48946-130">使用哪种类型取决于你想要允许的形状的类型。</span><span class="sxs-lookup"><span data-stu-id="48946-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="48946-131">下面是可用于在模型中的属性的 NTS 类型的层次结构。</span><span class="sxs-lookup"><span data-stu-id="48946-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="48946-132">它们位于`NetTopologySuite.Geometries`命名空间。</span><span class="sxs-lookup"><span data-stu-id="48946-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="48946-133">GeoAPI 包中的相应接口 (`GeoAPI.Geometries`命名空间) 也可用。</span><span class="sxs-lookup"><span data-stu-id="48946-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="48946-134">geometry</span><span class="sxs-lookup"><span data-stu-id="48946-134">Geometry</span></span>
  * <span data-ttu-id="48946-135">点</span><span class="sxs-lookup"><span data-stu-id="48946-135">Point</span></span>
  * <span data-ttu-id="48946-136">LineString</span><span class="sxs-lookup"><span data-stu-id="48946-136">LineString</span></span>
  * <span data-ttu-id="48946-137">多边形</span><span class="sxs-lookup"><span data-stu-id="48946-137">Polygon</span></span>
  * <span data-ttu-id="48946-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="48946-138">GeometryCollection</span></span>
    * <span data-ttu-id="48946-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="48946-139">MultiPoint</span></span>
    * <span data-ttu-id="48946-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="48946-140">MultiLineString</span></span>
    * <span data-ttu-id="48946-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="48946-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="48946-142">不支持 NTS CircularString、 CompoundCurve、 和 CurePolygon。</span><span class="sxs-lookup"><span data-stu-id="48946-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="48946-143">使用基本的 Geometry 类型允许任何类型的形状中，以指定的属性。</span><span class="sxs-lookup"><span data-stu-id="48946-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="48946-144">可以使用以下实体类将映射到表中[Wide World Importers 示例数据库](http://go.microsoft.com/fwlink/?LinkID=800630)。</span><span class="sxs-lookup"><span data-stu-id="48946-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public IPoint Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public IGeometry Border { get; set; }
}
```

### <a name="creating-values"></a><span data-ttu-id="48946-145">创建值</span><span class="sxs-lookup"><span data-stu-id="48946-145">Creating values</span></span>

<span data-ttu-id="48946-146">您可以使用构造函数创建 geometry 对象;但是，NTS 建议改为使用 geometry 工厂。</span><span class="sxs-lookup"><span data-stu-id="48946-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="48946-147">由此你可以指定默认 SRID （所使用的坐标空间引用系统），并为您提供了控制精度模型 （在计算过程中使用） 和坐标序列 （确定哪些坐标-维度等更高级的功能和度量值-是可用）。</span><span class="sxs-lookup"><span data-stu-id="48946-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="48946-148">4326 指 WGS 84，GPS 和其他地理系统中使用的标准。</span><span class="sxs-lookup"><span data-stu-id="48946-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="48946-149">经度和纬度</span><span class="sxs-lookup"><span data-stu-id="48946-149">Longitude and Latitude</span></span>

<span data-ttu-id="48946-150">在 NTS 坐标都的 X 和 Y 值。</span><span class="sxs-lookup"><span data-stu-id="48946-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="48946-151">若要表示经度和纬度，经度和 Y 的纬度使用 X。</span><span class="sxs-lookup"><span data-stu-id="48946-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="48946-152">请注意，这是**向后**从`latitude, longitude`通常看到这些值的格式。</span><span class="sxs-lookup"><span data-stu-id="48946-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="48946-153">在客户端操作期间忽略的 SRID</span><span class="sxs-lookup"><span data-stu-id="48946-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="48946-154">NTS 在操作期间忽略 SRID 值。</span><span class="sxs-lookup"><span data-stu-id="48946-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="48946-155">它假定平面坐标系统。</span><span class="sxs-lookup"><span data-stu-id="48946-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="48946-156">这意味着，如果指定经度和纬度，距离、 长度和区域将以度为单位计量不像某些客户端评估值方面的坐标。</span><span class="sxs-lookup"><span data-stu-id="48946-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="48946-157">对于更有意义的值，首先需要项目到另一个使用等库的坐标系统的坐标[ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI)之前计算这些值。</span><span class="sxs-lookup"><span data-stu-id="48946-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="48946-158">如果某个操作是由 EF Core 通过 SQL server 评估，将由数据库决定结果的单位。</span><span class="sxs-lookup"><span data-stu-id="48946-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="48946-159">下面是使用 ProjNet4GeoAPI 计算两个城市之间的距离的示例。</span><span class="sxs-lookup"><span data-stu-id="48946-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

``` csharp
static class GeometryExtensions
{
    static readonly IGeometryServices _geometryServices = NtsGeometryServices.Instance;
    static readonly ICoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                // (3857 and 4326 included automatically)

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

    public static IGeometry ProjectTo(this IGeometry geometry, int srid)
    {
        var geometryFactory = _geometryServices.CreateGeometryFactory(srid);
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        return GeometryTransform.TransformGeometry(
            geometryFactory,
            geometry,
            transformation.MathTransform);
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a><span data-ttu-id="48946-160">查询数据</span><span class="sxs-lookup"><span data-stu-id="48946-160">Querying Data</span></span>

<span data-ttu-id="48946-161">在 LINQ 中，NTS 方法和属性可用作数据库函数都将转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="48946-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="48946-162">例如，下列查询中转换的距离和 Contains 方法。</span><span class="sxs-lookup"><span data-stu-id="48946-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="48946-163">本文结束时表显示了所支持的各种 EF Core 提供程序的成员。</span><span class="sxs-lookup"><span data-stu-id="48946-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="48946-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="48946-164">SQL Server</span></span>

<span data-ttu-id="48946-165">如果您使用 SQL Server，有一些其他操作，您应注意。</span><span class="sxs-lookup"><span data-stu-id="48946-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="48946-166">Geography 或 geometry</span><span class="sxs-lookup"><span data-stu-id="48946-166">Geography or geometry</span></span>

<span data-ttu-id="48946-167">默认情况下，空间属性映射到`geography`SQL Server 中的列。</span><span class="sxs-lookup"><span data-stu-id="48946-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="48946-168">若要使用`geometry`，[配置的列类型](xref:core/modeling/relational/data-types)在模型中。</span><span class="sxs-lookup"><span data-stu-id="48946-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="48946-169">地理多边形环</span><span class="sxs-lookup"><span data-stu-id="48946-169">Geography polygon rings</span></span>

<span data-ttu-id="48946-170">当使用`geography`列类型，SQL Server 将对施加其他要求的外部环 （或命令行程序） 和内部环 （或漏洞）。</span><span class="sxs-lookup"><span data-stu-id="48946-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="48946-171">外部环必须为方向逆时针和内环顺时针旋转。</span><span class="sxs-lookup"><span data-stu-id="48946-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="48946-172">NTS 将值发送到数据库之前会验证此。</span><span class="sxs-lookup"><span data-stu-id="48946-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="48946-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="48946-173">FullGlobe</span></span>

<span data-ttu-id="48946-174">SQL Server 具有非标准的几何图形类型来表示整个地球时使用`geography`列类型。</span><span class="sxs-lookup"><span data-stu-id="48946-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="48946-175">它还具有一种方法来表示多边形根据 （而无需外部环） 完整的球形。</span><span class="sxs-lookup"><span data-stu-id="48946-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="48946-176">NTS 支持这两个命令。</span><span class="sxs-lookup"><span data-stu-id="48946-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="48946-177">不支持 NTS FullGlobe 和基于它的多边形。</span><span class="sxs-lookup"><span data-stu-id="48946-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="48946-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="48946-178">SQLite</span></span>

<span data-ttu-id="48946-179">下面是一些其他信息，这对于使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="48946-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="48946-180">安装 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="48946-180">Installing SpatiaLite</span></span>

<span data-ttu-id="48946-181">在 Windows 中，本机 mod_spatialite 库作为 NuGet 包依赖项分发。</span><span class="sxs-lookup"><span data-stu-id="48946-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="48946-182">其他平台需要单独安装。</span><span class="sxs-lookup"><span data-stu-id="48946-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="48946-183">这通常是使用软件程序包管理器。</span><span class="sxs-lookup"><span data-stu-id="48946-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="48946-184">例如，您可以在 Ubuntu 和 Homebrew 在 MacOS 上使用 APT。</span><span class="sxs-lookup"><span data-stu-id="48946-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="48946-185">配置 SRID</span><span class="sxs-lookup"><span data-stu-id="48946-185">Configuring SRID</span></span>

<span data-ttu-id="48946-186">在 SpatiaLite，需要指定每个列 SRID 列。</span><span class="sxs-lookup"><span data-stu-id="48946-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="48946-187">默认 SRID 为`0`。</span><span class="sxs-lookup"><span data-stu-id="48946-187">The default SRID is `0`.</span></span> <span data-ttu-id="48946-188">指定不同的 SRID 使用 ForSqliteHasSrid 方法。</span><span class="sxs-lookup"><span data-stu-id="48946-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="48946-189">维度</span><span class="sxs-lookup"><span data-stu-id="48946-189">Dimension</span></span>

<span data-ttu-id="48946-190">类似于 SRID，列的维度 （或坐标） 还指定为列的一部分。</span><span class="sxs-lookup"><span data-stu-id="48946-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="48946-191">默认坐标为 X 和 Y。 启用其他坐标 （Z 和 M） 使用 ForSqliteHasDimension 方法。</span><span class="sxs-lookup"><span data-stu-id="48946-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="48946-192">翻译后的操作</span><span class="sxs-lookup"><span data-stu-id="48946-192">Translated Operations</span></span>

<span data-ttu-id="48946-193">此表显示了哪些 NTS 成员的每个 EF Core 提供程序转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="48946-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="48946-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="48946-194">NetTopologySuite</span></span> | <span data-ttu-id="48946-195">SQL Server (geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-195">SQL Server (geometry)</span></span> | <span data-ttu-id="48946-196">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="48946-196">SQL Server (geography)</span></span> | <span data-ttu-id="48946-197">SQLite</span><span class="sxs-lookup"><span data-stu-id="48946-197">SQLite</span></span> | <span data-ttu-id="48946-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="48946-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="48946-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="48946-199">Geometry.Area</span></span> | <span data-ttu-id="48946-200">✔</span><span class="sxs-lookup"><span data-stu-id="48946-200">✔</span></span> | <span data-ttu-id="48946-201">✔</span><span class="sxs-lookup"><span data-stu-id="48946-201">✔</span></span> | <span data-ttu-id="48946-202">✔</span><span class="sxs-lookup"><span data-stu-id="48946-202">✔</span></span> | <span data-ttu-id="48946-203">✔</span><span class="sxs-lookup"><span data-stu-id="48946-203">✔</span></span>
<span data-ttu-id="48946-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="48946-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="48946-205">✔</span><span class="sxs-lookup"><span data-stu-id="48946-205">✔</span></span> | <span data-ttu-id="48946-206">✔</span><span class="sxs-lookup"><span data-stu-id="48946-206">✔</span></span> | <span data-ttu-id="48946-207">✔</span><span class="sxs-lookup"><span data-stu-id="48946-207">✔</span></span> | <span data-ttu-id="48946-208">✔</span><span class="sxs-lookup"><span data-stu-id="48946-208">✔</span></span>
<span data-ttu-id="48946-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="48946-209">Geometry.AsText()</span></span> | <span data-ttu-id="48946-210">✔</span><span class="sxs-lookup"><span data-stu-id="48946-210">✔</span></span> | <span data-ttu-id="48946-211">✔</span><span class="sxs-lookup"><span data-stu-id="48946-211">✔</span></span> | <span data-ttu-id="48946-212">✔</span><span class="sxs-lookup"><span data-stu-id="48946-212">✔</span></span> | <span data-ttu-id="48946-213">✔</span><span class="sxs-lookup"><span data-stu-id="48946-213">✔</span></span>
<span data-ttu-id="48946-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="48946-214">Geometry.Boundary</span></span> | <span data-ttu-id="48946-215">✔</span><span class="sxs-lookup"><span data-stu-id="48946-215">✔</span></span> | | <span data-ttu-id="48946-216">✔</span><span class="sxs-lookup"><span data-stu-id="48946-216">✔</span></span> | <span data-ttu-id="48946-217">✔</span><span class="sxs-lookup"><span data-stu-id="48946-217">✔</span></span>
<span data-ttu-id="48946-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="48946-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="48946-219">✔</span><span class="sxs-lookup"><span data-stu-id="48946-219">✔</span></span> | <span data-ttu-id="48946-220">✔</span><span class="sxs-lookup"><span data-stu-id="48946-220">✔</span></span> | <span data-ttu-id="48946-221">✔</span><span class="sxs-lookup"><span data-stu-id="48946-221">✔</span></span> | <span data-ttu-id="48946-222">✔</span><span class="sxs-lookup"><span data-stu-id="48946-222">✔</span></span>
<span data-ttu-id="48946-223">Geometry.Buffer (double，int)</span><span class="sxs-lookup"><span data-stu-id="48946-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="48946-224">✔</span><span class="sxs-lookup"><span data-stu-id="48946-224">✔</span></span>
<span data-ttu-id="48946-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="48946-225">Geometry.Centroid</span></span> | <span data-ttu-id="48946-226">✔</span><span class="sxs-lookup"><span data-stu-id="48946-226">✔</span></span> | | <span data-ttu-id="48946-227">✔</span><span class="sxs-lookup"><span data-stu-id="48946-227">✔</span></span> | <span data-ttu-id="48946-228">✔</span><span class="sxs-lookup"><span data-stu-id="48946-228">✔</span></span>
<span data-ttu-id="48946-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="48946-230">✔</span><span class="sxs-lookup"><span data-stu-id="48946-230">✔</span></span> | <span data-ttu-id="48946-231">✔</span><span class="sxs-lookup"><span data-stu-id="48946-231">✔</span></span> | <span data-ttu-id="48946-232">✔</span><span class="sxs-lookup"><span data-stu-id="48946-232">✔</span></span> | <span data-ttu-id="48946-233">✔</span><span class="sxs-lookup"><span data-stu-id="48946-233">✔</span></span>
<span data-ttu-id="48946-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="48946-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="48946-235">✔</span><span class="sxs-lookup"><span data-stu-id="48946-235">✔</span></span> | <span data-ttu-id="48946-236">✔</span><span class="sxs-lookup"><span data-stu-id="48946-236">✔</span></span> | <span data-ttu-id="48946-237">✔</span><span class="sxs-lookup"><span data-stu-id="48946-237">✔</span></span> | <span data-ttu-id="48946-238">✔</span><span class="sxs-lookup"><span data-stu-id="48946-238">✔</span></span>
<span data-ttu-id="48946-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="48946-240">✔</span><span class="sxs-lookup"><span data-stu-id="48946-240">✔</span></span> | <span data-ttu-id="48946-241">✔</span><span class="sxs-lookup"><span data-stu-id="48946-241">✔</span></span>
<span data-ttu-id="48946-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="48946-243">✔</span><span class="sxs-lookup"><span data-stu-id="48946-243">✔</span></span> | <span data-ttu-id="48946-244">✔</span><span class="sxs-lookup"><span data-stu-id="48946-244">✔</span></span>
<span data-ttu-id="48946-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="48946-246">✔</span><span class="sxs-lookup"><span data-stu-id="48946-246">✔</span></span> | | <span data-ttu-id="48946-247">✔</span><span class="sxs-lookup"><span data-stu-id="48946-247">✔</span></span> | <span data-ttu-id="48946-248">✔</span><span class="sxs-lookup"><span data-stu-id="48946-248">✔</span></span>
<span data-ttu-id="48946-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="48946-250">✔</span><span class="sxs-lookup"><span data-stu-id="48946-250">✔</span></span> | <span data-ttu-id="48946-251">✔</span><span class="sxs-lookup"><span data-stu-id="48946-251">✔</span></span> | <span data-ttu-id="48946-252">✔</span><span class="sxs-lookup"><span data-stu-id="48946-252">✔</span></span> | <span data-ttu-id="48946-253">✔</span><span class="sxs-lookup"><span data-stu-id="48946-253">✔</span></span>
<span data-ttu-id="48946-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="48946-254">Geometry.Dimension</span></span> | <span data-ttu-id="48946-255">✔</span><span class="sxs-lookup"><span data-stu-id="48946-255">✔</span></span> | <span data-ttu-id="48946-256">✔</span><span class="sxs-lookup"><span data-stu-id="48946-256">✔</span></span> | <span data-ttu-id="48946-257">✔</span><span class="sxs-lookup"><span data-stu-id="48946-257">✔</span></span> | <span data-ttu-id="48946-258">✔</span><span class="sxs-lookup"><span data-stu-id="48946-258">✔</span></span>
<span data-ttu-id="48946-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="48946-260">✔</span><span class="sxs-lookup"><span data-stu-id="48946-260">✔</span></span> | <span data-ttu-id="48946-261">✔</span><span class="sxs-lookup"><span data-stu-id="48946-261">✔</span></span> | <span data-ttu-id="48946-262">✔</span><span class="sxs-lookup"><span data-stu-id="48946-262">✔</span></span> | <span data-ttu-id="48946-263">✔</span><span class="sxs-lookup"><span data-stu-id="48946-263">✔</span></span>
<span data-ttu-id="48946-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="48946-265">✔</span><span class="sxs-lookup"><span data-stu-id="48946-265">✔</span></span> | <span data-ttu-id="48946-266">✔</span><span class="sxs-lookup"><span data-stu-id="48946-266">✔</span></span> | <span data-ttu-id="48946-267">✔</span><span class="sxs-lookup"><span data-stu-id="48946-267">✔</span></span> | <span data-ttu-id="48946-268">✔</span><span class="sxs-lookup"><span data-stu-id="48946-268">✔</span></span>
<span data-ttu-id="48946-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="48946-269">Geometry.Envelope</span></span> | <span data-ttu-id="48946-270">✔</span><span class="sxs-lookup"><span data-stu-id="48946-270">✔</span></span> | | <span data-ttu-id="48946-271">✔</span><span class="sxs-lookup"><span data-stu-id="48946-271">✔</span></span> | <span data-ttu-id="48946-272">✔</span><span class="sxs-lookup"><span data-stu-id="48946-272">✔</span></span>
<span data-ttu-id="48946-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="48946-274">✔</span><span class="sxs-lookup"><span data-stu-id="48946-274">✔</span></span>
<span data-ttu-id="48946-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="48946-276">✔</span><span class="sxs-lookup"><span data-stu-id="48946-276">✔</span></span> | <span data-ttu-id="48946-277">✔</span><span class="sxs-lookup"><span data-stu-id="48946-277">✔</span></span> | <span data-ttu-id="48946-278">✔</span><span class="sxs-lookup"><span data-stu-id="48946-278">✔</span></span> | <span data-ttu-id="48946-279">✔</span><span class="sxs-lookup"><span data-stu-id="48946-279">✔</span></span>
<span data-ttu-id="48946-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="48946-280">Geometry.GeometryType</span></span> | <span data-ttu-id="48946-281">✔</span><span class="sxs-lookup"><span data-stu-id="48946-281">✔</span></span> | <span data-ttu-id="48946-282">✔</span><span class="sxs-lookup"><span data-stu-id="48946-282">✔</span></span> | <span data-ttu-id="48946-283">✔</span><span class="sxs-lookup"><span data-stu-id="48946-283">✔</span></span> | <span data-ttu-id="48946-284">✔</span><span class="sxs-lookup"><span data-stu-id="48946-284">✔</span></span>
<span data-ttu-id="48946-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="48946-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="48946-286">✔</span><span class="sxs-lookup"><span data-stu-id="48946-286">✔</span></span> | | <span data-ttu-id="48946-287">✔</span><span class="sxs-lookup"><span data-stu-id="48946-287">✔</span></span> | <span data-ttu-id="48946-288">✔</span><span class="sxs-lookup"><span data-stu-id="48946-288">✔</span></span>
<span data-ttu-id="48946-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="48946-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="48946-290">✔</span><span class="sxs-lookup"><span data-stu-id="48946-290">✔</span></span> | | <span data-ttu-id="48946-291">✔</span><span class="sxs-lookup"><span data-stu-id="48946-291">✔</span></span>
<span data-ttu-id="48946-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="48946-293">✔</span><span class="sxs-lookup"><span data-stu-id="48946-293">✔</span></span> | <span data-ttu-id="48946-294">✔</span><span class="sxs-lookup"><span data-stu-id="48946-294">✔</span></span> | <span data-ttu-id="48946-295">✔</span><span class="sxs-lookup"><span data-stu-id="48946-295">✔</span></span> | <span data-ttu-id="48946-296">✔</span><span class="sxs-lookup"><span data-stu-id="48946-296">✔</span></span>
<span data-ttu-id="48946-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="48946-298">✔</span><span class="sxs-lookup"><span data-stu-id="48946-298">✔</span></span> | <span data-ttu-id="48946-299">✔</span><span class="sxs-lookup"><span data-stu-id="48946-299">✔</span></span> | <span data-ttu-id="48946-300">✔</span><span class="sxs-lookup"><span data-stu-id="48946-300">✔</span></span> | <span data-ttu-id="48946-301">✔</span><span class="sxs-lookup"><span data-stu-id="48946-301">✔</span></span>
<span data-ttu-id="48946-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="48946-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="48946-303">✔</span><span class="sxs-lookup"><span data-stu-id="48946-303">✔</span></span> | <span data-ttu-id="48946-304">✔</span><span class="sxs-lookup"><span data-stu-id="48946-304">✔</span></span> | <span data-ttu-id="48946-305">✔</span><span class="sxs-lookup"><span data-stu-id="48946-305">✔</span></span> | <span data-ttu-id="48946-306">✔</span><span class="sxs-lookup"><span data-stu-id="48946-306">✔</span></span>
<span data-ttu-id="48946-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="48946-307">Geometry.IsSimple</span></span> | <span data-ttu-id="48946-308">✔</span><span class="sxs-lookup"><span data-stu-id="48946-308">✔</span></span> | | <span data-ttu-id="48946-309">✔</span><span class="sxs-lookup"><span data-stu-id="48946-309">✔</span></span> | <span data-ttu-id="48946-310">✔</span><span class="sxs-lookup"><span data-stu-id="48946-310">✔</span></span>
<span data-ttu-id="48946-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="48946-311">Geometry.IsValid</span></span> | <span data-ttu-id="48946-312">✔</span><span class="sxs-lookup"><span data-stu-id="48946-312">✔</span></span> | <span data-ttu-id="48946-313">✔</span><span class="sxs-lookup"><span data-stu-id="48946-313">✔</span></span> | <span data-ttu-id="48946-314">✔</span><span class="sxs-lookup"><span data-stu-id="48946-314">✔</span></span> | <span data-ttu-id="48946-315">✔</span><span class="sxs-lookup"><span data-stu-id="48946-315">✔</span></span>
<span data-ttu-id="48946-316">Geometry.IsWithinDistance (Geometry，double)</span><span class="sxs-lookup"><span data-stu-id="48946-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="48946-317">✔</span><span class="sxs-lookup"><span data-stu-id="48946-317">✔</span></span> | | <span data-ttu-id="48946-318">✔</span><span class="sxs-lookup"><span data-stu-id="48946-318">✔</span></span>
<span data-ttu-id="48946-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="48946-319">Geometry.Length</span></span> | <span data-ttu-id="48946-320">✔</span><span class="sxs-lookup"><span data-stu-id="48946-320">✔</span></span> | <span data-ttu-id="48946-321">✔</span><span class="sxs-lookup"><span data-stu-id="48946-321">✔</span></span> | <span data-ttu-id="48946-322">✔</span><span class="sxs-lookup"><span data-stu-id="48946-322">✔</span></span> | <span data-ttu-id="48946-323">✔</span><span class="sxs-lookup"><span data-stu-id="48946-323">✔</span></span>
<span data-ttu-id="48946-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="48946-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="48946-325">✔</span><span class="sxs-lookup"><span data-stu-id="48946-325">✔</span></span> | <span data-ttu-id="48946-326">✔</span><span class="sxs-lookup"><span data-stu-id="48946-326">✔</span></span> | <span data-ttu-id="48946-327">✔</span><span class="sxs-lookup"><span data-stu-id="48946-327">✔</span></span> | <span data-ttu-id="48946-328">✔</span><span class="sxs-lookup"><span data-stu-id="48946-328">✔</span></span>
<span data-ttu-id="48946-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="48946-329">Geometry.NumPoints</span></span> | <span data-ttu-id="48946-330">✔</span><span class="sxs-lookup"><span data-stu-id="48946-330">✔</span></span> | <span data-ttu-id="48946-331">✔</span><span class="sxs-lookup"><span data-stu-id="48946-331">✔</span></span> | <span data-ttu-id="48946-332">✔</span><span class="sxs-lookup"><span data-stu-id="48946-332">✔</span></span> | <span data-ttu-id="48946-333">✔</span><span class="sxs-lookup"><span data-stu-id="48946-333">✔</span></span>
<span data-ttu-id="48946-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="48946-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="48946-335">✔</span><span class="sxs-lookup"><span data-stu-id="48946-335">✔</span></span> | <span data-ttu-id="48946-336">✔</span><span class="sxs-lookup"><span data-stu-id="48946-336">✔</span></span> | <span data-ttu-id="48946-337">✔</span><span class="sxs-lookup"><span data-stu-id="48946-337">✔</span></span>
<span data-ttu-id="48946-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="48946-339">✔</span><span class="sxs-lookup"><span data-stu-id="48946-339">✔</span></span> | <span data-ttu-id="48946-340">✔</span><span class="sxs-lookup"><span data-stu-id="48946-340">✔</span></span> | <span data-ttu-id="48946-341">✔</span><span class="sxs-lookup"><span data-stu-id="48946-341">✔</span></span> | <span data-ttu-id="48946-342">✔</span><span class="sxs-lookup"><span data-stu-id="48946-342">✔</span></span>
<span data-ttu-id="48946-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="48946-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="48946-344">✔</span><span class="sxs-lookup"><span data-stu-id="48946-344">✔</span></span> | | <span data-ttu-id="48946-345">✔</span><span class="sxs-lookup"><span data-stu-id="48946-345">✔</span></span> | <span data-ttu-id="48946-346">✔</span><span class="sxs-lookup"><span data-stu-id="48946-346">✔</span></span>
<span data-ttu-id="48946-347">Geometry.Relate (Geometry，字符串)</span><span class="sxs-lookup"><span data-stu-id="48946-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="48946-348">✔</span><span class="sxs-lookup"><span data-stu-id="48946-348">✔</span></span> | | <span data-ttu-id="48946-349">✔</span><span class="sxs-lookup"><span data-stu-id="48946-349">✔</span></span> | <span data-ttu-id="48946-350">✔</span><span class="sxs-lookup"><span data-stu-id="48946-350">✔</span></span>
<span data-ttu-id="48946-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="48946-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="48946-352">✔</span><span class="sxs-lookup"><span data-stu-id="48946-352">✔</span></span> | <span data-ttu-id="48946-353">✔</span><span class="sxs-lookup"><span data-stu-id="48946-353">✔</span></span>
<span data-ttu-id="48946-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="48946-354">Geometry.SRID</span></span> | <span data-ttu-id="48946-355">✔</span><span class="sxs-lookup"><span data-stu-id="48946-355">✔</span></span> | <span data-ttu-id="48946-356">✔</span><span class="sxs-lookup"><span data-stu-id="48946-356">✔</span></span> | <span data-ttu-id="48946-357">✔</span><span class="sxs-lookup"><span data-stu-id="48946-357">✔</span></span> | <span data-ttu-id="48946-358">✔</span><span class="sxs-lookup"><span data-stu-id="48946-358">✔</span></span>
<span data-ttu-id="48946-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="48946-360">✔</span><span class="sxs-lookup"><span data-stu-id="48946-360">✔</span></span> | <span data-ttu-id="48946-361">✔</span><span class="sxs-lookup"><span data-stu-id="48946-361">✔</span></span> | <span data-ttu-id="48946-362">✔</span><span class="sxs-lookup"><span data-stu-id="48946-362">✔</span></span> | <span data-ttu-id="48946-363">✔</span><span class="sxs-lookup"><span data-stu-id="48946-363">✔</span></span>
<span data-ttu-id="48946-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="48946-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="48946-365">✔</span><span class="sxs-lookup"><span data-stu-id="48946-365">✔</span></span> | <span data-ttu-id="48946-366">✔</span><span class="sxs-lookup"><span data-stu-id="48946-366">✔</span></span> | <span data-ttu-id="48946-367">✔</span><span class="sxs-lookup"><span data-stu-id="48946-367">✔</span></span> | <span data-ttu-id="48946-368">✔</span><span class="sxs-lookup"><span data-stu-id="48946-368">✔</span></span>
<span data-ttu-id="48946-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="48946-369">Geometry.ToText()</span></span> | <span data-ttu-id="48946-370">✔</span><span class="sxs-lookup"><span data-stu-id="48946-370">✔</span></span> | <span data-ttu-id="48946-371">✔</span><span class="sxs-lookup"><span data-stu-id="48946-371">✔</span></span> | <span data-ttu-id="48946-372">✔</span><span class="sxs-lookup"><span data-stu-id="48946-372">✔</span></span> | <span data-ttu-id="48946-373">✔</span><span class="sxs-lookup"><span data-stu-id="48946-373">✔</span></span>
<span data-ttu-id="48946-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="48946-375">✔</span><span class="sxs-lookup"><span data-stu-id="48946-375">✔</span></span> | | <span data-ttu-id="48946-376">✔</span><span class="sxs-lookup"><span data-stu-id="48946-376">✔</span></span> | <span data-ttu-id="48946-377">✔</span><span class="sxs-lookup"><span data-stu-id="48946-377">✔</span></span>
<span data-ttu-id="48946-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="48946-378">Geometry.Union()</span></span> | | | <span data-ttu-id="48946-379">✔</span><span class="sxs-lookup"><span data-stu-id="48946-379">✔</span></span>
<span data-ttu-id="48946-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="48946-381">✔</span><span class="sxs-lookup"><span data-stu-id="48946-381">✔</span></span> | <span data-ttu-id="48946-382">✔</span><span class="sxs-lookup"><span data-stu-id="48946-382">✔</span></span> | <span data-ttu-id="48946-383">✔</span><span class="sxs-lookup"><span data-stu-id="48946-383">✔</span></span> | <span data-ttu-id="48946-384">✔</span><span class="sxs-lookup"><span data-stu-id="48946-384">✔</span></span>
<span data-ttu-id="48946-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="48946-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="48946-386">✔</span><span class="sxs-lookup"><span data-stu-id="48946-386">✔</span></span> | <span data-ttu-id="48946-387">✔</span><span class="sxs-lookup"><span data-stu-id="48946-387">✔</span></span> | <span data-ttu-id="48946-388">✔</span><span class="sxs-lookup"><span data-stu-id="48946-388">✔</span></span> | <span data-ttu-id="48946-389">✔</span><span class="sxs-lookup"><span data-stu-id="48946-389">✔</span></span>
<span data-ttu-id="48946-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="48946-390">GeometryCollection.Count</span></span> | <span data-ttu-id="48946-391">✔</span><span class="sxs-lookup"><span data-stu-id="48946-391">✔</span></span> | <span data-ttu-id="48946-392">✔</span><span class="sxs-lookup"><span data-stu-id="48946-392">✔</span></span> | <span data-ttu-id="48946-393">✔</span><span class="sxs-lookup"><span data-stu-id="48946-393">✔</span></span> | <span data-ttu-id="48946-394">✔</span><span class="sxs-lookup"><span data-stu-id="48946-394">✔</span></span>
<span data-ttu-id="48946-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="48946-395">GeometryCollection[int]</span></span> | <span data-ttu-id="48946-396">✔</span><span class="sxs-lookup"><span data-stu-id="48946-396">✔</span></span> | <span data-ttu-id="48946-397">✔</span><span class="sxs-lookup"><span data-stu-id="48946-397">✔</span></span> | <span data-ttu-id="48946-398">✔</span><span class="sxs-lookup"><span data-stu-id="48946-398">✔</span></span> | <span data-ttu-id="48946-399">✔</span><span class="sxs-lookup"><span data-stu-id="48946-399">✔</span></span>
<span data-ttu-id="48946-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="48946-400">LineString.Count</span></span> | <span data-ttu-id="48946-401">✔</span><span class="sxs-lookup"><span data-stu-id="48946-401">✔</span></span> | <span data-ttu-id="48946-402">✔</span><span class="sxs-lookup"><span data-stu-id="48946-402">✔</span></span> | <span data-ttu-id="48946-403">✔</span><span class="sxs-lookup"><span data-stu-id="48946-403">✔</span></span> | <span data-ttu-id="48946-404">✔</span><span class="sxs-lookup"><span data-stu-id="48946-404">✔</span></span>
<span data-ttu-id="48946-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="48946-405">LineString.EndPoint</span></span> | <span data-ttu-id="48946-406">✔</span><span class="sxs-lookup"><span data-stu-id="48946-406">✔</span></span> | <span data-ttu-id="48946-407">✔</span><span class="sxs-lookup"><span data-stu-id="48946-407">✔</span></span> | <span data-ttu-id="48946-408">✔</span><span class="sxs-lookup"><span data-stu-id="48946-408">✔</span></span> | <span data-ttu-id="48946-409">✔</span><span class="sxs-lookup"><span data-stu-id="48946-409">✔</span></span>
<span data-ttu-id="48946-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="48946-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="48946-411">✔</span><span class="sxs-lookup"><span data-stu-id="48946-411">✔</span></span> | <span data-ttu-id="48946-412">✔</span><span class="sxs-lookup"><span data-stu-id="48946-412">✔</span></span> | <span data-ttu-id="48946-413">✔</span><span class="sxs-lookup"><span data-stu-id="48946-413">✔</span></span> | <span data-ttu-id="48946-414">✔</span><span class="sxs-lookup"><span data-stu-id="48946-414">✔</span></span>
<span data-ttu-id="48946-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="48946-415">LineString.IsClosed</span></span> | <span data-ttu-id="48946-416">✔</span><span class="sxs-lookup"><span data-stu-id="48946-416">✔</span></span> | <span data-ttu-id="48946-417">✔</span><span class="sxs-lookup"><span data-stu-id="48946-417">✔</span></span> | <span data-ttu-id="48946-418">✔</span><span class="sxs-lookup"><span data-stu-id="48946-418">✔</span></span> | <span data-ttu-id="48946-419">✔</span><span class="sxs-lookup"><span data-stu-id="48946-419">✔</span></span>
<span data-ttu-id="48946-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="48946-420">LineString.IsRing</span></span> | <span data-ttu-id="48946-421">✔</span><span class="sxs-lookup"><span data-stu-id="48946-421">✔</span></span> | | <span data-ttu-id="48946-422">✔</span><span class="sxs-lookup"><span data-stu-id="48946-422">✔</span></span> | <span data-ttu-id="48946-423">✔</span><span class="sxs-lookup"><span data-stu-id="48946-423">✔</span></span>
<span data-ttu-id="48946-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="48946-424">LineString.StartPoint</span></span> | <span data-ttu-id="48946-425">✔</span><span class="sxs-lookup"><span data-stu-id="48946-425">✔</span></span> | <span data-ttu-id="48946-426">✔</span><span class="sxs-lookup"><span data-stu-id="48946-426">✔</span></span> | <span data-ttu-id="48946-427">✔</span><span class="sxs-lookup"><span data-stu-id="48946-427">✔</span></span> | <span data-ttu-id="48946-428">✔</span><span class="sxs-lookup"><span data-stu-id="48946-428">✔</span></span>
<span data-ttu-id="48946-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="48946-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="48946-430">✔</span><span class="sxs-lookup"><span data-stu-id="48946-430">✔</span></span> | <span data-ttu-id="48946-431">✔</span><span class="sxs-lookup"><span data-stu-id="48946-431">✔</span></span> | <span data-ttu-id="48946-432">✔</span><span class="sxs-lookup"><span data-stu-id="48946-432">✔</span></span> | <span data-ttu-id="48946-433">✔</span><span class="sxs-lookup"><span data-stu-id="48946-433">✔</span></span>
<span data-ttu-id="48946-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="48946-434">Point.M</span></span> | <span data-ttu-id="48946-435">✔</span><span class="sxs-lookup"><span data-stu-id="48946-435">✔</span></span> | <span data-ttu-id="48946-436">✔</span><span class="sxs-lookup"><span data-stu-id="48946-436">✔</span></span> | <span data-ttu-id="48946-437">✔</span><span class="sxs-lookup"><span data-stu-id="48946-437">✔</span></span> | <span data-ttu-id="48946-438">✔</span><span class="sxs-lookup"><span data-stu-id="48946-438">✔</span></span>
<span data-ttu-id="48946-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="48946-439">Point.X</span></span> | <span data-ttu-id="48946-440">✔</span><span class="sxs-lookup"><span data-stu-id="48946-440">✔</span></span> | <span data-ttu-id="48946-441">✔</span><span class="sxs-lookup"><span data-stu-id="48946-441">✔</span></span> | <span data-ttu-id="48946-442">✔</span><span class="sxs-lookup"><span data-stu-id="48946-442">✔</span></span> | <span data-ttu-id="48946-443">✔</span><span class="sxs-lookup"><span data-stu-id="48946-443">✔</span></span>
<span data-ttu-id="48946-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="48946-444">Point.Y</span></span> | <span data-ttu-id="48946-445">✔</span><span class="sxs-lookup"><span data-stu-id="48946-445">✔</span></span> | <span data-ttu-id="48946-446">✔</span><span class="sxs-lookup"><span data-stu-id="48946-446">✔</span></span> | <span data-ttu-id="48946-447">✔</span><span class="sxs-lookup"><span data-stu-id="48946-447">✔</span></span> | <span data-ttu-id="48946-448">✔</span><span class="sxs-lookup"><span data-stu-id="48946-448">✔</span></span>
<span data-ttu-id="48946-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="48946-449">Point.Z</span></span> | <span data-ttu-id="48946-450">✔</span><span class="sxs-lookup"><span data-stu-id="48946-450">✔</span></span> | <span data-ttu-id="48946-451">✔</span><span class="sxs-lookup"><span data-stu-id="48946-451">✔</span></span> | <span data-ttu-id="48946-452">✔</span><span class="sxs-lookup"><span data-stu-id="48946-452">✔</span></span> | <span data-ttu-id="48946-453">✔</span><span class="sxs-lookup"><span data-stu-id="48946-453">✔</span></span>
<span data-ttu-id="48946-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="48946-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="48946-455">✔</span><span class="sxs-lookup"><span data-stu-id="48946-455">✔</span></span> | <span data-ttu-id="48946-456">✔</span><span class="sxs-lookup"><span data-stu-id="48946-456">✔</span></span> | <span data-ttu-id="48946-457">✔</span><span class="sxs-lookup"><span data-stu-id="48946-457">✔</span></span> | <span data-ttu-id="48946-458">✔</span><span class="sxs-lookup"><span data-stu-id="48946-458">✔</span></span>
<span data-ttu-id="48946-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="48946-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="48946-460">✔</span><span class="sxs-lookup"><span data-stu-id="48946-460">✔</span></span> | <span data-ttu-id="48946-461">✔</span><span class="sxs-lookup"><span data-stu-id="48946-461">✔</span></span> | <span data-ttu-id="48946-462">✔</span><span class="sxs-lookup"><span data-stu-id="48946-462">✔</span></span> | <span data-ttu-id="48946-463">✔</span><span class="sxs-lookup"><span data-stu-id="48946-463">✔</span></span>
<span data-ttu-id="48946-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="48946-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="48946-465">✔</span><span class="sxs-lookup"><span data-stu-id="48946-465">✔</span></span> | <span data-ttu-id="48946-466">✔</span><span class="sxs-lookup"><span data-stu-id="48946-466">✔</span></span> | <span data-ttu-id="48946-467">✔</span><span class="sxs-lookup"><span data-stu-id="48946-467">✔</span></span> | <span data-ttu-id="48946-468">✔</span><span class="sxs-lookup"><span data-stu-id="48946-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48946-469">其他资源</span><span class="sxs-lookup"><span data-stu-id="48946-469">Additional resources</span></span>

* [<span data-ttu-id="48946-470">SQL Server 中的空间数据</span><span class="sxs-lookup"><span data-stu-id="48946-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="48946-471">SpatiaLite 主页</span><span class="sxs-lookup"><span data-stu-id="48946-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="48946-472">PostGIS 文档</span><span class="sxs-lookup"><span data-stu-id="48946-472">PostGIS Documentation</span></span>](http://postgis.net/documentation/)
