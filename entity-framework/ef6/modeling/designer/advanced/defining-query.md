---
title: 定义查询-EF 设计器-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: b1589dc12ccb50754c2e950932a2d82bc4869f6b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415531"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="b2a38-102">定义查询 EF 设计器</span><span class="sxs-lookup"><span data-stu-id="b2a38-102">Defining Query - EF Designer</span></span>
<span data-ttu-id="b2a38-103">本演练演示如何使用 EF 设计器将定义查询和相应的实体类型添加到模型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-103">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="b2a38-104">定义查询通常用于提供类似于数据库视图提供的功能，但该视图是在模型中定义的，而不是在数据库中定义的。</span><span class="sxs-lookup"><span data-stu-id="b2a38-104">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="b2a38-105">使用定义查询可执行在 .edmx 文件的 **DefiningQuery** 元素中指定的 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="b2a38-105">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="b2a38-106">有关详细信息，请参阅[SSDL 规范](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)中的 **DefiningQuery** 。</span><span class="sxs-lookup"><span data-stu-id="b2a38-106">For more information, see **DefiningQuery** in the [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="b2a38-107">使用定义查询时，还必须在模型中定义一个实体类型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-107">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="b2a38-108">实体类型用于呈现由定义查询公开的数据。</span><span class="sxs-lookup"><span data-stu-id="b2a38-108">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="b2a38-109">请注意，通过此实体类型显示的数据是只读的。</span><span class="sxs-lookup"><span data-stu-id="b2a38-109">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="b2a38-110">无法将参数化查询作为定义查询执行。</span><span class="sxs-lookup"><span data-stu-id="b2a38-110">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="b2a38-111">但是，可以通过将显示数据的实体类型的插入、更新和删除函数映射到存储过程来更新数据。</span><span class="sxs-lookup"><span data-stu-id="b2a38-111">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="b2a38-112">有关详细信息，请参阅 [对存储过程执行插入、更新和删除操作](~/ef6/modeling/designer/stored-procedures/cud.md)。</span><span class="sxs-lookup"><span data-stu-id="b2a38-112">For more information, see [Insert, Update, and Delete with Stored Procedures](~/ef6/modeling/designer/stored-procedures/cud.md).</span></span>

<span data-ttu-id="b2a38-113">本主题演示如何执行以下任务。</span><span class="sxs-lookup"><span data-stu-id="b2a38-113">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="b2a38-114">添加定义查询</span><span class="sxs-lookup"><span data-stu-id="b2a38-114">Add a Defining Query</span></span>
-   <span data-ttu-id="b2a38-115">向模型添加实体类型</span><span class="sxs-lookup"><span data-stu-id="b2a38-115">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="b2a38-116">将定义查询映射到实体类型</span><span class="sxs-lookup"><span data-stu-id="b2a38-116">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b2a38-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="b2a38-117">Prerequisites</span></span>

