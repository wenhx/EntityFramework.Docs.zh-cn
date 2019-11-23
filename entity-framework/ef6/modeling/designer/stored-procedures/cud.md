---
title: 设计器 CUD 存储过程-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
ms.openlocfilehash: bdb0df969c33d5ad3f103bfa9af6002c9c2bb9b3
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813552"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="68795-102">设计器 CUD 存储过程</span><span class="sxs-lookup"><span data-stu-id="68795-102">Designer CUD Stored Procedures</span></span>

<span data-ttu-id="68795-103">此分步演练演示如何使用 Entity Framework Designer （EF 设计器）将实体类型的 create\\插入、更新和删除（CUD）操作映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="68795-103">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="68795-104"> 默认情况下，实体框架会自动为 CUD 操作生成 SQL 语句，但也可以将存储过程映射到这些操作。</span><span class="sxs-lookup"><span data-stu-id="68795-104"> By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="68795-105">请注意，Code First 不支持映射到存储过程或函数。</span><span class="sxs-lookup"><span data-stu-id="68795-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="68795-106">但是，可以使用 DbSet. SqlQuery 方法调用存储过程或函数。</span><span class="sxs-lookup"><span data-stu-id="68795-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="68795-107">例如：</span><span class="sxs-lookup"><span data-stu-id="68795-107">For example:</span></span>

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="68795-108">将 CUD 操作映射到存储过程时的注意事项</span><span class="sxs-lookup"><span data-stu-id="68795-108">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="68795-109">将 CUD 操作映射到存储过程时，请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="68795-109">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span>

- <span data-ttu-id="68795-110">如果要将其中一个 CUD 操作映射到存储过程，请映射所有这些操作。</span><span class="sxs-lookup"><span data-stu-id="68795-110">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="68795-111">如果未映射所有这三个映射，则未映射的操作将失败（如果执行）并引发 **UpdateException** 。</span><span class="sxs-lookup"><span data-stu-id="68795-111">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
- <span data-ttu-id="68795-112">必须将存储过程的每个参数映射到实体属性。</span><span class="sxs-lookup"><span data-stu-id="68795-112">You must map every parameter of the stored procedure to entity properties.</span></span>
- <span data-ttu-id="68795-113">如果服务器为插入的行生成主键值，则必须将此值映射回实体的键属性。</span><span class="sxs-lookup"><span data-stu-id="68795-113">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="68795-114">在下面的示例中， **InsertPerson** 存储过程返回新创建的主键作为存储过程的结果集的一部分。</span><span class="sxs-lookup"><span data-stu-id="68795-114">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="68795-115">使用&gt;EF 设计器 功能 **&lt;添加结果绑定**，将主键映射到实体键（**PersonID**）。</span><span class="sxs-lookup"><span data-stu-id="68795-115">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
- <span data-ttu-id="68795-116">存储过程调用映射到概念模型中的实体1:1。</span><span class="sxs-lookup"><span data-stu-id="68795-116">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="68795-117">例如，如果在概念模型中实现了继承层次结构，然后映射**父级**（base）和**子**（派生）实体的 CUD 存储过程，则保存**子**更改将仅调用**子**对象的存储过程，而不会触发**父级**的存储过程调用。</span><span class="sxs-lookup"><span data-stu-id="68795-117">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68795-118">先决条件</span><span class="sxs-lookup"><span data-stu-id="68795-118">Prerequisites</span></span>

