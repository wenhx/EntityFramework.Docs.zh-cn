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
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="c3c53-103">SQLite EF Core 提供程序中的空间数据</span><span class="sxs-lookup"><span data-stu-id="c3c53-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="c3c53-104">此页包含有关将空间数据与 SQLite 数据库提供程序一起使用的其他信息。</span><span class="sxs-lookup"><span data-stu-id="c3c53-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="c3c53-105">有关在 EF Core 中使用空间数据的常规信息，请参阅主 [空间数据](xref:core/modeling/spatial) 文档。</span><span class="sxs-lookup"><span data-stu-id="c3c53-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="c3c53-106">安装 SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="c3c53-106">Installing SpatiaLite</span></span>

<span data-ttu-id="c3c53-107">在 Windows 上，本机 mod_spatialite 库以 NuGet 包的依赖项的形式分发。</span><span class="sxs-lookup"><span data-stu-id="c3c53-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="c3c53-108">其他平台需要单独安装它。</span><span class="sxs-lookup"><span data-stu-id="c3c53-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="c3c53-109">通常使用软件程序包管理器完成此操作。</span><span class="sxs-lookup"><span data-stu-id="c3c53-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="c3c53-110">例如，可以在 Debian 和 Ubuntu 上使用 APT;MacOS 上的和 Homebrew。</span><span class="sxs-lookup"><span data-stu-id="c3c53-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="c3c53-111">遗憾的是，较新版本的 PROJ (SpatiaLite) 的依赖项与 EF 的默认 [SQLitePCLRaw 绑定](/dotnet/standard/data/sqlite/custom-versions#bundles)不兼容。</span><span class="sxs-lookup"><span data-stu-id="c3c53-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="c3c53-112">您可以通过使用系统 SQLite 库来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="c3c53-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="c3c53-113">在 **macOS**上，你还需要在运行应用之前设置环境变量，使其使用 Homebrew 版本的 SQLite。</span><span class="sxs-lookup"><span data-stu-id="c3c53-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="c3c53-114">在 Visual Studio for Mac 中，你可以在项目 > 项目选项下设置此项， **> 运行 > 配置 > 默认值**</span><span class="sxs-lookup"><span data-stu-id="c3c53-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="c3c53-115">配置 SRID</span><span class="sxs-lookup"><span data-stu-id="c3c53-115">Configuring SRID</span></span>

<span data-ttu-id="c3c53-116">在 SpatiaLite 中，列需要为每个列指定一个 SRID。</span><span class="sxs-lookup"><span data-stu-id="c3c53-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="c3c53-117">默认的 SRID 为 `0` 。</span><span class="sxs-lookup"><span data-stu-id="c3c53-117">The default SRID is `0`.</span></span> <span data-ttu-id="c3c53-118">使用 ForSqliteHasSrid 方法指定其他 SRID。</span><span class="sxs-lookup"><span data-stu-id="c3c53-118">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="c3c53-119">4326指的是 WGS 84，是 GPS 和其他地理系统中使用的标准。</span><span class="sxs-lookup"><span data-stu-id="c3c53-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="c3c53-120">维度</span><span class="sxs-lookup"><span data-stu-id="c3c53-120">Dimension</span></span>

<span data-ttu-id="c3c53-121">列 (或坐标) 的默认维度为 X 和 Y。若要启用其他坐标，如 Z 或 M，请配置列类型。</span><span class="sxs-lookup"><span data-stu-id="c3c53-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="c3c53-122">空间函数映射</span><span class="sxs-lookup"><span data-stu-id="c3c53-122">Spatial function mappings</span></span>

<span data-ttu-id="c3c53-123">此表显示哪些 [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) 成员转换为哪些 SQL 函数。</span><span class="sxs-lookup"><span data-stu-id="c3c53-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="c3c53-124">.NET</span><span class="sxs-lookup"><span data-stu-id="c3c53-124">.NET</span></span>                                        | <span data-ttu-id="c3c53-125">SQL</span><span class="sxs-lookup"><span data-stu-id="c3c53-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="c3c53-126">geometry.图</span><span class="sxs-lookup"><span data-stu-id="c3c53-126">geometry.Area</span></span>                               | <span data-ttu-id="c3c53-127">区域 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-127">Area(@geometry)</span></span>
<span data-ttu-id="c3c53-128">geometry.AsBinary ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="c3c53-129">AsBinary (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="c3c53-130">geometry.AsText ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-130">geometry.AsText()</span></span>                           | <span data-ttu-id="c3c53-131">AsText (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-131">AsText(@geometry)</span></span>
<span data-ttu-id="c3c53-132">geometry.边缘</span><span class="sxs-lookup"><span data-stu-id="c3c53-132">geometry.Boundary</span></span>                           | <span data-ttu-id="c3c53-133">边界 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-133">Boundary(@geometry)</span></span>
<span data-ttu-id="c3c53-134">geometry.缓冲区 (距离) </span><span class="sxs-lookup"><span data-stu-id="c3c53-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="c3c53-135">缓冲区 (@geometry ， @distance) </span><span class="sxs-lookup"><span data-stu-id="c3c53-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="c3c53-136">geometry.缓冲区 (距离，quadrantSegments) </span><span class="sxs-lookup"><span data-stu-id="c3c53-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="c3c53-137">Buffer (@geometry 、 @distance @quadrantSegments) </span><span class="sxs-lookup"><span data-stu-id="c3c53-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="c3c53-138">geometry.质心</span><span class="sxs-lookup"><span data-stu-id="c3c53-138">geometry.Centroid</span></span>                           | <span data-ttu-id="c3c53-139">质心 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-139">Centroid(@geometry)</span></span>
<span data-ttu-id="c3c53-140">geometry.包含 (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="c3c53-141">包含 (@geometry 、 @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-142">geometry.ConvexHull ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="c3c53-143">ConvexHull (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="c3c53-144">geometry.CoveredBy (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="c3c53-145">CoveredBy (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-146">geometry.涵盖 (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="c3c53-147">介绍 (@geometry 、 @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-148">geometry.交叉 (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="c3c53-149">跨 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-150">geometry. (其他) 的差异</span><span class="sxs-lookup"><span data-stu-id="c3c53-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="c3c53-151">差异 (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="c3c53-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="c3c53-152">geometry.维数</span><span class="sxs-lookup"><span data-stu-id="c3c53-152">geometry.Dimension</span></span>                          | <span data-ttu-id="c3c53-153">维度 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-153">Dimension(@geometry)</span></span>
<span data-ttu-id="c3c53-154">geometry.不连续的 (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="c3c53-155">不连续 @geometry 的 (， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-156">geometry.G (距离) </span><span class="sxs-lookup"><span data-stu-id="c3c53-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="c3c53-157">距离 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-158">geometry.号</span><span class="sxs-lookup"><span data-stu-id="c3c53-158">geometry.Envelope</span></span>                           | <span data-ttu-id="c3c53-159">信封 (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-159">Envelope(@geometry)</span></span>
<span data-ttu-id="c3c53-160">geometry.EqualsTopologically (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="c3c53-161">等于 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-162">geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="c3c53-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="c3c53-163">GeometryType (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="c3c53-164">geometry.GetGeometryN (n) </span><span class="sxs-lookup"><span data-stu-id="c3c53-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="c3c53-165">GeometryN (@geometry ， @n + 1) </span><span class="sxs-lookup"><span data-stu-id="c3c53-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="c3c53-166">geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="c3c53-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="c3c53-167">PointOnSurface (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="c3c53-168">geometry.其他) 的交集 (</span><span class="sxs-lookup"><span data-stu-id="c3c53-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="c3c53-169">交集 (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="c3c53-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="c3c53-170">geometry. (g 的交集) </span><span class="sxs-lookup"><span data-stu-id="c3c53-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="c3c53-171"> (@geometry 的交集， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-172">geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="c3c53-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="c3c53-173">IsEmpty (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="c3c53-174">geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="c3c53-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="c3c53-175">IsSimple (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="c3c53-176">geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="c3c53-176">geometry.IsValid</span></span>                            | <span data-ttu-id="c3c53-177">IsValid (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-177">IsValid(@geometry)</span></span>
<span data-ttu-id="c3c53-178">geometry.IsWithinDistance (几何、距离) </span><span class="sxs-lookup"><span data-stu-id="c3c53-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="c3c53-179">距离 (@geometry ， @geom) <= @distance</span><span class="sxs-lookup"><span data-stu-id="c3c53-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="c3c53-180">geometry.长短</span><span class="sxs-lookup"><span data-stu-id="c3c53-180">geometry.Length</span></span>                             | <span data-ttu-id="c3c53-181">GLength (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-181">GLength(@geometry)</span></span>
<span data-ttu-id="c3c53-182">geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="c3c53-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="c3c53-183">NumGeometries (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="c3c53-184">geometry.X.numpoints</span><span class="sxs-lookup"><span data-stu-id="c3c53-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="c3c53-185">X.numpoints (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="c3c53-186">geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="c3c53-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="c3c53-187">CASE GeometryType (@geometry) "POINT" THEN .。。端面</span><span class="sxs-lookup"><span data-stu-id="c3c53-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="c3c53-188">geometry. (g) 重叠</span><span class="sxs-lookup"><span data-stu-id="c3c53-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="c3c53-189">重叠 (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-190">geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="c3c53-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="c3c53-191">PointOnSurface (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="c3c53-192">geometry.关联 (g，intersectionPattern) </span><span class="sxs-lookup"><span data-stu-id="c3c53-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="c3c53-193">关联 (@geometry 、 @g @intersectionPattern) </span><span class="sxs-lookup"><span data-stu-id="c3c53-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="c3c53-194">geometry.反向 ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="c3c53-195">ST_Reverse (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="c3c53-196">geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="c3c53-196">geometry.SRID</span></span>                               | <span data-ttu-id="c3c53-197">SRID (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-197">SRID(@geometry)</span></span>
<span data-ttu-id="c3c53-198">geometry.SymmetricDifference (其他) </span><span class="sxs-lookup"><span data-stu-id="c3c53-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="c3c53-199">SymDifference (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="c3c53-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="c3c53-200">geometry.ToBinary ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="c3c53-201">AsBinary (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="c3c53-202">geometry.ToText ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-202">geometry.ToText()</span></span>                           | <span data-ttu-id="c3c53-203">AsText (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-203">AsText(@geometry)</span></span>
<span data-ttu-id="c3c53-204">geometry. (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="c3c53-205"> (@geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-206">geometry.联合 ( # A1</span><span class="sxs-lookup"><span data-stu-id="c3c53-206">geometry.Union()</span></span>                            | <span data-ttu-id="c3c53-207">UnaryUnion (@geometry) </span><span class="sxs-lookup"><span data-stu-id="c3c53-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="c3c53-208">geometry.联合 (其他) </span><span class="sxs-lookup"><span data-stu-id="c3c53-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="c3c53-209">GUnion (@geometry ， @other) </span><span class="sxs-lookup"><span data-stu-id="c3c53-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="c3c53-210">geometry. (g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="c3c53-211">在 (中 @geometry ， @g) </span><span class="sxs-lookup"><span data-stu-id="c3c53-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="c3c53-212">geometryCollection [i]</span><span class="sxs-lookup"><span data-stu-id="c3c53-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="c3c53-213">GeometryN (@geometryCollection ， @i + 1) </span><span class="sxs-lookup"><span data-stu-id="c3c53-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="c3c53-214">geometryCollection</span><span class="sxs-lookup"><span data-stu-id="c3c53-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="c3c53-215">NumGeometries (@geometryCollection) </span><span class="sxs-lookup"><span data-stu-id="c3c53-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="c3c53-216">lineString</span><span class="sxs-lookup"><span data-stu-id="c3c53-216">lineString.Count</span></span>                            | <span data-ttu-id="c3c53-217">X.numpoints (@lineString) </span><span class="sxs-lookup"><span data-stu-id="c3c53-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="c3c53-218">lineString 终结点</span><span class="sxs-lookup"><span data-stu-id="c3c53-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="c3c53-219">终结点 (@lineString) </span><span class="sxs-lookup"><span data-stu-id="c3c53-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="c3c53-220">lineString. GetPointN (n) </span><span class="sxs-lookup"><span data-stu-id="c3c53-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="c3c53-221">PointN (@lineString ， @n + 1) </span><span class="sxs-lookup"><span data-stu-id="c3c53-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="c3c53-222">lineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="c3c53-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="c3c53-223">IsClosed (@lineString) </span><span class="sxs-lookup"><span data-stu-id="c3c53-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="c3c53-224">lineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="c3c53-224">lineString.IsRing</span></span>                           | <span data-ttu-id="c3c53-225">IsRing (@lineString) </span><span class="sxs-lookup"><span data-stu-id="c3c53-225">IsRing(@lineString)</span></span>
<span data-ttu-id="c3c53-226">lineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="c3c53-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="c3c53-227">StartPoint (@lineString) </span><span class="sxs-lookup"><span data-stu-id="c3c53-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="c3c53-228">multiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="c3c53-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="c3c53-229">IsClosed (@multiLineString) </span><span class="sxs-lookup"><span data-stu-id="c3c53-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="c3c53-230">情况.年</span><span class="sxs-lookup"><span data-stu-id="c3c53-230">point.M</span></span>                                     | <span data-ttu-id="c3c53-231">M (@point) </span><span class="sxs-lookup"><span data-stu-id="c3c53-231">M(@point)</span></span>
<span data-ttu-id="c3c53-232">情况.X-blade</span><span class="sxs-lookup"><span data-stu-id="c3c53-232">point.X</span></span>                                     | <span data-ttu-id="c3c53-233">X (@point) </span><span class="sxs-lookup"><span data-stu-id="c3c53-233">X(@point)</span></span>
<span data-ttu-id="c3c53-234">情况.误差</span><span class="sxs-lookup"><span data-stu-id="c3c53-234">point.Y</span></span>                                     | <span data-ttu-id="c3c53-235">Y (@point) </span><span class="sxs-lookup"><span data-stu-id="c3c53-235">Y(@point)</span></span>
<span data-ttu-id="c3c53-236">情况.Z</span><span class="sxs-lookup"><span data-stu-id="c3c53-236">point.Z</span></span>                                     | <span data-ttu-id="c3c53-237">Z (@point) </span><span class="sxs-lookup"><span data-stu-id="c3c53-237">Z(@point)</span></span>
<span data-ttu-id="c3c53-238">各.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="c3c53-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="c3c53-239">ExteriorRing (@polygon) </span><span class="sxs-lookup"><span data-stu-id="c3c53-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="c3c53-240">各.GetInteriorRingN (n) </span><span class="sxs-lookup"><span data-stu-id="c3c53-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="c3c53-241">InteriorRingN (@polygon ， @n + 1) </span><span class="sxs-lookup"><span data-stu-id="c3c53-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="c3c53-242">各.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="c3c53-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="c3c53-243">NumInteriorRing (@polygon) </span><span class="sxs-lookup"><span data-stu-id="c3c53-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3c53-244">其他资源</span><span class="sxs-lookup"><span data-stu-id="c3c53-244">Additional resources</span></span>

* [<span data-ttu-id="c3c53-245">SpatiaLite 主页</span><span class="sxs-lookup"><span data-stu-id="c3c53-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="c3c53-246">NetTopologySuite 文档</span><span class="sxs-lookup"><span data-stu-id="c3c53-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
