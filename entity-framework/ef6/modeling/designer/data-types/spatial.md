---
title: 空间-EF 设计器-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
ms.openlocfilehash: a9c54fbc14dd02ce5d4d91449a0d5f9e72f7f0f7
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182505"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="48de8-102">空间-EF 设计器</span><span class="sxs-lookup"><span data-stu-id="48de8-102">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="48de8-103">**EF5 仅向前**-实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="48de8-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="48de8-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="48de8-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="48de8-105">视频和分步演练演示了如何使用 Entity Framework Designer 映射空间类型。</span><span class="sxs-lookup"><span data-stu-id="48de8-105">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="48de8-106">它还演示了如何使用 LINQ 查询查找两个位置之间的距离。</span><span class="sxs-lookup"><span data-stu-id="48de8-106">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="48de8-107">本演练将使用 Model First 创建新的数据库，但 EF 设计器也可以与[Database First](~/ef6/modeling/designer/workflows/database-first.md)工作流一起用于映射到现有数据库。</span><span class="sxs-lookup"><span data-stu-id="48de8-107">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](~/ef6/modeling/designer/workflows/database-first.md) workflow to map to an existing database.</span></span>

<span data-ttu-id="48de8-108">实体框架5中引入了空间类型支持。</span><span class="sxs-lookup"><span data-stu-id="48de8-108">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="48de8-109">请注意，若要使用空间类型、枚举和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。</span><span class="sxs-lookup"><span data-stu-id="48de8-109">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="48de8-110">默认情况下，Visual Studio 2012 面向 .NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="48de8-110">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="48de8-111">若要使用空间数据类型，还必须使用具有空间支持的实体框架提供程序。</span><span class="sxs-lookup"><span data-stu-id="48de8-111">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="48de8-112">有关详细信息，请参阅[提供程序对空间类型的支持](~/ef6/fundamentals/providers/spatial-support.md)。</span><span class="sxs-lookup"><span data-stu-id="48de8-112">See [provider support for spatial types](~/ef6/fundamentals/providers/spatial-support.md) for more information.</span></span>

