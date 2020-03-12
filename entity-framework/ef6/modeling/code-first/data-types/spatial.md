---
title: 空间 Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
ms.openlocfilehash: 018f480c1f0f1e74fc9f7a8950a6880e96f1facc
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415783"
---
# <a name="spatial---code-first"></a><span data-ttu-id="ac3e9-102">空间 Code First</span><span class="sxs-lookup"><span data-stu-id="ac3e9-102">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="ac3e9-103">**EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="ac3e9-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="ac3e9-105">视频和分步演练演示了如何使用实体框架 Code First 映射空间类型。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-105">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="ac3e9-106">它还演示了如何使用 LINQ 查询查找两个位置之间的距离。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-106">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="ac3e9-107">本演练将使用 Code First 创建新数据库，但也可以使用[Code First 到现有数据库](~/ef6/modeling/code-first/workflows/existing-database.md)。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-107">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](~/ef6/modeling/code-first/workflows/existing-database.md).</span></span>

<span data-ttu-id="ac3e9-108">实体框架5中引入了空间类型支持。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-108">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="ac3e9-109">请注意，若要使用空间类型、枚举和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-109">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="ac3e9-110">默认情况下，Visual Studio 2012 面向 .NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="ac3e9-111">若要使用空间数据类型，还必须使用具有空间支持的实体框架提供程序。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-111">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="ac3e9-112">有关详细信息，请参阅[提供程序对空间类型的支持](~/ef6/fundamentals/providers/spatial-support.md)。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-112">See [provider support for spatial types](~/ef6/fundamentals/providers/spatial-support.md) for more information.</span></span>

<span data-ttu-id="ac3e9-113">主要的空间数据类型有两种：地理和几何。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-113">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="ac3e9-114">Geography 数据类型存储椭圆体的数据（例如 GPS 纬度和经度坐标）。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-114">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="ac3e9-115">Geometry 数据类型表示欧氏（平面）坐标系。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-115">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="ac3e9-116">观看视频</span><span class="sxs-lookup"><span data-stu-id="ac3e9-116">Watch the video</span></span>
<span data-ttu-id="ac3e9-117">此视频演示如何实体框架 Code First 映射空间类型。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-117">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="ac3e9-118">它还演示了如何使用 LINQ 查询查找两个位置之间的距离。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-118">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="ac3e9-119">**主讲人**：Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="ac3e9-119">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="ac3e9-120">**视频**：WMV | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="ac3e9-120">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="ac3e9-121">先决条件</span><span class="sxs-lookup"><span data-stu-id="ac3e9-121">Pre-Requisites</span></span>

<span data-ttu-id="ac3e9-122">你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-122">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="ac3e9-123">设置项目</span><span class="sxs-lookup"><span data-stu-id="ac3e9-123">Set up the Project</span></span>

1.  <span data-ttu-id="ac3e9-124">打开 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="ac3e9-124">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="ac3e9-125">在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"</span><span class="sxs-lookup"><span data-stu-id="ac3e9-125">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="ac3e9-126">在左窗格中，单击 " **Visual C\#** "，然后选择**控制台**模板</span><span class="sxs-lookup"><span data-stu-id="ac3e9-126">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="ac3e9-127">输入**SpatialCodeFirst**作为项目名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="ac3e9-127">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="ac3e9-128">使用 Code First 定义新模型</span><span class="sxs-lookup"><span data-stu-id="ac3e9-128">Define a New Model using Code First</span></span>

<span data-ttu-id="ac3e9-129">使用 Code First 开发时，通常首先编写定义概念（域）模型 .NET Framework 类。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-129">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="ac3e9-130">下面的代码定义了大学类。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-130">The code below defines the University class.</span></span>

<span data-ttu-id="ac3e9-131">该大学具有 DbGeography 类型的 Location 属性。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-131">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="ac3e9-132">若要使用 DbGeography 类型，必须添加对 System.web 程序集的引用，并使用语句添加 node.js。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-132">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="ac3e9-133">打开 Program.cs 文件，并将以下 using 语句粘贴到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="ac3e9-133">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="ac3e9-134">将以下大学类定义添加到 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-134">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="ac3e9-135">定义 DbContext 派生类型</span><span class="sxs-lookup"><span data-stu-id="ac3e9-135">Define the DbContext Derived Type</span></span>

