---
title: 设计器 TPT 继承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
ms.openlocfilehash: 84330fba4807620aa242a70cd8ac76a60284416d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415045"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="5234c-102">设计器 TPT 继承</span><span class="sxs-lookup"><span data-stu-id="5234c-102">Designer TPT Inheritance</span></span>
<span data-ttu-id="5234c-103">此分步演练演示如何使用 Entity Framework Designer （EF 设计器）实现模型中的每种类型一个表（TPT）继承。</span><span class="sxs-lookup"><span data-stu-id="5234c-103">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="5234c-104">每种类型一个表继承使用数据库中单独的表为继承层次结构中的每种类型维护非继承属性和键属性的数据。</span><span class="sxs-lookup"><span data-stu-id="5234c-104">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="5234c-105">在本演练中，我们会将**课程**（基类型）、 **OnlineCourse** （派生自课程）和 **为 onsitecourse** （从**课程**派生）到同名的表。</span><span class="sxs-lookup"><span data-stu-id="5234c-105">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="5234c-106">我们将从数据库创建一个模型，然后更改模型以实现 TPT 继承。</span><span class="sxs-lookup"><span data-stu-id="5234c-106">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="5234c-107">还可以从 Model First 开始，然后从模型生成数据库。</span><span class="sxs-lookup"><span data-stu-id="5234c-107">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="5234c-108">默认情况下，EF 设计器会使用 TPT 策略，因此该模型中的任何继承都将映射到单独的表。</span><span class="sxs-lookup"><span data-stu-id="5234c-108">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="5234c-109">其他继承选项</span><span class="sxs-lookup"><span data-stu-id="5234c-109">Other Inheritance Options</span></span>

<span data-ttu-id="5234c-110">每个层次结构一个表（TPH）是另一种类型的继承，其中，一个数据库表用于维护继承层次结构中所有实体类型的数据。</span><span class="sxs-lookup"><span data-stu-id="5234c-110">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span><span data-ttu-id="5234c-111">  有关如何用 Entity Designer 映射每个层次结构一个表继承的信息，请参阅[EF 设计器 TPH 继承](~/ef6/modeling/designer/inheritance/tph.md)。</span><span class="sxs-lookup"><span data-stu-id="5234c-111">  For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](~/ef6/modeling/designer/inheritance/tph.md).</span></span> 

<span data-ttu-id="5234c-112">请注意，实体框架运行时支持每个具体的表类型继承（TPC）和混合继承模型，但 EF 设计器不支持。</span><span class="sxs-lookup"><span data-stu-id="5234c-112">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="5234c-113">如果要使用 TPC 或混合继承，则有两个选项：使用 Code First 或手动编辑 EDMX 文件。</span><span class="sxs-lookup"><span data-stu-id="5234c-113">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="5234c-114">如果选择处理 EDMX 文件，"映射详细信息" 窗口将进入 "安全模式"，您将无法使用设计器来更改映射。</span><span class="sxs-lookup"><span data-stu-id="5234c-114">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5234c-115">先决条件</span><span class="sxs-lookup"><span data-stu-id="5234c-115">Prerequisites</span></span>

