---
title: 枚举支持-EF 设计器-EF6
description: 枚举支持-实体框架6中的 EF 设计器
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 42508b533249bf01c76939f864a23adc86a08822
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064921"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="73f31-103">枚举支持-EF 设计器</span><span class="sxs-lookup"><span data-stu-id="73f31-103">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="73f31-104">**EF5 仅向前** -实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="73f31-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="73f31-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="73f31-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="73f31-106">此视频和分步演练演示了如何将枚举类型与 Entity Framework Designer 一起使用。</span><span class="sxs-lookup"><span data-stu-id="73f31-106">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="73f31-107">它还演示了如何在 LINQ 查询中使用枚举。</span><span class="sxs-lookup"><span data-stu-id="73f31-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="73f31-108">本演练将使用 Model First 创建新的数据库，但 EF 设计器也可以与 [Database First](xref:ef6/modeling/designer/workflows/database-first) 工作流一起用于映射到现有数据库。</span><span class="sxs-lookup"><span data-stu-id="73f31-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="73f31-109">实体框架5中引入了枚举支持。</span><span class="sxs-lookup"><span data-stu-id="73f31-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="73f31-110">若要使用枚举、空间数据类型和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。</span><span class="sxs-lookup"><span data-stu-id="73f31-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="73f31-111">默认情况下，Visual Studio 2012 面向 .NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="73f31-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="73f31-112">在实体框架中，枚举可以具有以下基础类型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。</span><span class="sxs-lookup"><span data-stu-id="73f31-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="73f31-113">观看视频</span><span class="sxs-lookup"><span data-stu-id="73f31-113">Watch the Video</span></span>
<span data-ttu-id="73f31-114">此视频演示如何将枚举类型与 Entity Framework Designer 一起使用。</span><span class="sxs-lookup"><span data-stu-id="73f31-114">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="73f31-115">它还演示了如何在 LINQ 查询中使用枚举。</span><span class="sxs-lookup"><span data-stu-id="73f31-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="73f31-116">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="73f31-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="73f31-117">**视频**： [wmv](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [.wmv](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="73f31-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="73f31-118">先决条件</span><span class="sxs-lookup"><span data-stu-id="73f31-118">Pre-Requisites</span></span>

<span data-ttu-id="73f31-119">你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="73f31-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="73f31-120">设置项目</span><span class="sxs-lookup"><span data-stu-id="73f31-120">Set up the Project</span></span>

1.  <span data-ttu-id="73f31-121">打开 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="73f31-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="73f31-122">在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"</span><span class="sxs-lookup"><span data-stu-id="73f31-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="73f31-123">在左窗格中，单击 " **Visual \# C**"，然后选择**控制台**模板</span><span class="sxs-lookup"><span data-stu-id="73f31-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="73f31-124">输入 **EnumEFDesigner** 作为项目名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="73f31-124">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="73f31-125">使用 EF 设计器创建新模型</span><span class="sxs-lookup"><span data-stu-id="73f31-125">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="73f31-126">右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"</span><span class="sxs-lookup"><span data-stu-id="73f31-126">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="73f31-127">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**</span><span class="sxs-lookup"><span data-stu-id="73f31-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="73f31-128">输入**EnumTestModel**作为文件名，然后单击 "**添加**"</span><span class="sxs-lookup"><span data-stu-id="73f31-128">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="73f31-129">在 "实体数据模型向导" 页上，在 "选择模型内容" 对话框中选择 " **空模型** "</span><span class="sxs-lookup"><span data-stu-id="73f31-129">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="73f31-130">单击“完成”</span><span class="sxs-lookup"><span data-stu-id="73f31-130">Click **Finish**</span></span>

<span data-ttu-id="73f31-131">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="73f31-131">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="73f31-132">该向导执行下列操作：</span><span class="sxs-lookup"><span data-stu-id="73f31-132">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="73f31-133">生成 EnumTestModel 文件，该文件定义概念模型、存储模型和这些模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="73f31-133">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="73f31-134">设置要嵌入到输出程序集的 .edmx 文件的元数据项目处理属性，以便将生成的元数据文件嵌入到程序集中。</span><span class="sxs-lookup"><span data-stu-id="73f31-134">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="73f31-135">添加对以下程序集的引用： EntityFramework、System.componentmodel 和 DataAnnotations。</span><span class="sxs-lookup"><span data-stu-id="73f31-135">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="73f31-136">创建 EnumTestModel.tt 和 EnumTestModel.Context.tt 文件，并将它们添加到 .edmx 文件下。</span><span class="sxs-lookup"><span data-stu-id="73f31-136">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="73f31-137">这些 T4 模板文件生成代码，该代码定义 DbContext 派生类型和映射到 .edmx 模型中的实体的 POCO 类型。</span><span class="sxs-lookup"><span data-stu-id="73f31-137">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="73f31-138">添加新的实体类型</span><span class="sxs-lookup"><span data-stu-id="73f31-138">Add a New Entity Type</span></span>

1.  <span data-ttu-id="73f31-139">右键单击设计图面的空白区域，选择 " **添加 &gt; 实体**"，此时将显示 "新建实体" 对话框。</span><span class="sxs-lookup"><span data-stu-id="73f31-139">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="73f31-140">为类型名称指定 **部门** 并为键属性名称指定 **DepartmentID** ，将类型保留为 **Int32**</span><span class="sxs-lookup"><span data-stu-id="73f31-140">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="73f31-141">单击 **“确定”**</span><span class="sxs-lookup"><span data-stu-id="73f31-141">Click **OK**</span></span>
4.  <span data-ttu-id="73f31-142">右键单击该实体，然后选择 "**添加新 &gt; 标量属性**"</span><span class="sxs-lookup"><span data-stu-id="73f31-142">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="73f31-143">将新属性重命名为 **名称**</span><span class="sxs-lookup"><span data-stu-id="73f31-143">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="73f31-144">将新属性的类型更改为 **Int32** (默认情况下，新属性为字符串类型) 若要更改类型，请打开属性窗口并将 type 属性更改为 **Int32**</span><span class="sxs-lookup"><span data-stu-id="73f31-144">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="73f31-145">添加另一个标量属性，并将其重命名为 **预算**，将类型更改为 **Decimal**</span><span class="sxs-lookup"><span data-stu-id="73f31-145">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="73f31-146">添加枚举类型</span><span class="sxs-lookup"><span data-stu-id="73f31-146">Add an Enum Type</span></span>

1.  <span data-ttu-id="73f31-147">在 Entity Framework Designer 中，右键单击 "名称" 属性，选择 "**转换为枚举**"</span><span class="sxs-lookup"><span data-stu-id="73f31-147">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![转换为枚举](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="73f31-149">在 " **添加枚举** " 对话框中，键入 **DepartmentNames** 作为枚举类型名称，将基础类型更改为 **Int32**，然后将以下成员添加到类型：英语、数学和经济性</span><span class="sxs-lookup"><span data-stu-id="73f31-149">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![添加枚举类型](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="73f31-151">按“确定”\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="73f31-151">Press **OK**</span></span>
4.  <span data-ttu-id="73f31-152">保存模型并生成项目</span><span class="sxs-lookup"><span data-stu-id="73f31-152">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="73f31-153">生成时，有关未映射实体和关联的警告可能出现在错误列表中。</span><span class="sxs-lookup"><span data-stu-id="73f31-153">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="73f31-154">你可以忽略这些警告，因为在我们选择从模型生成数据库后，错误将消失。</span><span class="sxs-lookup"><span data-stu-id="73f31-154">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="73f31-155">如果你查看属性窗口，你会注意到，Name 属性的类型已更改为 **DepartmentNames** ，并且新添加的枚举类型已添加到类型列表中。</span><span class="sxs-lookup"><span data-stu-id="73f31-155">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="73f31-156">如果切换到 "模型浏览器" 窗口，您将看到该类型也已添加到 "枚举类型" 节点。</span><span class="sxs-lookup"><span data-stu-id="73f31-156">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![模型浏览器](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="73f31-158">还可以通过单击鼠标右键并选择 " **添加枚举类型**"，从此窗口添加新枚举类型。</span><span class="sxs-lookup"><span data-stu-id="73f31-158">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="73f31-159">一旦创建类型，该类型就会出现在类型列表中，并且你可以与属性关联</span><span class="sxs-lookup"><span data-stu-id="73f31-159">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="73f31-160">从模型生成数据库</span><span class="sxs-lookup"><span data-stu-id="73f31-160">Generate Database from Model</span></span>

<span data-ttu-id="73f31-161">现在，我们可以生成一个基于该模型的数据库。</span><span class="sxs-lookup"><span data-stu-id="73f31-161">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="73f31-162">右键单击 Entity Designer 图面上的空白区域，然后选择 "**从模型生成数据库**"</span><span class="sxs-lookup"><span data-stu-id="73f31-162">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="73f31-163">随即显示 "生成数据库" 向导的 "选择数据连接" 对话框，单击 "**新建连接**" 按钮，为数据库的服务器名称和**EnumTest**指定\*\* (localdb) \\ Mssqllocaldb\*\* ，并单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="73f31-163">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="73f31-164">询问是否要创建新数据库的对话框将弹出，单击 **"是"**。</span><span class="sxs-lookup"><span data-stu-id="73f31-164">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="73f31-165">单击 " **下一步** "，创建数据库向导将生成数据定义语言 (DDL) 用于创建数据库。在 "摘要和设置" 对话框中显示该 ddl 不包含映射到枚举类型的表的定义</span><span class="sxs-lookup"><span data-stu-id="73f31-165">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="73f31-166">单击 " **完成** "，单击 "完成" 不执行 DDL 脚本。</span><span class="sxs-lookup"><span data-stu-id="73f31-166">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="73f31-167">"创建数据库向导" 执行以下操作：在 T-sql 编辑器中打开 **EnumTest** ，并生成 .edmx 文件的存储架构和映射部分，并将连接字符串信息添加到 App.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="73f31-167">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="73f31-168">在 T-sql 编辑器中单击鼠标右键，然后选择 "**执行**连接到服务器" 对话框，输入步骤2中的连接信息，然后单击 "**连接**"</span><span class="sxs-lookup"><span data-stu-id="73f31-168">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="73f31-169">若要查看生成的架构，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 "**刷新**"</span><span class="sxs-lookup"><span data-stu-id="73f31-169">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="73f31-170">保留和检索数据</span><span class="sxs-lookup"><span data-stu-id="73f31-170">Persist and Retrieve Data</span></span>

<span data-ttu-id="73f31-171">打开 Program.cs 文件，其中定义了 Main 方法。</span><span class="sxs-lookup"><span data-stu-id="73f31-171">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="73f31-172">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="73f31-172">Add the following code into the Main function.</span></span> <span data-ttu-id="73f31-173">该代码将新的部门对象添加到上下文中。</span><span class="sxs-lookup"><span data-stu-id="73f31-173">The code adds a new Department object to the context.</span></span> <span data-ttu-id="73f31-174">然后，它会保存数据。</span><span class="sxs-lookup"><span data-stu-id="73f31-174">It then saves the data.</span></span> <span data-ttu-id="73f31-175">此代码还执行 LINQ 查询，该查询返回名称为 DepartmentNames 的部门。</span><span class="sxs-lookup"><span data-stu-id="73f31-175">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="73f31-176">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="73f31-176">Compile and run the application.</span></span> <span data-ttu-id="73f31-177">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="73f31-177">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="73f31-178">若要查看数据库中的数据，请在 SQL Server 对象资源管理器中右键单击数据库名称，然后选择 " **刷新**"。</span><span class="sxs-lookup"><span data-stu-id="73f31-178">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="73f31-179">然后，单击表上的鼠标右键，然后选择 " **查看数据**"。</span><span class="sxs-lookup"><span data-stu-id="73f31-179">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="73f31-180">摘要</span><span class="sxs-lookup"><span data-stu-id="73f31-180">Summary</span></span>

<span data-ttu-id="73f31-181">在本演练中，我们介绍了如何使用 Entity Framework Designer 映射枚举类型以及如何在代码中使用枚举。</span><span class="sxs-lookup"><span data-stu-id="73f31-181">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
