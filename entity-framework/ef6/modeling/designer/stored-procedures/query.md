---
title: 设计器查询存储过程-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 2e0092b526278597e8477d47eeb642598647bb91
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415201"
---
# <a name="designer-query-stored-procedures"></a>设计器查询存储过程
此分步演练演示如何使用 Entity Framework Designer （EF 设计器）将存储过程导入到模型中，然后调用导入的存储过程来检索结果。 

请注意，Code First 不支持映射到存储过程或函数。 但是，可以使用 DbSet. SqlQuery 方法调用存储过程或函数。 例如：
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>先决条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](~/ef6/resources/school-database.md)。

## <a name="set-up-the-project"></a>设置项目

-   打开 Visual Studio 2012。
-   选择 "**文件-&gt;"&gt; 项目**
-   在左窗格中，单击 " **Visual C\#** "，然后选择 "**控制台**" 模板。
-   输入 " **EFwithSProcsSample** " 作为名称。
-   选择“确定”。 ****

## <a name="create-a-model"></a>创建模型

-   在解决方案资源管理器中右键单击项目，然后选择 "**添加-&gt; 新项**"。
-   从左侧菜单中选择 "**数据**"，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型**。
-   输入**EFwithSProcsModel**作为文件名，然后单击 "**添加**"。
-   在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。
-   单击 " **新建连接**"。  
    在 "连接属性" 对话框中，输入服务器名称（例如， **（localdb）\\mssqllocaldb**），选择身份验证方法，为数据库名称键入 **School** ，然后单击 **"确定"** 。  
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
-   在 "选择数据库对象" 对话框中，选中 "**表** " 复选框以选择所有表。  
    另外，在 "**存储过程和函数**" 节点下选择以下存储过程： **GetStudentGrades**和**GetDepartmentName**。 

    ![导入](~/ef6/media/import.jpg)

    *从 Visual Studio 2012 开始，EF 设计器支持存储过程的大容量导入。默认情况下，将选中 "将**选定的存储过程和函数导入 entity 模型**"。*
-   单击 " **完成**"。

默认情况下，返回多个列的每个导入的存储过程或函数的结果形状将自动成为新的复杂类型。 在此示例中，我们想要将 **GetStudentGrades**函数的结果映射到 **StudentGrade**实体，并将**GetDepartmentName**结果映射到 "**无**" （默认值为 "**无**"）。

为了使函数导入返回实体类型，由相应的存储过程返回的列必须与返回的实体类型的标量属性完全匹配。 函数导入还可以返回简单类型、复杂类型或无值的集合。

-   右键单击设计图面，然后选择 " **模型浏览器**"。
-   在**模型浏览器**中，选择 " **函数导入**"，然后双击 " **GetStudentGrades** " 函数。
-   在 "编辑函数导入" 对话框中，选择 " **实体**" 然后选择 " **StudentGrade**"。  
    *函数导入**对话框顶部**的 "函数**导入是可组合**的" 复选框允许您映射到可组合函数。如果选中此复选框，则 "**存储过程/函数名称**" 下拉列表中将只显示可组合函数（表值函数）。如果不选中此框，则列表中将只显示不可组合的函数。*

## <a name="use-the-model"></a>使用模型

打开**Program.cs**文件，其中定义了**Main**方法。 将以下代码添加到 Main 函数中。

此代码将调用两个存储过程： **GetStudentGrades** （为指定的*StudentId*返回**StudentGrades** ）和**GetDepartmentName** （返回 output 参数中部门的名称）。  

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

编译并运行该应用程序。 该程序生成以下输出：

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>输出参数
-----------------

如果使用了 output 参数，则在完全读取结果之前，它们的值将不可用。 这是因为 DbDataReader 的基础行为，有关详细信息，请参阅[使用 DataReader 检索数据](https://go.microsoft.com/fwlink/?LinkID=398589)。
