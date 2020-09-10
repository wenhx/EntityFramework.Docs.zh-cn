---
title: 设计器 CUD 存储过程-EF6
description: 实体框架6中的设计器 CUD 存储过程
author: divega
ms.date: 10/23/2016
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: f722cb3ac0b6ce21e685dbb7bffe571fa7b783d5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620369"
---
# <a name="designer-cud-stored-procedures"></a>设计器 CUD 存储过程

此分步演练演示如何 \\ 使用 Entity Framework Designer (EF 设计器) ，将实体类型的 create insert、update 和 delete (CUD) 操作映射到存储过程。 默认情况下，实体框架会自动为 CUD 操作生成 SQL 语句，但也可以将存储过程映射到这些操作。  

请注意，Code First 不支持映射到存储过程或函数。 但是，可以使用 DbSet. SqlQuery 方法调用存储过程或函数。 例如：

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>将 CUD 操作映射到存储过程时的注意事项

将 CUD 操作映射到存储过程时，请注意以下事项：

- 如果要将其中一个 CUD 操作映射到存储过程，请映射所有这些操作。 如果不映射这三个，则未映射的操作将在执行时失败，并将引发 **UpdateException**   。
- 必须将存储过程的每个参数映射到实体属性。
- 如果服务器为插入的行生成主键值，则必须将此值映射回实体的键属性。 在下面的示例中， **InsertPerson**   存储过程返回新创建的主键作为存储过程的结果集的一部分。 使用 EF 设计器的 " ** &lt; 添加结果绑定 &gt; **" 功能将主键映射到实体键 (**PersonID**)   。
- 存储过程调用映射到概念模型中的实体1:1。 例如，如果在概念模型中实现了继承层次结构，然后为 **父** (基础) 和子 (派生) 实体的 **子** 存储过程，则保存 **子** 更改将仅调用 **子**的存储过程，而不会触发 **父级**的存储过程调用。

## <a name="prerequisites"></a>先决条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>设置项目

- 打开 Visual Studio 2012。
- 选择 **文件- &gt; 新建- &gt; 项目**
- 在左窗格中，单击 " **Visual \# C**"，然后选择 "**控制台**" 模板。
- 输入 **CUDSProcsSample**   作为名称。
- 选择“确定”。 ****

## <a name="create-a-model"></a>创建模型

- 在解决方案资源管理器中右键单击项目名称，然后选择 " **添加- &gt; 新建项**"。
- 从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。
- 输入 **CUDSProcs** 作为文件名，然后单击 " **添加**"。
- 在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。
- 单击 " **新建连接**"。 在 "连接属性" 对话框中，输入服务器名称 (例如， ** (localdb) \\ mssqllocaldb**) ，选择身份验证方法，为数据库名称键入 **School**，然后   单击 **"确定"**。
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
- 在 "选择数据库对象" 对话框中的 " **表**" 节点下，   选择 **Person**表。
- 另外，在 " **存储过程和函数** " 节点下选择以下存储过程： **DeletePerson**、 **InsertPerson**和 **UpdatePerson**。
- 从 Visual Studio 2012 开始，EF 设计器支持存储过程的大容量导入。 默认情况下，将选中 "将 **选定的存储过程和函数导入实体模型** "。 由于在此示例中，我们已存储了用于插入、更新和删除实体类型的存储过程，因此我们不想将它们导入，并将取消选中此复选框。

    ![导入过程](~/ef6/media/importsprocs.jpg)

- 单击 " **完成**"。
    显示了 EF 设计器（提供编辑模型的设计图面）。

## <a name="map-the-person-entity-to-stored-procedures"></a>将 Person 实体映射到存储过程

- 右键单击 **Person**   实体类型，然后选择 " **存储过程映射**"。
- 存储过程映射显示在 " **映射详细信息**"   窗口中。
- 单击 " ** &lt; 选择插入 &gt; 函数**"。
    该字段变成一个下拉列表，该列表中包含存储模型中可以映射到概念模型中的实体类型的存储过程。
     **InsertPerson**   从下拉列表中选择 "InsertPerson"。
- 此时将显示存储过程参数和实体属性之间的默认映射。 请注意，箭头指示映射方向：向存储过程参数提供属性值。
- 单击 " ** &lt; 添加结果 &gt; 绑定**"。
- 键入 **NewPersonID**，即 **InsertPerson**存储过程返回的参数的名称   。 请确保不要键入前导空格或尾随空格。
- 按 **enter**。
- 默认情况下， **NewPersonID**   映射到实体键 **PersonID**。 请注意，箭头指示映射的方向：向属性提供结果列的值。

    ![映射详细信息](~/ef6/media/mappingdetails.png)

- 单击 " ** &lt; 选择更新 &gt; 函数**   " **UpdatePerson**，然后   从生成的下拉列表中选择 "UpdatePerson"。
- 此时将显示存储过程参数和实体属性之间的默认映射。
- 单击 " ** &lt; 选择删除 &gt; 函数**   " **DeletePerson**，然后   从生成的下拉列表中选择 "DeletePerson"。
- 此时将显示存储过程参数和实体属性之间的默认映射。

Person 实体类型的插入、更新和删除操作 **Person**   现在映射到存储过程。

如果要在使用存储过程更新或删除实体时启用并发检查，请使用下列选项之一：

- 使用**OUTPUT**参数可以从存储过程中返回受影响的行数，并选中参数名称旁边的 "**受影响的行" 参数**   复选框。 如果调用操作时返回的值为零，则将引发  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)   。
- 选中要用于并发检查的属性旁边的 " **使用原始值** " 复选框。 尝试更新时，在将数据写回数据库时，将使用最初从数据库读取的属性的值。 如果值与数据库中的值不匹配，则将引发**OptimisticConcurrencyException**   。

## <a name="use-the-model"></a>使用模型

打开 **Program.cs** 文件，其中定义了 **Main** 方法。 将以下代码添加到 Main 函数中。

此代码创建一个新的 **Person** 对象，然后更新该对象，最后删除该对象。

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

- 编译并运行该应用程序。 该程序生成以下输出 *

> [!NOTE]
> PersonID 是由服务器自动生成的，因此很可能会看到不同的数字 *

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

如果使用的是 Visual Studio 的旗舰版，则可以将 Intellitrace 与调试器结合使用，以查看执行的 SQL 语句。

![用 Intellitrace 调试](~/ef6/media/intellitrace.png)