<span data-ttu-id="5234c-116">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="5234c-116">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="5234c-117">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="5234c-117">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="5234c-118">[School 示例数据库](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="5234c-118">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="5234c-119">设置项目</span><span class="sxs-lookup"><span data-stu-id="5234c-119">Set up the Project</span></span>

-   <span data-ttu-id="5234c-120">打开 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="5234c-120">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="5234c-121">选择 "**文件-&gt;"&gt; 项目**</span><span class="sxs-lookup"><span data-stu-id="5234c-121">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="5234c-122">在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台**" 模板。</span><span class="sxs-lookup"><span data-stu-id="5234c-122">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="5234c-123">输入 " **TPTDBFirstSample** " 作为名称。</span><span class="sxs-lookup"><span data-stu-id="5234c-123">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="5234c-124">选择“确定”。 \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="5234c-124">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="5234c-125">创建模型</span><span class="sxs-lookup"><span data-stu-id="5234c-125">Create a Model</span></span>

-   <span data-ttu-id="5234c-126">右键单击 "解决方案资源管理器中的项目，然后选择"**添加-&gt; 新项**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-126">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="5234c-127">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。</span><span class="sxs-lookup"><span data-stu-id="5234c-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="5234c-128">输入**TPTModel**作为文件名，然后单击 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-128">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="5234c-129">在 "选择模型内容" 对话框中，选择 " ** 从数据库生成**"，然后单击 " **下一步**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-129">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="5234c-130">单击 " **新建连接**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-130">Click **New Connection**.</span></span>
    <span data-ttu-id="5234c-131">在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择身份验证方法，为数据库名称键入 **School** ，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="5234c-131">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="5234c-132">"选择您的数据连接" 对话框将通过数据库连接设置进行更新。</span><span class="sxs-lookup"><span data-stu-id="5234c-132">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="5234c-133">在 "选择数据库对象" 对话框的 "表" 节点下，选择 " **部门**"、"**课程"、"OnlineCourse" 和 "为 onsitecourse** " 表。</span><span class="sxs-lookup"><span data-stu-id="5234c-133">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="5234c-134">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-134">Click **Finish**.</span></span>

<span data-ttu-id="5234c-135">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="5234c-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="5234c-136">您在 "选择数据库对象" 对话框中选择的所有对象都将添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="5234c-136">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="5234c-137">实现每种类型一个表继承</span><span class="sxs-lookup"><span data-stu-id="5234c-137">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="5234c-138">在设计图面上，右键单击**OnlineCourse**实体类型，然后选择 "**属性**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-138">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="5234c-139">在 "**属性**" 窗口中，将 "基类型" 属性设置为 "**课程**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-139">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="5234c-140">右键单击**为 onsitecourse**实体类型，然后选择 "**属性**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-140">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="5234c-141">在 "**属性**" 窗口中，将 "基类型" 属性设置为 "**课程**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-141">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="5234c-142">右键单击**OnlineCourse**和**课程**实体类型之间的关联（行）。</span><span class="sxs-lookup"><span data-stu-id="5234c-142">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="5234c-143">选择 "**从模型删除**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-143">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="5234c-144">右键单击**为 onsitecourse**和**课程**实体类型之间的关联。</span><span class="sxs-lookup"><span data-stu-id="5234c-144">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="5234c-145">选择 "**从模型删除**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-145">Select **Delete from Model**.</span></span>

<span data-ttu-id="5234c-146">现在，我们将从**OnlineCourse**和**为 onsitecourse**中删除**CourseID**属性，因为这些类从**课程**基类型继承**CourseID** 。</span><span class="sxs-lookup"><span data-stu-id="5234c-146">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="5234c-147">右键单击**OnlineCourse**实体类型的**CourseID**属性，然后选择 "**从模型中删除**"。</span><span class="sxs-lookup"><span data-stu-id="5234c-147">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="5234c-148">右键单击**为 onsitecourse**实体类型的**CourseID**属性，然后选择 "**从模型删除**"</span><span class="sxs-lookup"><span data-stu-id="5234c-148">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="5234c-149">至此已实现每种类型一个表继承。</span><span class="sxs-lookup"><span data-stu-id="5234c-149">Table-per-type inheritance is now implemented.</span></span>

![TPT](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="5234c-151">使用模型</span><span class="sxs-lookup"><span data-stu-id="5234c-151">Use the Model</span></span>

<span data-ttu-id="5234c-152">打开**Program.cs**文件，其中定义了**Main**方法。</span><span class="sxs-lookup"><span data-stu-id="5234c-152">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="5234c-153">将以下代码粘贴到**Main**函数中。</span><span class="sxs-lookup"><span data-stu-id="5234c-153">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="5234c-154">此代码执行三个查询。</span><span class="sxs-lookup"><span data-stu-id="5234c-154">The code executes three queries.</span></span> <span data-ttu-id="5234c-155">第一个查询返回与指定部门相关的所有**课程**。</span><span class="sxs-lookup"><span data-stu-id="5234c-155">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="5234c-156">第二个查询使用**OfType**方法返回与指定部门相关的**OnlineCourses** 。</span><span class="sxs-lookup"><span data-stu-id="5234c-156">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="5234c-157">第三个查询返回**OnsiteCourses**。</span><span class="sxs-lookup"><span data-stu-id="5234c-157">The third query returns **OnsiteCourses**.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
