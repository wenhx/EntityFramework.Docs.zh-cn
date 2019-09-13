---
title: 设计器表拆分-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f5e7532e6c0b473d8ce77cbd11e3e673b0af6cbe
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921785"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="e4c04-102">设计器表拆分</span><span class="sxs-lookup"><span data-stu-id="e4c04-102">Designer Table Splitting</span></span>
<span data-ttu-id="e4c04-103">本演练演示如何通过使用 Entity Framework Designer （EF 设计器）修改模型，将多个实体类型映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="e4c04-103">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="e4c04-104">当使用延迟加载加载对象时，可能需要使用表拆分延迟加载某些属性。</span><span class="sxs-lookup"><span data-stu-id="e4c04-104">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="e4c04-105"> 您可以将可能包含大量数据的属性分成单独的实体，并且仅在需要时加载。</span><span class="sxs-lookup"><span data-stu-id="e4c04-105"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="e4c04-106">下图显示了在使用 EF 设计器时使用的主窗口。</span><span class="sxs-lookup"><span data-stu-id="e4c04-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF 设计器](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="e4c04-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="e4c04-108">Prerequisites</span></span>

<span data-ttu-id="e4c04-109">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="e4c04-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="e4c04-110">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="e4c04-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="e4c04-111">[School 示例数据库](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="e4c04-111">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e4c04-112">设置项目</span><span class="sxs-lookup"><span data-stu-id="e4c04-112">Set up the Project</span></span>

<span data-ttu-id="e4c04-113">本演练使用 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="e4c04-113">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="e4c04-114">打开 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="e4c04-114">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="e4c04-115">在 **“文件”** 菜单上，指向 **“新建”** ，然后单击 **“项目”** 。</span><span class="sxs-lookup"><span data-stu-id="e4c04-115">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="e4c04-116">在左窗格中，单击 "Visual\#C"，然后选择 "控制台应用程序" 模板。</span><span class="sxs-lookup"><span data-stu-id="e4c04-116">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="e4c04-117">输入**TableSplittingSample**作为项目名称，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="e4c04-117">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="e4c04-118">基于 School 数据库创建模型</span><span class="sxs-lookup"><span data-stu-id="e4c04-118">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="e4c04-119">右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-119">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="e4c04-120">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。</span><span class="sxs-lookup"><span data-stu-id="e4c04-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="e4c04-121">输入**TableSplittingModel**作为文件名，然后单击 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-121">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="e4c04-122">在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步"。**</span><span class="sxs-lookup"><span data-stu-id="e4c04-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="e4c04-123">单击 "新建连接"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-123">Click New Connection.</span></span> <span data-ttu-id="e4c04-124">在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择 "身份验证方法"， **键入 School** 作为数据库名称，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="e4c04-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="e4c04-125">"选择您的数据连接" 对话框将通过数据库连接设置进行更新。</span><span class="sxs-lookup"><span data-stu-id="e4c04-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="e4c04-126">在 "选择数据库对象" 对话框中，展开 "**表** " 节点并检查**Person**表。</span><span class="sxs-lookup"><span data-stu-id="e4c04-126">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="e4c04-127">这会将指定的表添加到**School**模型。</span><span class="sxs-lookup"><span data-stu-id="e4c04-127">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="e4c04-128">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-128">Click **Finish**.</span></span>

<span data-ttu-id="e4c04-129">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="e4c04-129">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="e4c04-130">您在 " **选择数据库对象** " 对话框中选择的所有对象都将添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="e4c04-130">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="e4c04-131">将两个实体映射到单个表</span><span class="sxs-lookup"><span data-stu-id="e4c04-131">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="e4c04-132">在本节中，您将把**Person**实体拆分为两个实体，然后将它们映射到一个表。</span><span class="sxs-lookup"><span data-stu-id="e4c04-132">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="e4c04-133">**Person**实体不包含任何可能包含大量数据的属性;它仅用作示例。</span><span class="sxs-lookup"><span data-stu-id="e4c04-133">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="e4c04-134">右键单击设计图面的空白区域，指向 " **添加新**项"，然后单击 " **实体**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-134">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="e4c04-135">此时将显示 " **新建实体** " 对话框。</span><span class="sxs-lookup"><span data-stu-id="e4c04-135">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="e4c04-136">为 " **实体名称**" 键入 **HireInfo** ，为**键属性**名称键入 " **PersonID** "。</span><span class="sxs-lookup"><span data-stu-id="e4c04-136">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="e4c04-137">单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="e4c04-137">Click **OK**.</span></span>
-   <span data-ttu-id="e4c04-138">新的实体类型创建完毕，并且显示在设计图面上。</span><span class="sxs-lookup"><span data-stu-id="e4c04-138">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="e4c04-139">选择 **Person**  \*\*\*\* 实体类型的"雇佣日期"属性，并按Ctrl+X 键。</span><span class="sxs-lookup"><span data-stu-id="e4c04-139">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="e4c04-140">选择**HireInfo** 实体，并按**Ctrl + V**键。</span><span class="sxs-lookup"><span data-stu-id="e4c04-140">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="e4c04-141">创建**Person**和**HireInfo**之间的关联。</span><span class="sxs-lookup"><span data-stu-id="e4c04-141">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="e4c04-142">为此，请右键单击设计图面的空白区域，指向 " **添加新**项"，然后单击 " **关联**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-142">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="e4c04-143">此时将显示 " **添加关联** " 对话框。</span><span class="sxs-lookup"><span data-stu-id="e4c04-143">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="e4c04-144">默认情况下，指定**PersonHireInfo**名称。</span><span class="sxs-lookup"><span data-stu-id="e4c04-144">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="e4c04-145">指定关系两端的重数**1 （一）** 。</span><span class="sxs-lookup"><span data-stu-id="e4c04-145">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="e4c04-146">按“确定”。</span><span class="sxs-lookup"><span data-stu-id="e4c04-146">Press **OK**.</span></span>

<span data-ttu-id="e4c04-147">下一步需要 " **映射详细信息** " 窗口。</span><span class="sxs-lookup"><span data-stu-id="e4c04-147">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="e4c04-148">如果看不到此窗口，请右键单击设计图面，然后选择 "**映射详细信息**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-148">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="e4c04-149">选择 **HireInfo** 实体类型，然后在 " **映射详细信息**  " 窗口中单击 **&lt;"添加表或视图&gt;** "。</span><span class="sxs-lookup"><span data-stu-id="e4c04-149">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="e4c04-150">从 "  **&lt;添加表或视图&gt;**  字段" 下拉列表中选择 "**人员**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-150">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="e4c04-151">此列表包含所选实体可以映射到的表或视图。</span><span class="sxs-lookup"><span data-stu-id="e4c04-151">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="e4c04-152">默认情况下，应映射相应的属性。</span><span class="sxs-lookup"><span data-stu-id="e4c04-152">The appropriate properties should be mapped by default.</span></span>

    ![映射](~/ef6/media/mapping.png)

-   <span data-ttu-id="e4c04-154">在设计图面上选择 " **PersonHireInfo** " 关联。</span><span class="sxs-lookup"><span data-stu-id="e4c04-154">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="e4c04-155">右键单击设计图面上的关联，然后选择 "**属性**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-155">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="e4c04-156">在 "**属性**" 窗口中，选择 "**引用约束**" 属性，然后单击省略号按钮。</span><span class="sxs-lookup"><span data-stu-id="e4c04-156">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="e4c04-157">从 "**主体**" 下拉列表中选择 "**人员**"。</span><span class="sxs-lookup"><span data-stu-id="e4c04-157">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="e4c04-158">按“确定”。</span><span class="sxs-lookup"><span data-stu-id="e4c04-158">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="e4c04-159">使用模型</span><span class="sxs-lookup"><span data-stu-id="e4c04-159">Use the Model</span></span>

-   <span data-ttu-id="e4c04-160">将以下代码粘贴到 Main 方法中。</span><span class="sxs-lookup"><span data-stu-id="e4c04-160">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="e4c04-161">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="e4c04-161">Compile and run the application.</span></span>

<span data-ttu-id="e4c04-162">由于运行此应用程序，以下 T-sql 语句针对**School**数据库执行。</span><span class="sxs-lookup"><span data-stu-id="e4c04-162">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="e4c04-163">执行上下文时，执行了以下**插入**操作。SaveChanges （）并结合**Person**和**HireInfo**实体中的数据</span><span class="sxs-lookup"><span data-stu-id="e4c04-163">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![Insert](~/ef6/media/insert.png)

-   <span data-ttu-id="e4c04-165">执行上下文时，执行了以下**SELECT** 。FirstOrDefault （）并只选择映射到**人员**的列</span><span class="sxs-lookup"><span data-stu-id="e4c04-165">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![选择1](~/ef6/media/select1.png)

-   <span data-ttu-id="e4c04-167">在访问导航属性 existingPerson 的结果中执行了下面的**SELECT**语句，并只选择了映射到**HireInfo**的列</span><span class="sxs-lookup"><span data-stu-id="e4c04-167">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![选择2](~/ef6/media/select2.png)
