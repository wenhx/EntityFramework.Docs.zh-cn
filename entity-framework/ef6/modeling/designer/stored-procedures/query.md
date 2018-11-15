---
title: 设计器的查询存储过程-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 04478ea1c8cd43a7ba4ee788e464992af3de7f64
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283896"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="8cc93-102">设计器的查询存储过程</span><span class="sxs-lookup"><span data-stu-id="8cc93-102">Designer Query Stored Procedures</span></span>
<span data-ttu-id="8cc93-103">此分步演练演示如何使用实体框架设计器 （EF 设计器） 导入存储的过程转换为模型，然后调用导入的存储的过程来检索结果。</span><span class="sxs-lookup"><span data-stu-id="8cc93-103">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="8cc93-104">请注意，Code First 不支持映射到存储的过程或函数。</span><span class="sxs-lookup"><span data-stu-id="8cc93-104">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="8cc93-105">但是，可以通过使用 System.Data.Entity.DbSet.SqlQuery 方法中调用存储的过程或函数。</span><span class="sxs-lookup"><span data-stu-id="8cc93-105">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="8cc93-106">例如：</span><span class="sxs-lookup"><span data-stu-id="8cc93-106">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="8cc93-107">系统必备</span><span class="sxs-lookup"><span data-stu-id="8cc93-107">Prerequisites</span></span>

<span data-ttu-id="8cc93-108">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="8cc93-108">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="8cc93-109">最新版本的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="8cc93-109">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="8cc93-110">[School 示例数据库](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="8cc93-110">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="8cc93-111">设置项目</span><span class="sxs-lookup"><span data-stu-id="8cc93-111">Set up the Project</span></span>

-   <span data-ttu-id="8cc93-112">打开 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="8cc93-112">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="8cc93-113">选择**文件-&gt;新增-&gt;项目**</span><span class="sxs-lookup"><span data-stu-id="8cc93-113">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="8cc93-114">在左窗格中，单击**Visual C\#**，然后选择**控制台**模板。</span><span class="sxs-lookup"><span data-stu-id="8cc93-114">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="8cc93-115">输入**EFwithSProcsSample**作为名称。</span><span class="sxs-lookup"><span data-stu-id="8cc93-115">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="8cc93-116">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="8cc93-116">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="8cc93-117">创建模型</span><span class="sxs-lookup"><span data-stu-id="8cc93-117">Create a Model</span></span>

-   <span data-ttu-id="8cc93-118">右键单击解决方案资源管理器中的项目并选择**添加-&gt;新项**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-118">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="8cc93-119">选择**数据**从左侧的菜单，然后选择**ADO.NET 实体数据模型**模板窗格中。</span><span class="sxs-lookup"><span data-stu-id="8cc93-119">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="8cc93-120">输入**EFwithSProcsModel.edmx**以及该文件的名称，然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-120">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="8cc93-121">在选择模型内容对话框中，选择**从数据库生成**，然后单击**下一步**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-121">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="8cc93-122">单击**新的连接**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-122">Click **New Connection**.</span></span>  
    <span data-ttu-id="8cc93-123">在连接属性对话框中，输入服务器名称 (例如， **(localdb)\\mssqllocaldb**)，选择身份验证方法、 类型**学校**数据库名称，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-123">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="8cc93-124">选择数据连接对话框中使用数据库连接设置更新。</span><span class="sxs-lookup"><span data-stu-id="8cc93-124">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="8cc93-125">在选择数据库对象对话框中，检查**表**复选框以选择所有表。</span><span class="sxs-lookup"><span data-stu-id="8cc93-125">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="8cc93-126">此外，请选择下的以下存储的过程**存储过程和函数**节点： **GetStudentGrades**并**GetDepartmentName**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-126">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![导入](~/ef6/media/import.jpg)

    <span data-ttu-id="8cc93-128">*从 Visual Studio 2012 EF 设计器支持大容量导入的存储过程。**导入选定的存储的过程和函数到实体模型**默认选中的。*</span><span class="sxs-lookup"><span data-stu-id="8cc93-128">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="8cc93-129">单击 **“完成”**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-129">Click **Finish**.</span></span>

<span data-ttu-id="8cc93-130">默认情况下，每个导入存储的过程或函数，可返回多个列的结果形状将自动成为新的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="8cc93-130">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="8cc93-131">在此示例中我们想要的结果映射**GetStudentGrades**函数来**StudentGrade**实体和结果**GetDepartmentName**到**none** (**无**是默认值)。</span><span class="sxs-lookup"><span data-stu-id="8cc93-131">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="8cc93-132">函数导入返回实体类型，相应的存储过程返回的列必须与返回的实体类型的标量属性完全匹配。</span><span class="sxs-lookup"><span data-stu-id="8cc93-132">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="8cc93-133">函数导入还可以返回简单类型、 复杂类型或没有值的集合。</span><span class="sxs-lookup"><span data-stu-id="8cc93-133">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="8cc93-134">右键单击设计图面，然后选择**模型浏览器**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-134">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="8cc93-135">在中**模型浏览器**，选择**函数导入**，然后双击**GetStudentGrades**函数。</span><span class="sxs-lookup"><span data-stu-id="8cc93-135">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="8cc93-136">在编辑函数导入对话框中，选择**实体**，然后选择**StudentGrade**。</span><span class="sxs-lookup"><span data-stu-id="8cc93-136">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="8cc93-137">\***是可组合的函数导入**顶部的复选框**函数导入**对话框将允许您将映射到撰写的函数。如果不要选中此框，仅可组合的函数 （表值函数） 会出现在**存储过程 / 函数名称**下拉列表。如果不选中此框，仅非撰写的函数将在列表中所示。\*</span><span class="sxs-lookup"><span data-stu-id="8cc93-137">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="8cc93-138">使用模型</span><span class="sxs-lookup"><span data-stu-id="8cc93-138">Use the Model</span></span>

<span data-ttu-id="8cc93-139">打开**Program.cs**文件，其中**Main**方法的定义。</span><span class="sxs-lookup"><span data-stu-id="8cc93-139">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="8cc93-140">以下代码添加到 Main 函数。</span><span class="sxs-lookup"><span data-stu-id="8cc93-140">Add the following code into the Main function.</span></span>

<span data-ttu-id="8cc93-141">该代码调用两个存储的过程： **GetStudentGrades** (返回**StudentGrades**指定*StudentId*) 和**GetDepartmentName**（输出参数中返回的部门名称）。</span><span class="sxs-lookup"><span data-stu-id="8cc93-141">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

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

<span data-ttu-id="8cc93-142">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="8cc93-142">Compile and run the application.</span></span> <span data-ttu-id="8cc93-143">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="8cc93-143">The program produces the following output:</span></span>

```
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="8cc93-144">输出参数</span><span class="sxs-lookup"><span data-stu-id="8cc93-144">Output Parameters</span></span>
-----------------

<span data-ttu-id="8cc93-145">如果使用输出参数，其值将不可用，直到已完全读取结果。</span><span class="sxs-lookup"><span data-stu-id="8cc93-145">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="8cc93-146">这是由于 DbDataReader 的基础行为，请参阅[使用 DataReader 检索数据](https://go.microsoft.com/fwlink/?LinkID=398589)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="8cc93-146">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
