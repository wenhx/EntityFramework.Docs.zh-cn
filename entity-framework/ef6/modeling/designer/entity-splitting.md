---
title: 设计器实体拆分-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415261"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="50609-102">设计器实体拆分</span><span class="sxs-lookup"><span data-stu-id="50609-102">Designer Entity Splitting</span></span>
<span data-ttu-id="50609-103">本演练演示如何通过使用 Entity Framework Designer （EF 设计器）修改模型，将一个实体类型映射到两个表。</span><span class="sxs-lookup"><span data-stu-id="50609-103">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="50609-104">如果多个表共享同一个键，则可以将一个实体映射到这些表。</span><span class="sxs-lookup"><span data-stu-id="50609-104">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="50609-105">将实体类型映射到两个表的概念可以轻松地扩展为将一个实体类型映射到两个以上的表。</span><span class="sxs-lookup"><span data-stu-id="50609-105">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="50609-106">下图显示了在使用 EF 设计器时使用的主窗口。</span><span class="sxs-lookup"><span data-stu-id="50609-106">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF 设计器](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="50609-108">先决条件</span><span class="sxs-lookup"><span data-stu-id="50609-108">Prerequisites</span></span>

<span data-ttu-id="50609-109">Visual Studio 2012 或 Visual Studio 2010、旗舰版、高级版、专业版或 Web Express edition。</span><span class="sxs-lookup"><span data-stu-id="50609-109">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="50609-110">创建数据库</span><span class="sxs-lookup"><span data-stu-id="50609-110">Create the Database</span></span>

<span data-ttu-id="50609-111">随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：</span><span class="sxs-lookup"><span data-stu-id="50609-111">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="50609-112">如果使用的是 Visual Studio 2012，则将创建一个 LocalDB 数据库。</span><span class="sxs-lookup"><span data-stu-id="50609-112">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="50609-113">如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。</span><span class="sxs-lookup"><span data-stu-id="50609-113">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="50609-114">首先，我们将创建一个包含两个表的数据库，我们会将这些表合并到一个实体中。</span><span class="sxs-lookup"><span data-stu-id="50609-114">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="50609-115">打开 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50609-115">Open Visual Studio</span></span>
-   <span data-ttu-id="50609-116">**视图-&gt; 服务器资源管理器**</span><span class="sxs-lookup"><span data-stu-id="50609-116">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="50609-117">右键单击 "**数据连接-&gt; 添加连接 ...** "</span><span class="sxs-lookup"><span data-stu-id="50609-117">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="50609-118">如果尚未从服务器资源管理器连接到数据库，则需要选择**Microsoft SQL Server**作为数据源</span><span class="sxs-lookup"><span data-stu-id="50609-118">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="50609-119">连接到 LocalDB 或 SQL Express，具体取决于你安装的是哪个</span><span class="sxs-lookup"><span data-stu-id="50609-119">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="50609-120">输入**EntitySplitting**作为数据库名称</span><span class="sxs-lookup"><span data-stu-id="50609-120">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="50609-121">选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**</span><span class="sxs-lookup"><span data-stu-id="50609-121">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="50609-122">新数据库现在将出现在服务器资源管理器</span><span class="sxs-lookup"><span data-stu-id="50609-122">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="50609-123">如果使用的是 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="50609-123">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="50609-124">在服务器资源管理器中右键单击该数据库，然后选择 "**新建查询**"</span><span class="sxs-lookup"><span data-stu-id="50609-124">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="50609-125">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"</span><span class="sxs-lookup"><span data-stu-id="50609-125">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="50609-126">如果使用的是 Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="50609-126">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="50609-127">选择**数据&gt; Transact-sql 编辑器-&gt; 新建查询连接 ...**</span><span class="sxs-lookup"><span data-stu-id="50609-127">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="50609-128">输入 **。\\SQLEXPRESS**作为服务器名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="50609-128">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="50609-129">从 "查询编辑器" 顶部的下拉菜单中选择 " **EntitySplitting** " 数据库</span><span class="sxs-lookup"><span data-stu-id="50609-129">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="50609-130">将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行 SQL** "。</span><span class="sxs-lookup"><span data-stu-id="50609-130">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a><span data-ttu-id="50609-131">创建项目</span><span class="sxs-lookup"><span data-stu-id="50609-131">Create the Project</span></span>

-   <span data-ttu-id="50609-132">在 **“文件”** 菜单上，指向 **“新建”** ，再单击 **“项目”** 。</span><span class="sxs-lookup"><span data-stu-id="50609-132">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="50609-133">在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台应用程序**" 模板。</span><span class="sxs-lookup"><span data-stu-id="50609-133">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="50609-134">输入**MapEntityToTablesSample**作为项目名称，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="50609-134">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="50609-135">如果系统提示保存在第一部分中创建的 SQL 查询，请单击 "**否**"。</span><span class="sxs-lookup"><span data-stu-id="50609-135">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="50609-136">创建基于数据库的模型</span><span class="sxs-lookup"><span data-stu-id="50609-136">Create a Model based on the Database</span></span>

-   <span data-ttu-id="50609-137">右键单击 "解决方案资源管理器中的项目名称，指向"**添加**"，然后单击"**新建项**"。</span><span class="sxs-lookup"><span data-stu-id="50609-137">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="50609-138">从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。</span><span class="sxs-lookup"><span data-stu-id="50609-138">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="50609-139">输入**MapEntityToTablesModel**作为文件名，然后单击 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="50609-139">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="50609-140">在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步"。**</span><span class="sxs-lookup"><span data-stu-id="50609-140">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="50609-141">从下拉菜单中选择 " **EntitySplitting** " 连接，然后单击 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="50609-141">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="50609-142">在 "选择数据库对象" 对话框中，选中 "**表**" 旁边的框 "节点。</span><span class="sxs-lookup"><span data-stu-id="50609-142">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="50609-143">这会将**EntitySplitting**数据库中的所有表添加到模型。</span><span class="sxs-lookup"><span data-stu-id="50609-143">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="50609-144">单击 " **完成**"。</span><span class="sxs-lookup"><span data-stu-id="50609-144">Click **Finish**.</span></span>

<span data-ttu-id="50609-145">此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。</span><span class="sxs-lookup"><span data-stu-id="50609-145">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="50609-146">将一个实体映射到两个表</span><span class="sxs-lookup"><span data-stu-id="50609-146">Map an Entity to Two Tables</span></span>

<span data-ttu-id="50609-147">在此步骤中，我们将更新**person**实体类型以合并**person**和**PersonInfo**表中的数据。</span><span class="sxs-lookup"><span data-stu-id="50609-147">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="50609-148">选择 **PersonInfo **实体的 **电子邮件** 和**电话**属性，并按**Ctrl + X**键。</span><span class="sxs-lookup"><span data-stu-id="50609-148">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="50609-149">选择 " **人员 **" 实体，并按**Ctrl + V**键。</span><span class="sxs-lookup"><span data-stu-id="50609-149">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="50609-150">在设计图面上，选择 **PersonInfo** 实体，并按键盘上的 "**删除**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="50609-150">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="50609-151">当系统询问你是否要从模型中删除**PersonInfo**表时，请单击 "**否**"，我们将把它映射到**Person**实体。</span><span class="sxs-lookup"><span data-stu-id="50609-151">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![删除表](~/ef6/media/deletetables.png)

<span data-ttu-id="50609-153">接下来的步骤需要 "窗口中的" **映射详细信息**"。</span><span class="sxs-lookup"><span data-stu-id="50609-153">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="50609-154">如果看不到此窗口，请右键单击设计图面，然后选择 "**映射详细信息**"。</span><span class="sxs-lookup"><span data-stu-id="50609-154">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="50609-155">选择 " **人员**" 实体类型，然后单击 " **映射详细信息** 窗口中的 **&lt;添加表或视图&gt;**  。</span><span class="sxs-lookup"><span data-stu-id="50609-155">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="50609-156">从下拉列表中选择 " **PersonInfo ** "。</span><span class="sxs-lookup"><span data-stu-id="50609-156">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="50609-157"> " **映射详细信息**" 窗口将更新为默认的列映射，这些映射适用于我们的方案。</span><span class="sxs-lookup"><span data-stu-id="50609-157">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="50609-158"> 实体类型的 **人员**现在映射 **到 和** **PersonInfo** 表。</span><span class="sxs-lookup"><span data-stu-id="50609-158">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![映射2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="50609-160">使用模型</span><span class="sxs-lookup"><span data-stu-id="50609-160">Use the Model</span></span>

-   <span data-ttu-id="50609-161">将以下代码粘贴到 Main 方法中。</span><span class="sxs-lookup"><span data-stu-id="50609-161">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   <span data-ttu-id="50609-162">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="50609-162">Compile and run the application.</span></span>

<span data-ttu-id="50609-163">由于运行此应用程序，对数据库执行了以下 T-sql 语句。</span><span class="sxs-lookup"><span data-stu-id="50609-163">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="50609-164">以下两个**INSERT**语句是执行上下文的结果。SaveChanges （）。</span><span class="sxs-lookup"><span data-stu-id="50609-164">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="50609-165">它们采用**person**实体的数据，并将其拆分为**person**和**PersonInfo**表。</span><span class="sxs-lookup"><span data-stu-id="50609-165">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![插入1](~/ef6/media/insert1.png)

    ![插入2](~/ef6/media/insert2.png)
-   <span data-ttu-id="50609-168">由于枚举数据库中的人员，因此执行了下面的**SELECT** 。</span><span class="sxs-lookup"><span data-stu-id="50609-168">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="50609-169">它将**Person**和**PersonInfo**表中的数据合并在一起。</span><span class="sxs-lookup"><span data-stu-id="50609-169">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![选择](~/ef6/media/select.png)