<span data-ttu-id="b2a38-118">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="b2a38-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="b2a38-119">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="b2a38-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="b2a38-120">[School 示例数据库](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="b2a38-120">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="b2a38-121">设置项目</span><span class="sxs-lookup"><span data-stu-id="b2a38-121">Set up the Project</span></span>

<span data-ttu-id="b2a38-122">本演练使用 Visual Studio 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="b2a38-122">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="b2a38-123">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="b2a38-123">Open Visual Studio.</span></span>
-   <span data-ttu-id="b2a38-124">在 **“文件”** 菜单上，指向 **“新建”** ，再单击 **“项目”** 。</span><span class="sxs-lookup"><span data-stu-id="b2a38-124">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="b2a38-125">在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台应用程序**" 模板。</span><span class="sxs-lookup"><span data-stu-id="b2a38-125">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="b2a38-126">输入**DefiningQuerySample**作为项目名称，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="b2a38-126">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="b2a38-127">基于 School 数据库创建模型</span><span class="sxs-lookup"><span data-stu-id="b2a38-127">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="b2a38-128">右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-128">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="b2a38-129">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。</span><span class="sxs-lookup"><span data-stu-id="b2a38-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="b2a38-130">输入**DefiningQueryModel**作为文件名，然后单击 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-130">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="b2a38-131">在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="b2a38-132">单击 "新建连接"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-132">Click New Connection.</span></span> <span data-ttu-id="b2a38-133">在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择身份验证方法，为数据库名称键入 **School** ，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="b2a38-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="b2a38-134">"选择您的数据连接" 对话框将通过数据库连接设置进行更新。</span><span class="sxs-lookup"><span data-stu-id="b2a38-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="b2a38-135">在 "选择数据库对象" 对话框中，检查 "**表** " 节点。</span><span class="sxs-lookup"><span data-stu-id="b2a38-135">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="b2a38-136">这会将所有表添加到**School**模型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-136">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="b2a38-137">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-137">Click **Finish**.</span></span>
-   <span data-ttu-id="b2a38-138">在解决方案资源管理器中，右键单击**DefiningQueryModel**文件并选择 "**打开方式 ...** "。</span><span class="sxs-lookup"><span data-stu-id="b2a38-138">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="b2a38-139">选择 " **XML （文本）编辑器**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-139">Select **XML (Text) Editor**.</span></span>

    ![XML 编辑器](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="b2a38-141">如果出现以下消息，请单击 **"是"** ：</span><span class="sxs-lookup"><span data-stu-id="b2a38-141">Click **Yes** if prompted with the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="b2a38-143">添加定义查询</span><span class="sxs-lookup"><span data-stu-id="b2a38-143">Add a Defining Query</span></span>

<span data-ttu-id="b2a38-144">在此步骤中，我们将使用 "XML 编辑器" 向 .edmx 文件的 SSDL 部分添加定义查询和实体类型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-144">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="b2a38-145">将 **EntitySet** 元素添加到 .edmx 文件的 SSDL 部分（第5行到第13行）。</span><span class="sxs-lookup"><span data-stu-id="b2a38-145">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="b2a38-146">指定下列信息：</span><span class="sxs-lookup"><span data-stu-id="b2a38-146">Specify the following:</span></span>
    -   <span data-ttu-id="b2a38-147">仅指定 **EntitySet** 元素的 **名称** 和 **EntityType** 特性。</span><span class="sxs-lookup"><span data-stu-id="b2a38-147">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="b2a38-148">实体类型的完全限定名称在 **EntityType** 特性中使用。</span><span class="sxs-lookup"><span data-stu-id="b2a38-148">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="b2a38-149">要执行的 SQL 语句是在 **DefiningQuery** 元素中指定的。</span><span class="sxs-lookup"><span data-stu-id="b2a38-149">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   <span data-ttu-id="b2a38-150">将**EntityType**元素添加到 .EDMX 的 SSDL 部分。</span><span class="sxs-lookup"><span data-stu-id="b2a38-150">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="b2a38-151">文件，如下所示。</span><span class="sxs-lookup"><span data-stu-id="b2a38-151">file as shown below.</span></span> <span data-ttu-id="b2a38-152">注意以下各项：</span><span class="sxs-lookup"><span data-stu-id="b2a38-152">Note the following:</span></span>
    -   <span data-ttu-id="b2a38-153">**Name**特性的值对应于上面的**EntitySet**元素中的**EntityType**特性的值，但该实体类型的完全限定名称在**EntityType**特性中使用。</span><span class="sxs-lookup"><span data-stu-id="b2a38-153">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="b2a38-154">属性名称对应于**DefiningQuery**元素（如上所示）中的 SQL 语句返回的列名称。</span><span class="sxs-lookup"><span data-stu-id="b2a38-154">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="b2a38-155">在此示例中，实体键由三个属性组成以确保唯一键值。</span><span class="sxs-lookup"><span data-stu-id="b2a38-155">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> <span data-ttu-id="b2a38-156">如果以后运行**模型更新向导**对话框，对存储模型所做的任何更改（包括定义查询）都将被覆盖。</span><span class="sxs-lookup"><span data-stu-id="b2a38-156">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="b2a38-157">向模型添加实体类型</span><span class="sxs-lookup"><span data-stu-id="b2a38-157">Add an Entity Type to the Model</span></span>

<span data-ttu-id="b2a38-158">在此步骤中，我们将使用 EF 设计器将实体类型添加到概念模型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-158">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="b2a38-159"> 请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="b2a38-159"> Note the following:</span></span>

-   <span data-ttu-id="b2a38-160">实体的**名称**与上面的**EntitySet**元素中的**EntityType**属性的值相对应。</span><span class="sxs-lookup"><span data-stu-id="b2a38-160">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="b2a38-161">属性名称对应于上面的**DefiningQuery**元素中的 SQL 语句返回的列名称。</span><span class="sxs-lookup"><span data-stu-id="b2a38-161">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="b2a38-162">在此示例中，实体键由三个属性组成以确保唯一键值。</span><span class="sxs-lookup"><span data-stu-id="b2a38-162">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="b2a38-163">在 EF 设计器中打开模型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-163">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="b2a38-164">双击 "DefiningQueryModel"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-164">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="b2a38-165">对以下消息说为 **"是"** ：</span><span class="sxs-lookup"><span data-stu-id="b2a38-165">Say **Yes** to the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

<span data-ttu-id="b2a38-167">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="b2a38-167">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="b2a38-168">右键单击设计器图面，然后选择 "**添加新**-&gt;"**实体 ...** "。</span><span class="sxs-lookup"><span data-stu-id="b2a38-168">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="b2a38-169">为 "**键" 属性**的 "实体名称" 和 " **CourseID** " 指定**GradeReport** 。</span><span class="sxs-lookup"><span data-stu-id="b2a38-169">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="b2a38-170">右键单击 " **GradeReport** " 实体，然后选择 "**添加新**-&gt;"**标量属性**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-170">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="b2a38-171">将属性的默认名称更改为**FirstName**。</span><span class="sxs-lookup"><span data-stu-id="b2a38-171">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="b2a38-172">添加另一个标量属性，并为名称指定**LastName** 。</span><span class="sxs-lookup"><span data-stu-id="b2a38-172">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="b2a38-173">添加另一个标量属性，并为该名称指定**等级**。</span><span class="sxs-lookup"><span data-stu-id="b2a38-173">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="b2a38-174">在 "**属性**" 窗口中，将**评分**的 "**类型**" 属性更改为**Decimal**。</span><span class="sxs-lookup"><span data-stu-id="b2a38-174">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="b2a38-175">选择**FirstName**和**LastName**属性。</span><span class="sxs-lookup"><span data-stu-id="b2a38-175">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="b2a38-176">在 "**属性**" 窗口中，将**EntityKey**属性值更改为**True**。</span><span class="sxs-lookup"><span data-stu-id="b2a38-176">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="b2a38-177">因此，将以下元素添加到 .edmx 文件的**CSDL**部分。</span><span class="sxs-lookup"><span data-stu-id="b2a38-177">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="b2a38-178">将定义查询映射到实体类型</span><span class="sxs-lookup"><span data-stu-id="b2a38-178">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="b2a38-179">在此步骤中，我们将使用 "映射详细信息" 窗口来映射概念性实体类型和存储实体类型。</span><span class="sxs-lookup"><span data-stu-id="b2a38-179">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="b2a38-180">右键单击设计图面上的 " **GradeReport** " 实体，然后选择 "**表映射**"。</span><span class="sxs-lookup"><span data-stu-id="b2a38-180">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="b2a38-181">将显示 "**映射详细信息**" 窗口。</span><span class="sxs-lookup"><span data-stu-id="b2a38-181">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="b2a38-182">从 **&lt;添加表或视图&gt;** 下拉列表中选择 " **GradeReport** " （位于**表**s 下）。</span><span class="sxs-lookup"><span data-stu-id="b2a38-182">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="b2a38-183">显示概念和存储**GradeReport**实体类型之间的默认映射。</span><span class="sxs-lookup"><span data-stu-id="b2a38-183">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="b2a38-184">![映射 Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="b2a38-184">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="b2a38-185">因此， **EntitySetMapping** 元素将添加到 .edmx 文件的映射部分。</span><span class="sxs-lookup"><span data-stu-id="b2a38-185">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   <span data-ttu-id="b2a38-186">编译应用程序。</span><span class="sxs-lookup"><span data-stu-id="b2a38-186">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="b2a38-187">在代码中调用定义查询</span><span class="sxs-lookup"><span data-stu-id="b2a38-187">Call the Defining Query in your Code</span></span>

<span data-ttu-id="b2a38-188">现在可以使用**GradeReport**实体类型执行定义查询。</span><span class="sxs-lookup"><span data-stu-id="b2a38-188">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
