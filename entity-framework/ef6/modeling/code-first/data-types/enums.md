---
title: 枚举支持-Code First-EF6
description: 枚举支持-实体框架6中的 Code First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: d12a7427f217eec21b1428dda78d0b7a9e88d07d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065311"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="37e46-103">枚举支持-Code First</span><span class="sxs-lookup"><span data-stu-id="37e46-103">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="37e46-104">**EF5 仅向前** -实体框架5中引入了本页中所述的功能、api 等。</span><span class="sxs-lookup"><span data-stu-id="37e46-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="37e46-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="37e46-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="37e46-106">此视频和分步演练演示了如何使用实体框架 Code First 的枚举类型。</span><span class="sxs-lookup"><span data-stu-id="37e46-106">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="37e46-107">它还演示了如何在 LINQ 查询中使用枚举。</span><span class="sxs-lookup"><span data-stu-id="37e46-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="37e46-108">本演练将使用 Code First 创建新数据库，但也可以使用 [Code First 映射到现有数据库](xref:ef6/modeling/code-first/workflows/existing-database)。</span><span class="sxs-lookup"><span data-stu-id="37e46-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="37e46-109">实体框架5中引入了枚举支持。</span><span class="sxs-lookup"><span data-stu-id="37e46-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="37e46-110">若要使用枚举、空间数据类型和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。</span><span class="sxs-lookup"><span data-stu-id="37e46-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="37e46-111">默认情况下，Visual Studio 2012 面向 .NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="37e46-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="37e46-112">在实体框架中，枚举可以具有以下基础类型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。</span><span class="sxs-lookup"><span data-stu-id="37e46-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="37e46-113">观看视频</span><span class="sxs-lookup"><span data-stu-id="37e46-113">Watch the video</span></span>
<span data-ttu-id="37e46-114">此视频演示如何使用实体框架 Code First 的枚举类型。</span><span class="sxs-lookup"><span data-stu-id="37e46-114">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="37e46-115">它还演示了如何在 LINQ 查询中使用枚举。</span><span class="sxs-lookup"><span data-stu-id="37e46-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="37e46-116">**提供者**： Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="37e46-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="37e46-117">**视频**： [wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [.wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="37e46-117">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="37e46-118">先决条件</span><span class="sxs-lookup"><span data-stu-id="37e46-118">Pre-Requisites</span></span>

<span data-ttu-id="37e46-119">你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。</span><span class="sxs-lookup"><span data-stu-id="37e46-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="37e46-120">设置项目</span><span class="sxs-lookup"><span data-stu-id="37e46-120">Set up the Project</span></span>

1.  <span data-ttu-id="37e46-121">打开 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="37e46-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="37e46-122">在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"</span><span class="sxs-lookup"><span data-stu-id="37e46-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="37e46-123">在左窗格中，单击 " **Visual \# C**"，然后选择**控制台**模板</span><span class="sxs-lookup"><span data-stu-id="37e46-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="37e46-124">输入 **EnumCodeFirst** 作为项目名称，然后单击 **"确定"**</span><span class="sxs-lookup"><span data-stu-id="37e46-124">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="37e46-125">使用 Code First 定义新模型</span><span class="sxs-lookup"><span data-stu-id="37e46-125">Define a New Model using Code First</span></span>

<span data-ttu-id="37e46-126">使用 Code First 开发时，通常首先编写 .NET Framework 类来定义概念 (域) 模型。</span><span class="sxs-lookup"><span data-stu-id="37e46-126">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="37e46-127">下面的代码定义了部门类。</span><span class="sxs-lookup"><span data-stu-id="37e46-127">The code below defines the Department class.</span></span>

<span data-ttu-id="37e46-128">该代码还定义了 DepartmentNames 枚举。</span><span class="sxs-lookup"><span data-stu-id="37e46-128">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="37e46-129">默认情况下，枚举的类型为 **int** 。</span><span class="sxs-lookup"><span data-stu-id="37e46-129">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="37e46-130">部门类的 Name 属性为 DepartmentNames 类型。</span><span class="sxs-lookup"><span data-stu-id="37e46-130">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="37e46-131">打开 Program.cs 文件并粘贴以下类定义。</span><span class="sxs-lookup"><span data-stu-id="37e46-131">Open the Program.cs file and paste the following class definitions.</span></span>

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="37e46-132">定义 DbContext 派生类型</span><span class="sxs-lookup"><span data-stu-id="37e46-132">Define the DbContext Derived Type</span></span>

<span data-ttu-id="37e46-133">除了定义实体外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt; 属性的类。</span><span class="sxs-lookup"><span data-stu-id="37e46-133">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="37e46-134">DbSet&lt;TEntity&gt; 属性让上下文知道要包括在模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="37e46-134">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="37e46-135">DbContext 派生类型的实例在运行时管理实体对象，其中包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="37e46-135">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="37e46-136">DbContext 和 DbSet 类型是在 EntityFramework 程序集中定义的。</span><span class="sxs-lookup"><span data-stu-id="37e46-136">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="37e46-137">我们将使用 EntityFramework NuGet 包添加对此 DLL 的引用。</span><span class="sxs-lookup"><span data-stu-id="37e46-137">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="37e46-138">在解决方案资源管理器中，右键单击项目名称。</span><span class="sxs-lookup"><span data-stu-id="37e46-138">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="37e46-139">选择 "**管理 NuGet 包 ...** "</span><span class="sxs-lookup"><span data-stu-id="37e46-139">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="37e46-140">在 "管理 NuGet 包" 对话框中，选择 " **联机** " 选项卡，然后选择 " **EntityFramework** " 包。</span><span class="sxs-lookup"><span data-stu-id="37e46-140">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="37e46-141">单击“安装” </span><span class="sxs-lookup"><span data-stu-id="37e46-141">Click **Install**</span></span>

<span data-ttu-id="37e46-142">请注意，除 EntityFramework 程序集之外，还添加了对 System.componentmodel 和 DataAnnotations 程序集的引用。</span><span class="sxs-lookup"><span data-stu-id="37e46-142">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="37e46-143">在 Program.cs 文件的顶部，添加以下 using 语句：</span><span class="sxs-lookup"><span data-stu-id="37e46-143">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="37e46-144">在 Program.cs 中，添加上下文定义。</span><span class="sxs-lookup"><span data-stu-id="37e46-144">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="37e46-145">保留和检索数据</span><span class="sxs-lookup"><span data-stu-id="37e46-145">Persist and Retrieve Data</span></span>

<span data-ttu-id="37e46-146">打开 Program.cs 文件，其中定义了 Main 方法。</span><span class="sxs-lookup"><span data-stu-id="37e46-146">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="37e46-147">将以下代码添加到 Main 函数中。</span><span class="sxs-lookup"><span data-stu-id="37e46-147">Add the following code into the Main function.</span></span> <span data-ttu-id="37e46-148">该代码将新的部门对象添加到上下文中。</span><span class="sxs-lookup"><span data-stu-id="37e46-148">The code adds a new Department object to the context.</span></span> <span data-ttu-id="37e46-149">然后，它会保存数据。</span><span class="sxs-lookup"><span data-stu-id="37e46-149">It then saves the data.</span></span> <span data-ttu-id="37e46-150">此代码还执行 LINQ 查询，该查询返回名称为 DepartmentNames 的部门。</span><span class="sxs-lookup"><span data-stu-id="37e46-150">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="37e46-151">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="37e46-151">Compile and run the application.</span></span> <span data-ttu-id="37e46-152">该程序生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="37e46-152">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="37e46-153">查看生成的数据库</span><span class="sxs-lookup"><span data-stu-id="37e46-153">View the Generated Database</span></span>

<span data-ttu-id="37e46-154">首次运行应用程序时，实体框架会为您创建一个数据库。</span><span class="sxs-lookup"><span data-stu-id="37e46-154">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="37e46-155">由于我们安装了 Visual Studio 2012，因此将在 LocalDB 实例上创建数据库。</span><span class="sxs-lookup"><span data-stu-id="37e46-155">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="37e46-156">默认情况下，实体框架在派生上下文的完全限定名称后命名该数据库 (此示例的 **EnumCodeFirst. EnumTestContext**) 。</span><span class="sxs-lookup"><span data-stu-id="37e46-156">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="37e46-157">随后将使用现有数据库的时间。</span><span class="sxs-lookup"><span data-stu-id="37e46-157">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="37e46-158">请注意，如果在创建数据库后对模型进行了任何更改，则应使用 Code First 迁移来更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="37e46-158">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="37e46-159">有关使用迁移的示例，请参阅 [Code First 到新的数据库](xref:ef6/modeling/code-first/workflows/new-database) 。</span><span class="sxs-lookup"><span data-stu-id="37e46-159">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="37e46-160">若要查看数据库和数据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="37e46-160">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="37e46-161">在 Visual Studio 2012 主菜单中，选择 "**查看**  - &gt; **SQL Server 对象资源管理器**"。</span><span class="sxs-lookup"><span data-stu-id="37e46-161">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="37e46-162">如果 LocalDB 不在服务器列表中，请在 **SQL Server** 上单击鼠标右键按钮，然后选择 " **添加 SQL Server** 使用默认 **Windows 身份验证** 连接到 LocalDB 实例</span><span class="sxs-lookup"><span data-stu-id="37e46-162">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="37e46-163">展开 LocalDB 节点</span><span class="sxs-lookup"><span data-stu-id="37e46-163">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="37e46-164">展开 " **数据库** " 文件夹以查看新数据库并浏览到 " **部门** " 表，该 Code First 不会创建映射到枚举类型的表</span><span class="sxs-lookup"><span data-stu-id="37e46-164">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="37e46-165">若要查看数据，请右键单击该表，然后选择 "**查看数据**"</span><span class="sxs-lookup"><span data-stu-id="37e46-165">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="37e46-166">摘要</span><span class="sxs-lookup"><span data-stu-id="37e46-166">Summary</span></span>

<span data-ttu-id="37e46-167">在本演练中，我们介绍了如何使用实体框架 Code First 的枚举类型。</span><span class="sxs-lookup"><span data-stu-id="37e46-167">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