<span data-ttu-id="48de8-113">主要的空间数据类型有两种：地理和几何。</span><span class="sxs-lookup"><span data-stu-id="48de8-113">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="48de8-114">Geography 数据类型存储椭圆体的数据（例如 GPS 纬度和经度坐标）。</span><span class="sxs-lookup"><span data-stu-id="48de8-114">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="48de8-115">Geometry 数据类型表示欧氏（平面）坐标系。</span><span class="sxs-lookup"><span data-stu-id="48de8-115">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="48de8-116">观看视频</span><span class="sxs-lookup"><span data-stu-id="48de8-116">Watch the video</span></span>
<span data-ttu-id="48de8-117">此视频演示如何用 Entity Framework Designer 映射空间类型。</span><span class="sxs-lookup"><span data-stu-id="48de8-117">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="48de8-118">它还演示了如何使用 LINQ 查询查找两个位置之间的距离。</span><span class="sxs-lookup"><span data-stu-id="48de8-118">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="48de8-119">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="48de8-119">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="48de8-120">**视频**：[WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="48de8-120">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="48de8-121">先决条件</span><span class="sxs-lookup"><span data-stu-id="48de8-121">Pre-Requisites</span></span>

<span data-ttu-id="48de8-122">你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="48de8-122">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="48de8-123">设置项目</span><span class="sxs-lookup"><span data-stu-id="48de8-123">Set up the Project</span></span>

1.  <span data-ttu-id="48de8-124">打开 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="48de8-124">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="48de8-125">在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"</span><span class="sxs-lookup"><span data-stu-id="48de8-125">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="48de8-126">在左窗格中，单击 " **Visual C\#** "，然后选择**控制台**模板</span><span class="sxs-lookup"><span data-stu-id="48de8-126">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="48de8-127">输入**SpatialEFDesigner**作为项目名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="48de8-127">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="48de8-128">使用 EF 设计器创建新模型</span><span class="sxs-lookup"><span data-stu-id="48de8-128">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="48de8-129">右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"</span><span class="sxs-lookup"><span data-stu-id="48de8-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="48de8-130">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="48de8-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="48de8-131">输入**UniversityModel**作为文件名，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="48de8-131">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="48de8-132">在 "实体数据模型向导" 页上，在 "选择模型内容" 对话框中选择 "**空模型**"</span><span class="sxs-lookup"><span data-stu-id="48de8-132">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="48de8-133">单击 "**完成**"</span><span class="sxs-lookup"><span data-stu-id="48de8-133">Click **Finish**</span></span>

<span data-ttu-id="48de8-134">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="48de8-134">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="48de8-135">该向导执行下列操作：</span><span class="sxs-lookup"><span data-stu-id="48de8-135">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="48de8-136">生成 EnumTestModel 文件，该文件定义概念模型、存储模型和这些模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="48de8-136">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="48de8-137">设置要嵌入到输出程序集的 .edmx 文件的元数据项目处理属性，以便将生成的元数据文件嵌入到程序集中。</span><span class="sxs-lookup"><span data-stu-id="48de8-137">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="48de8-138">添加对以下程序集的引用： EntityFramework、System.componentmodel 和 DataAnnotations。</span><span class="sxs-lookup"><span data-stu-id="48de8-138">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="48de8-139">创建 UniversityModel.tt 和 UniversityModel.Context.tt 文件，并将它们添加到 .edmx 文件下。</span><span class="sxs-lookup"><span data-stu-id="48de8-139">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="48de8-140">这些 T4 模板文件生成代码，该代码定义 DbContext 派生类型和映射到 .edmx 模型中的实体的 POCO 类型</span><span class="sxs-lookup"><span data-stu-id="48de8-140">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="48de8-141">添加新的实体类型</span><span class="sxs-lookup"><span data-stu-id="48de8-141">Add a New Entity Type</span></span>

1.  <span data-ttu-id="48de8-142">右键单击设计图面的空白区域，选择 "**外接&gt; 实体**"，将显示 "新建实体" 对话框。</span><span class="sxs-lookup"><span data-stu-id="48de8-142">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="48de8-143">指定类型名称的**大学**，并为键属性名称指定**UniversityID** ，将类型保留为**Int32**</span><span class="sxs-lookup"><span data-stu-id="48de8-143">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="48de8-144">单击“确定”</span><span class="sxs-lookup"><span data-stu-id="48de8-144">Click **OK**</span></span>
4.  <span data-ttu-id="48de8-145">右键单击该实体，然后选择 "**添加新的&gt; 标量属性**"</span><span class="sxs-lookup"><span data-stu-id="48de8-145">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="48de8-146">将新属性重命名为**名称**</span><span class="sxs-lookup"><span data-stu-id="48de8-146">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="48de8-147">添加另一个标量属性，并将其重命名为**Location**打开属性窗口并将新属性的类型更改为**Geography**</span><span class="sxs-lookup"><span data-stu-id="48de8-147">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="48de8-148">保存模型并生成项目</span><span class="sxs-lookup"><span data-stu-id="48de8-148">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="48de8-149">生成时，有关未映射实体和关联的警告可能出现在错误列表中。</span><span class="sxs-lookup"><span data-stu-id="48de8-149">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="48de8-150">你可以忽略这些警告，因为在我们选择从模型生成数据库后，错误将消失。</span><span class="sxs-lookup"><span data-stu-id="48de8-150">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="48de8-151">从模型生成数据库</span><span class="sxs-lookup"><span data-stu-id="48de8-151">Generate Database from Model</span></span>

<span data-ttu-id="48de8-152">现在，我们可以生成一个基于该模型的数据库。</span><span class="sxs-lookup"><span data-stu-id="48de8-152">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="48de8-153">右键单击 Entity Designer 图面上的空白区域，然后选择 "**从模型生成数据库**"</span><span class="sxs-lookup"><span data-stu-id="48de8-153">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="48de8-154">"生成数据库" 向导的 "选择您的数据连接" 对话框随即出现，单击 "**新建连接**" 按钮 "指定 **（localdb）\\mssqllocaldb**作为数据库的服务器名称和**大学**，并单击 **" 确定 "**</span><span class="sxs-lookup"><span data-stu-id="48de8-154">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="48de8-155">询问是否要创建新数据库的对话框将弹出，单击 **"是"** 。</span><span class="sxs-lookup"><span data-stu-id="48de8-155">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="48de8-156">单击 "**下一步**"，"创建数据库向导" 生成用于创建数据库的数据定义语言（ddl）。在 "摘要和设置" 对话框中显示该 ddl 不包含映射到枚举类型的表的定义</span><span class="sxs-lookup"><span data-stu-id="48de8-156">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="48de8-157">单击 "**完成**"，单击 "完成" 不执行 DDL 脚本。</span><span class="sxs-lookup"><span data-stu-id="48de8-157">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="48de8-158">"创建数据库向导" 执行以下操作：在 T-sql 编辑器中打开**UniversityModel** ，并生成 edmx 文件的存储架构和映射部分，并将连接字符串信息添加到 app.config 文件</span><span class="sxs-lookup"><span data-stu-id="48de8-158">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="48de8-159">在 T-sql 编辑器中单击鼠标右键，然后选择 "**执行**连接到服务器" 对话框，输入步骤2中的连接信息，然后单击 "**连接**"</span><span class="sxs-lookup"><span data-stu-id="48de8-159">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="48de8-160">若要查看生成的架构，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"</span><span class="sxs-lookup"><span data-stu-id="48de8-160">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="48de8-161">保留和检索数据</span><span class="sxs-lookup"><span data-stu-id="48de8-161">Persist and Retrieve Data</span></span>

<span data-ttu-id="48de8-162">打开 Program.cs 文件，其中定义了 Main 方法。</span><span class="sxs-lookup"><span data-stu-id="48de8-162">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="48de8-163">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="48de8-163">Add the following code into the Main function.</span></span>

<span data-ttu-id="48de8-164">该代码将两个新的大学对象添加到上下文中。</span><span class="sxs-lookup"><span data-stu-id="48de8-164">The code adds two new University objects to the context.</span></span> <span data-ttu-id="48de8-165">空间属性使用 DbGeography. FromText 方法进行初始化。</span><span class="sxs-lookup"><span data-stu-id="48de8-165">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="48de8-166">将 WellKnownText 表示的地理点传递给方法。</span><span class="sxs-lookup"><span data-stu-id="48de8-166">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="48de8-167">然后，该代码将保存数据。</span><span class="sxs-lookup"><span data-stu-id="48de8-167">The code then saves the data.</span></span> <span data-ttu-id="48de8-168">然后，将构造并执行 LINQ 查询，该查询返回其位置与指定位置最接近的大学对象。</span><span class="sxs-lookup"><span data-stu-id="48de8-168">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

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

<span data-ttu-id="48de8-169">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="48de8-169">Compile and run the application.</span></span> <span data-ttu-id="48de8-170">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="48de8-170">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="48de8-171">若要查看数据库中的数据，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"。</span><span class="sxs-lookup"><span data-stu-id="48de8-171">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="48de8-172">然后，单击表上的鼠标右键，然后选择 "**查看数据**"。</span><span class="sxs-lookup"><span data-stu-id="48de8-172">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="48de8-173">摘要</span><span class="sxs-lookup"><span data-stu-id="48de8-173">Summary</span></span>

<span data-ttu-id="48de8-174">在本演练中，我们介绍了如何使用 Entity Framework Designer 映射空间类型，以及如何在代码中使用空间类型。</span><span class="sxs-lookup"><span data-stu-id="48de8-174">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
