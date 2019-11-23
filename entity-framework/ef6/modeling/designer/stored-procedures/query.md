---
title: 设计器查询存储过程-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 2e0092b526278597e8477d47eeb642598647bb91
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182485"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="7d1b9-102">设计器查询存储过程</span><span class="sxs-lookup"><span data-stu-id="7d1b9-102">Designer Query Stored Procedures</span></span>
<span data-ttu-id="7d1b9-103">此分步演练演示如何使用 Entity Framework Designer （EF 设计器）将存储过程导入到模型中，然后调用导入的存储过程来检索结果。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-103">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="7d1b9-104">请注意，Code First 不支持映射到存储过程或函数。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-104">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="7d1b9-105">但是，可以使用 DbSet. SqlQuery 方法调用存储过程或函数。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-105">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="7d1b9-106">例如：</span><span class="sxs-lookup"><span data-stu-id="7d1b9-106">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="7d1b9-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="7d1b9-107">Prerequisites</span></span>

<span data-ttu-id="7d1b9-108">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="7d1b9-108">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="7d1b9-109">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-109">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="7d1b9-110">[School 示例数据库](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-110">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="7d1b9-111">设置项目</span><span class="sxs-lookup"><span data-stu-id="7d1b9-111">Set up the Project</span></span>

-   <span data-ttu-id="7d1b9-112">打开 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-112">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="7d1b9-113">选择 "**文件-&gt;"&gt; 项目**</span><span class="sxs-lookup"><span data-stu-id="7d1b9-113">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="7d1b9-114">在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台**" 模板。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-114">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="7d1b9-115">输入 " **EFwithSProcsSample** " 作为名称。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-115">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="7d1b9-116">选择 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-116">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="7d1b9-117">创建模型</span><span class="sxs-lookup"><span data-stu-id="7d1b9-117">Create a Model</span></span>

-   <span data-ttu-id="7d1b9-118">在解决方案资源管理器中右键单击项目，然后选择 "**添加-&gt; 新项**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-118">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="7d1b9-119">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-119">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="7d1b9-120">输入**EFwithSProcsModel**作为文件名，然后单击 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-120">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="7d1b9-121">在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-121">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="7d1b9-122">单击 " **新建连接**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-122">Click **New Connection**.</span></span>  
    <span data-ttu-id="7d1b9-123">在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择身份验证方法，为数据库名称键入 **School** ，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-123">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="7d1b9-124">"选择您的数据连接" 对话框将通过数据库连接设置进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-124">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="7d1b9-125">在 "选择数据库对象" 对话框中，选中 "**表** " 复选框以选择所有表。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-125">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="7d1b9-126">另外，在 "**存储过程和函数**" 节点下选择以下存储过程： **GetStudentGrades**和**GetDepartmentName**。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-126">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![导入](~/ef6/media/import.jpg)

    <span data-ttu-id="7d1b9-128">*从 Visual Studio 2012 开始，EF 设计器支持存储过程的大容量导入。默认情况下，将选中 "将**选定的存储过程和函数导入 entity 模型**"。*</span><span class="sxs-lookup"><span data-stu-id="7d1b9-128">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="7d1b9-129">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-129">Click **Finish**.</span></span>

<span data-ttu-id="7d1b9-130">默认情况下，返回多个列的每个导入的存储过程或函数的结果形状将自动成为新的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-130">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="7d1b9-131">在此示例中，我们想要将 **GetStudentGrades**函数的结果映射到 **StudentGrade**实体，并将**GetDepartmentName**结果映射到 "**无**" （默认值为 "**无**"）。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-131">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="7d1b9-132">为了使函数导入返回实体类型，由相应的存储过程返回的列必须与返回的实体类型的标量属性完全匹配。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-132">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="7d1b9-133">函数导入还可以返回简单类型、复杂类型或无值的集合。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-133">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="7d1b9-134">右键单击设计图面，然后选择 " **模型浏览器**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-134">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="7d1b9-135">在**模型浏览器**中，选择 " **函数导入**"，然后双击 " **GetStudentGrades** " 函数。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-135">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="7d1b9-136">在 "编辑函数导入" 对话框中，选择 " **实体**" 然后选择 " **StudentGrade**"。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-136">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="7d1b9-137">*函数导入**对话框顶部**的 "函数**导入是可组合**的" 复选框允许您映射到可组合函数。如果选中此复选框，则 "**存储过程/函数名称**" 下拉列表中将只显示可组合函数（表值函数）。如果不选中此框，则列表中将只显示不可组合的函数。*</span><span class="sxs-lookup"><span data-stu-id="7d1b9-137">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="7d1b9-138">使用模型</span><span class="sxs-lookup"><span data-stu-id="7d1b9-138">Use the Model</span></span>

<span data-ttu-id="7d1b9-139">打开**Program.cs**文件，其中定义了**Main**方法。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-139">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="7d1b9-140">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-140">Add the following code into the Main function.</span></span>

<span data-ttu-id="7d1b9-141">此代码将调用两个存储过程： **GetStudentGrades** （为指定的*StudentId*返回**StudentGrades** ）和**GetDepartmentName** （返回 output 参数中部门的名称）。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-141">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

<span data-ttu-id="7d1b9-142">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-142">Compile and run the application.</span></span> <span data-ttu-id="7d1b9-143">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="7d1b9-143">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="7d1b9-144">输出参数</span><span class="sxs-lookup"><span data-stu-id="7d1b9-144">Output Parameters</span></span>
-----------------

<span data-ttu-id="7d1b9-145">如果使用了 output 参数，则在完全读取结果之前，它们的值将不可用。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-145">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="7d1b9-146">这是因为 DbDataReader 的基础行为，有关详细信息，请参阅[使用 DataReader 检索数据](https://go.microsoft.com/fwlink/?LinkID=398589)。</span><span class="sxs-lookup"><span data-stu-id="7d1b9-146">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