<span data-ttu-id="68795-119">若要完成此演练，您需要：</span><span class="sxs-lookup"><span data-stu-id="68795-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="68795-120">Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="68795-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="68795-121">[School 示例数据库](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="68795-121">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="68795-122">设置项目</span><span class="sxs-lookup"><span data-stu-id="68795-122">Set up the Project</span></span>

- <span data-ttu-id="68795-123">打开 Visual Studio 2012。</span><span class="sxs-lookup"><span data-stu-id="68795-123">Open Visual Studio 2012.</span></span>
- <span data-ttu-id="68795-124">选择 "**文件-&gt;"&gt; 项目**</span><span class="sxs-lookup"><span data-stu-id="68795-124">Select **File-&gt; New -&gt; Project**</span></span>
- <span data-ttu-id="68795-125">在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台**" 模板。</span><span class="sxs-lookup"><span data-stu-id="68795-125">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
- <span data-ttu-id="68795-126">输入 " **CUDSProcsSample** " 作为名称。</span><span class="sxs-lookup"><span data-stu-id="68795-126">Enter **CUDSProcsSample** as the name.</span></span>
- <span data-ttu-id="68795-127">选择 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="68795-127">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="68795-128">创建模型</span><span class="sxs-lookup"><span data-stu-id="68795-128">Create a Model</span></span>

- <span data-ttu-id="68795-129">右键单击 "解决方案资源管理器中的项目名称，然后选择"**添加-&gt; 新项**"。</span><span class="sxs-lookup"><span data-stu-id="68795-129">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
- <span data-ttu-id="68795-130">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。</span><span class="sxs-lookup"><span data-stu-id="68795-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
- <span data-ttu-id="68795-131">输入**CUDSProcs**作为文件名，然后单击 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="68795-131">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
- <span data-ttu-id="68795-132">在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。</span><span class="sxs-lookup"><span data-stu-id="68795-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
- <span data-ttu-id="68795-133">单击 " **新建连接**"。</span><span class="sxs-lookup"><span data-stu-id="68795-133">Click **New Connection**.</span></span> <span data-ttu-id="68795-134">在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择身份验证方法，为数据库名称键入 **School** ，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="68795-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="68795-135">"选择您的数据连接" 对话框将通过数据库连接设置进行更新。</span><span class="sxs-lookup"><span data-stu-id="68795-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
- <span data-ttu-id="68795-136">在 "选择数据库对象" 对话框中的 " **表** " 节点下，选择 **Person**表。</span><span class="sxs-lookup"><span data-stu-id="68795-136">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
- <span data-ttu-id="68795-137">另外，在 "**存储过程和函数**" 节点下选择以下存储过程： **DeletePerson**、 **InsertPerson**和**UpdatePerson**。</span><span class="sxs-lookup"><span data-stu-id="68795-137">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span>
- <span data-ttu-id="68795-138">从 Visual Studio 2012 开始，EF 设计器支持存储过程的大容量导入。</span><span class="sxs-lookup"><span data-stu-id="68795-138">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="68795-139">默认情况下，将选中 "将**选定的存储过程和函数导入实体模型**"。</span><span class="sxs-lookup"><span data-stu-id="68795-139">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="68795-140">由于在此示例中，我们已存储了用于插入、更新和删除实体类型的存储过程，因此我们不想将它们导入，并将取消选中此复选框。</span><span class="sxs-lookup"><span data-stu-id="68795-140">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span>

    ![导入过程](~/ef6/media/importsprocs.jpg)

- <span data-ttu-id="68795-142">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="68795-142">Click **Finish**.</span></span>
    <span data-ttu-id="68795-143">显示了 EF 设计器（提供编辑模型的设计图面）。</span><span class="sxs-lookup"><span data-stu-id="68795-143">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="68795-144">将 Person 实体映射到存储过程</span><span class="sxs-lookup"><span data-stu-id="68795-144">Map the Person Entity to Stored Procedures</span></span>

- <span data-ttu-id="68795-145">右键单击 " **人员**" 实体类型，然后选择 " **存储过程映射**"。</span><span class="sxs-lookup"><span data-stu-id="68795-145">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
- <span data-ttu-id="68795-146">存储过程映射显示在 " **映射详细信息**" "窗口中。</span><span class="sxs-lookup"><span data-stu-id="68795-146">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
- <span data-ttu-id="68795-147">单击 **&lt;选择 "插入函数"&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="68795-147">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="68795-148">该字段变成一个下拉列表，该列表中包含存储模型中可以映射到概念模型中的实体类型的存储过程。</span><span class="sxs-lookup"><span data-stu-id="68795-148">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="68795-149">从下拉列表中选择 " **InsertPerson** "。</span><span class="sxs-lookup"><span data-stu-id="68795-149">Select **InsertPerson** from the drop-down list.</span></span>
- <span data-ttu-id="68795-150">此时将显示存储过程参数和实体属性之间的默认映射。</span><span class="sxs-lookup"><span data-stu-id="68795-150">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="68795-151">请注意，箭头指示映射方向：向存储过程参数提供属性值。</span><span class="sxs-lookup"><span data-stu-id="68795-151">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
- <span data-ttu-id="68795-152">单击 **&lt;添加结果绑定&gt;** 。</span><span class="sxs-lookup"><span data-stu-id="68795-152">Click **&lt;Add Result Binding&gt;**.</span></span>
- <span data-ttu-id="68795-153">键入 **NewPersonID**， **InsertPerson** 存储过程返回的参数的名称。</span><span class="sxs-lookup"><span data-stu-id="68795-153">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="68795-154">请确保不要键入前导空格或尾随空格。</span><span class="sxs-lookup"><span data-stu-id="68795-154">Make sure not to type leading or trailing spaces.</span></span>
- <span data-ttu-id="68795-155">按 **enter**。</span><span class="sxs-lookup"><span data-stu-id="68795-155">Press **Enter**.</span></span>
- <span data-ttu-id="68795-156">默认情况下， **NewPersonID** 映射到实体键 **PersonID**。</span><span class="sxs-lookup"><span data-stu-id="68795-156">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="68795-157">请注意，箭头指示映射的方向：向属性提供结果列的值。</span><span class="sxs-lookup"><span data-stu-id="68795-157">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![映射详细信息](~/ef6/media/mappingdetails.png)

- <span data-ttu-id="68795-159">单击 " **&lt;&gt; 选择" 更新函数**"，然后从生成的下拉列表中选择" **UpdatePerson** "。</span><span class="sxs-lookup"><span data-stu-id="68795-159">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="68795-160">此时将显示存储过程参数和实体属性之间的默认映射。</span><span class="sxs-lookup"><span data-stu-id="68795-160">Default mappings between stored procedure parameters and entity properties appear.</span></span>
- <span data-ttu-id="68795-161">单击 " **&lt;&gt; 选择" 删除函数**"，然后从生成的下拉列表中选择" **DeletePerson** "。</span><span class="sxs-lookup"><span data-stu-id="68795-161">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="68795-162">此时将显示存储过程参数和实体属性之间的默认映射。</span><span class="sxs-lookup"><span data-stu-id="68795-162">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="68795-163"> **用户** 实体类型的插入、更新和删除操作现已映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="68795-163">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="68795-164">如果要在使用存储过程更新或删除实体时启用并发检查，请使用下列选项之一：</span><span class="sxs-lookup"><span data-stu-id="68795-164">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

- <span data-ttu-id="68795-165">使用**OUTPUT**参数从存储过程中返回受影响的行数，并选中参数名称旁的 "**受影响的行" 参数** 复选框。</span><span class="sxs-lookup"><span data-stu-id="68795-165">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="68795-166">如果调用操作时返回的值为零，则将引发  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="68795-166">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
- <span data-ttu-id="68795-167">选中要用于并发检查的属性旁边的 "**使用原始值**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="68795-167">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="68795-168">尝试更新时，在将数据写回数据库时，将使用最初从数据库读取的属性的值。</span><span class="sxs-lookup"><span data-stu-id="68795-168">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="68795-169">如果值与数据库中的值不匹配，则将引发**OptimisticConcurrencyException** 。</span><span class="sxs-lookup"><span data-stu-id="68795-169">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="68795-170">使用模型</span><span class="sxs-lookup"><span data-stu-id="68795-170">Use the Model</span></span>

<span data-ttu-id="68795-171">打开**Program.cs**文件，其中定义了**Main**方法。</span><span class="sxs-lookup"><span data-stu-id="68795-171">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="68795-172">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="68795-172">Add the following code into the Main function.</span></span>

<span data-ttu-id="68795-173">此代码创建一个新的**Person**对象，然后更新该对象，最后删除该对象。</span><span class="sxs-lookup"><span data-stu-id="68795-173">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- <span data-ttu-id="68795-174">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="68795-174">Compile and run the application.</span></span> <span data-ttu-id="68795-175">该程序生成以下输出 \*</span><span class="sxs-lookup"><span data-stu-id="68795-175">The program produces the following output \*</span></span>

> [!NOTE]
> <span data-ttu-id="68795-176">PersonID 是由服务器自动生成的，因此很可能会看到不同的数字 \*</span><span class="sxs-lookup"><span data-stu-id="68795-176">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="68795-177">如果使用的是 Visual Studio 的旗舰版，则可以将 Intellitrace 与调试器结合使用，以查看执行的 SQL 语句。</span><span class="sxs-lookup"><span data-stu-id="68795-177">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Intellitrace](~/ef6/media/intellitrace.png)