<span data-ttu-id="ac3e9-136">除了定义实体外，还需要定义派生自 DbContext 的类，并公开 DbSet&lt;TEntity&gt; 属性。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-136">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="ac3e9-137">DbSet&lt;TEntity&gt; 属性使上下文知道要包括在模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-137">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="ac3e9-138">DbContext 派生类型的实例在运行时管理实体对象，这包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-138">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="ac3e9-139">DbContext 和 DbSet 类型是在 EntityFramework 程序集中定义的。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-139">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="ac3e9-140">我们将使用 EntityFramework NuGet 包添加对此 DLL 的引用。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-140">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="ac3e9-141">在解决方案资源管理器中，右键单击项目名称。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-141">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="ac3e9-142">选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="ac3e9-142">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="ac3e9-143">在 "管理 NuGet 包" 对话框中，选择 "**联机**" 选项卡，然后选择 " **EntityFramework** " 包。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-143">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="ac3e9-144">单击“安装”</span><span class="sxs-lookup"><span data-stu-id="ac3e9-144">Click **Install**</span></span>

<span data-ttu-id="ac3e9-145">请注意，除 EntityFramework 程序集之外，还添加了对 System.componentmodel. DataAnnotations 程序集的引用。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-145">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="ac3e9-146">在 Program.cs 文件的顶部，添加以下 using 语句：</span><span class="sxs-lookup"><span data-stu-id="ac3e9-146">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="ac3e9-147">在 Program.cs 中，添加上下文定义。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-147">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="ac3e9-148">保留和检索数据</span><span class="sxs-lookup"><span data-stu-id="ac3e9-148">Persist and Retrieve Data</span></span>

<span data-ttu-id="ac3e9-149">打开 Program.cs 文件，其中定义了 Main 方法。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-149">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="ac3e9-150">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-150">Add the following code into the Main function.</span></span>

<span data-ttu-id="ac3e9-151">该代码将两个新的大学对象添加到上下文中。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-151">The code adds two new University objects to the context.</span></span> <span data-ttu-id="ac3e9-152">空间属性使用 DbGeography. FromText 方法进行初始化。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-152">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="ac3e9-153">将 WellKnownText 表示的地理点传递给方法。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-153">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="ac3e9-154">然后，该代码将保存数据。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-154">The code then saves the data.</span></span> <span data-ttu-id="ac3e9-155">然后，将构造并执行 LINQ 查询，该查询返回其位置与指定位置最接近的大学对象。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-155">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
        {
            Name = "School of Fine Art",
            Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
        });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                        orderby u.Location.Distance(myLocation)
                        select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="ac3e9-156">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-156">Compile and run the application.</span></span> <span data-ttu-id="ac3e9-157">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="ac3e9-157">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="ac3e9-158">查看生成的数据库</span><span class="sxs-lookup"><span data-stu-id="ac3e9-158">View the Generated Database</span></span>

<span data-ttu-id="ac3e9-159">首次运行应用程序时，实体框架会为您创建一个数据库。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-159">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="ac3e9-160">由于我们安装了 Visual Studio 2012，因此将在 LocalDB 实例上创建数据库。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-160">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="ac3e9-161">默认情况下，实体框架在派生上下文的完全限定名称（在此示例中为**SpatialCodeFirst. UniversityContext**）之后对数据库进行命名。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-161">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="ac3e9-162">随后将使用现有数据库的时间。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-162">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="ac3e9-163">请注意，如果在创建数据库后对模型进行了任何更改，则应使用 Code First 迁移来更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-163">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="ac3e9-164">有关使用迁移的示例，请参阅[Code First 到新的数据库](~/ef6/modeling/code-first/workflows/new-database.md)。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-164">See [Code First to a New Database](~/ef6/modeling/code-first/workflows/new-database.md) for an example of using Migrations.</span></span>

<span data-ttu-id="ac3e9-165">若要查看数据库和数据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ac3e9-165">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="ac3e9-166">在 Visual Studio 2012 主菜单中，选择 "**查看** -&gt; **SQL Server 对象资源管理器**"。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-166">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="ac3e9-167">如果 LocalDB 不在服务器列表中，请在**SQL Server**上单击鼠标右键按钮，然后选择 "**添加 SQL Server**使用默认**Windows 身份验证**连接到 LocalDB 实例</span><span class="sxs-lookup"><span data-stu-id="ac3e9-167">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="ac3e9-168">展开 LocalDB 节点</span><span class="sxs-lookup"><span data-stu-id="ac3e9-168">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="ac3e9-169">展开 "**数据库**" 文件夹，以查看新数据库并浏览到**大学**表</span><span class="sxs-lookup"><span data-stu-id="ac3e9-169">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="ac3e9-170">若要查看数据，请右键单击该表，然后选择 "**查看数据**"</span><span class="sxs-lookup"><span data-stu-id="ac3e9-170">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="ac3e9-171">Summary</span><span class="sxs-lookup"><span data-stu-id="ac3e9-171">Summary</span></span>

<span data-ttu-id="ac3e9-172">在本演练中，我们介绍了如何在实体框架 Code First 中使用空间类型。</span><span class="sxs-lookup"><span data-stu-id="ac3e9-172">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
