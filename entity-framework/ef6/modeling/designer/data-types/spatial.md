---
title: 空间-EF 设计器-EF6
description: 空间-EF 设计器（实体框架6）
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 298666e558ad5bb531948e6a0bbcf3d71141df0b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064908"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="74989-103">空间-EF 设计器</span><span class="sxs-lookup"><span data-stu-id="74989-103">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="74989-104">**EF5 仅向前** -实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="74989-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="74989-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="74989-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="74989-106">视频和分步演练演示了如何使用 Entity Framework Designer 映射空间类型。</span><span class="sxs-lookup"><span data-stu-id="74989-106">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="74989-107">它还演示了如何使用 LINQ 查询查找两个位置之间的距离。</span><span class="sxs-lookup"><span data-stu-id="74989-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="74989-108">本演练将使用 Model First 创建新的数据库，但 EF 设计器也可以与 [Database First](xref:ef6/modeling/designer/workflows/database-first) 工作流一起用于映射到现有数据库。</span><span class="sxs-lookup"><span data-stu-id="74989-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="74989-109">实体框架5中引入了空间类型支持。</span><span class="sxs-lookup"><span data-stu-id="74989-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="74989-110">请注意，若要使用空间类型、枚举和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。</span><span class="sxs-lookup"><span data-stu-id="74989-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="74989-111">默认情况下，Visual Studio 2012 面向 .NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="74989-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="74989-112">若要使用空间数据类型，还必须使用具有空间支持的实体框架提供程序。</span><span class="sxs-lookup"><span data-stu-id="74989-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="74989-113">有关详细信息，请参阅 [提供程序对空间类型的支持](xref:ef6/fundamentals/providers/spatial-support) 。</span><span class="sxs-lookup"><span data-stu-id="74989-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="74989-114">主要的空间数据类型有两种：地理和几何。</span><span class="sxs-lookup"><span data-stu-id="74989-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="74989-115">Geography 数据类型存储椭圆体数据 (例如，GPS 纬度和经度坐标) 。</span><span class="sxs-lookup"><span data-stu-id="74989-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="74989-116">Geometry 数据类型表示欧氏 (平面) 坐标系。</span><span class="sxs-lookup"><span data-stu-id="74989-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="74989-117">观看视频</span><span class="sxs-lookup"><span data-stu-id="74989-117">Watch the video</span></span>
<span data-ttu-id="74989-118">此视频演示如何用 Entity Framework Designer 映射空间类型。</span><span class="sxs-lookup"><span data-stu-id="74989-118">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="74989-119">它还演示了如何使用 LINQ 查询查找两个位置之间的距离。</span><span class="sxs-lookup"><span data-stu-id="74989-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="74989-120">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="74989-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="74989-121">**视频**： [wmv](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [.wmv](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="74989-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="74989-122">先决条件</span><span class="sxs-lookup"><span data-stu-id="74989-122">Pre-Requisites</span></span>

<span data-ttu-id="74989-123">你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="74989-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="74989-124">设置项目</span><span class="sxs-lookup"><span data-stu-id="74989-124">Set up the Project</span></span>

1.  <span data-ttu-id="74989-125">打开 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="74989-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="74989-126">在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"</span><span class="sxs-lookup"><span data-stu-id="74989-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="74989-127">在左窗格中，单击 " **Visual \# C**"，然后选择**控制台**模板</span><span class="sxs-lookup"><span data-stu-id="74989-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="74989-128">输入 **SpatialEFDesigner** 作为项目名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="74989-128">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="74989-129">使用 EF 设计器创建新模型</span><span class="sxs-lookup"><span data-stu-id="74989-129">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="74989-130">右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"</span><span class="sxs-lookup"><span data-stu-id="74989-130">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="74989-131">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="74989-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="74989-132">输入**UniversityModel**作为文件名，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="74989-132">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="74989-133">在 "实体数据模型向导" 页上，在 "选择模型内容" 对话框中选择 " **空模型** "</span><span class="sxs-lookup"><span data-stu-id="74989-133">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="74989-134">单击“完成”</span><span class="sxs-lookup"><span data-stu-id="74989-134">Click **Finish**</span></span>

<span data-ttu-id="74989-135">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="74989-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="74989-136">该向导执行下列操作：</span><span class="sxs-lookup"><span data-stu-id="74989-136">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="74989-137">生成 EnumTestModel 文件，该文件定义概念模型、存储模型和这些模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="74989-137">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="74989-138">设置要嵌入到输出程序集的 .edmx 文件的元数据项目处理属性，以便将生成的元数据文件嵌入到程序集中。</span><span class="sxs-lookup"><span data-stu-id="74989-138">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="74989-139">添加对以下程序集的引用： EntityFramework、System.componentmodel 和 DataAnnotations。</span><span class="sxs-lookup"><span data-stu-id="74989-139">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="74989-140">创建 UniversityModel.tt 和 UniversityModel.Context.tt 文件，并将它们添加到 .edmx 文件下。</span><span class="sxs-lookup"><span data-stu-id="74989-140">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="74989-141">这些 T4 模板文件生成代码，该代码定义 DbContext 派生类型和映射到 .edmx 模型中的实体的 POCO 类型</span><span class="sxs-lookup"><span data-stu-id="74989-141">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="74989-142">添加新的实体类型</span><span class="sxs-lookup"><span data-stu-id="74989-142">Add a New Entity Type</span></span>

1.  <span data-ttu-id="74989-143">右键单击设计图面的空白区域，选择 " **添加 &gt; 实体**"，此时将显示 "新建实体" 对话框。</span><span class="sxs-lookup"><span data-stu-id="74989-143">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="74989-144">指定类型名称的 **大学** ，并为键属性名称指定 **UniversityID** ，将类型保留为 **Int32**</span><span class="sxs-lookup"><span data-stu-id="74989-144">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="74989-145">单击 **“确定”**</span><span class="sxs-lookup"><span data-stu-id="74989-145">Click **OK**</span></span>
4.  <span data-ttu-id="74989-146">右键单击该实体，然后选择 "**添加新 &gt; 标量属性**"</span><span class="sxs-lookup"><span data-stu-id="74989-146">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="74989-147">将新属性重命名为 **名称**</span><span class="sxs-lookup"><span data-stu-id="74989-147">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="74989-148">添加另一个标量属性，并将其重命名为 **Location** 打开属性窗口并将新属性的类型更改为 **Geography**</span><span class="sxs-lookup"><span data-stu-id="74989-148">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="74989-149">保存模型并生成项目</span><span class="sxs-lookup"><span data-stu-id="74989-149">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="74989-150">生成时，有关未映射实体和关联的警告可能出现在错误列表中。</span><span class="sxs-lookup"><span data-stu-id="74989-150">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="74989-151">你可以忽略这些警告，因为在我们选择从模型生成数据库后，错误将消失。</span><span class="sxs-lookup"><span data-stu-id="74989-151">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="74989-152">从模型生成数据库</span><span class="sxs-lookup"><span data-stu-id="74989-152">Generate Database from Model</span></span>

<span data-ttu-id="74989-153">现在，我们可以生成一个基于该模型的数据库。</span><span class="sxs-lookup"><span data-stu-id="74989-153">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="74989-154">右键单击 Entity Designer 图面上的空白区域，然后选择 "**从模型生成数据库**"</span><span class="sxs-lookup"><span data-stu-id="74989-154">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="74989-155">随即显示 "生成数据库" 向导的 "选择数据连接" 对话框，单击 "**新建连接**" 按钮，为数据库的服务器名称和**大学**指定\*\* (localdb) \\ Mssqllocaldb\*\* ，并单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="74989-155">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="74989-156">询问是否要创建新数据库的对话框将弹出，单击 **"是"**。</span><span class="sxs-lookup"><span data-stu-id="74989-156">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="74989-157">单击 " **下一步** "，创建数据库向导将生成数据定义语言 (DDL) 用于创建数据库。在 "摘要和设置" 对话框中显示该 ddl 不包含映射到枚举类型的表的定义</span><span class="sxs-lookup"><span data-stu-id="74989-157">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="74989-158">单击 " **完成** "，单击 "完成" 不执行 DDL 脚本。</span><span class="sxs-lookup"><span data-stu-id="74989-158">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="74989-159">"创建数据库向导" 执行以下操作：在 T-sql 编辑器中打开 **UniversityModel** ，并生成 .edmx 文件的存储架构和映射部分，并将连接字符串信息添加到 App.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="74989-159">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="74989-160">在 T-sql 编辑器中单击鼠标右键，然后选择 "**执行**连接到服务器" 对话框，输入步骤2中的连接信息，然后单击 "**连接**"</span><span class="sxs-lookup"><span data-stu-id="74989-160">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="74989-161">若要查看生成的架构，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"</span><span class="sxs-lookup"><span data-stu-id="74989-161">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="74989-162">保留和检索数据</span><span class="sxs-lookup"><span data-stu-id="74989-162">Persist and Retrieve Data</span></span>

<span data-ttu-id="74989-163">打开 Program.cs 文件，其中定义了 Main 方法。</span><span class="sxs-lookup"><span data-stu-id="74989-163">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="74989-164">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="74989-164">Add the following code into the Main function.</span></span>

<span data-ttu-id="74989-165">该代码将两个新的大学对象添加到上下文中。</span><span class="sxs-lookup"><span data-stu-id="74989-165">The code adds two new University objects to the context.</span></span> <span data-ttu-id="74989-166">空间属性使用 DbGeography. FromText 方法进行初始化。</span><span class="sxs-lookup"><span data-stu-id="74989-166">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="74989-167">将 WellKnownText 表示的地理点传递给方法。</span><span class="sxs-lookup"><span data-stu-id="74989-167">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="74989-168">然后，该代码将保存数据。</span><span class="sxs-lookup"><span data-stu-id="74989-168">The code then saves the data.</span></span> <span data-ttu-id="74989-169">然后，将构造并执行 LINQ 查询，该查询返回其位置与指定位置最接近的大学对象。</span><span class="sxs-lookup"><span data-stu-id="74989-169">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
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

<span data-ttu-id="74989-170">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="74989-170">Compile and run the application.</span></span> <span data-ttu-id="74989-171">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="74989-171">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="74989-172">若要查看数据库中的数据，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 " **刷新**"。</span><span class="sxs-lookup"><span data-stu-id="74989-172">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="74989-173">然后，单击表上的鼠标右键，然后选择 " **查看数据**"。</span><span class="sxs-lookup"><span data-stu-id="74989-173">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="74989-174">总结</span><span class="sxs-lookup"><span data-stu-id="74989-174">Summary</span></span>

<span data-ttu-id="74989-175">在本演练中，我们介绍了如何使用 Entity Framework Designer 映射空间类型，以及如何在代码中使用空间类型。</span><span class="sxs-lookup"><span data-stu-id="74989-175">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
