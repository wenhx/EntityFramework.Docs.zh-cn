---
title: 设计器 TPT 继承-EF6
description: 实体框架6中的设计器 TPT 继承
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: e60965550db3f140dfebf4c1952b3901b9b00aab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073365"
---
# <a name="designer-tpt-inheritance"></a>设计器 TPT 继承
此分步演练演示如何使用 Entity Framework Designer (EF 设计器) ，在模型中 (TPT) 继承来实现每种类型一个表继承。 每种类型一个表继承使用数据库中单独的表为继承层次结构中的每种类型维护非继承属性和键属性的数据。

在本演练中，我们将 **)  (基**类型的、 (派生的 " **OnlineCourse** " 派生的课程) ，并将 " **为 onsitecourse** "   (从**课程**) 实体派生到具有相同名称的表。 我们将从数据库创建一个模型，然后更改模型以实现 TPT 继承。

还可以从 Model First 开始，然后从模型生成数据库。 默认情况下，EF 设计器会使用 TPT 策略，因此该模型中的任何继承都将映射到单独的表。

## <a name="other-inheritance-options"></a>其他继承选项

每个层次结构一个表 (TPH) 是另一种类型的继承，其中，一个数据库表用于维护继承层次结构中所有实体类型的数据。有关如何用 Entity Designer 映射每个层次结构一个表继承的信息，请参阅 [EF 设计器 TPH 继承](xref:ef6/modeling/designer/inheritance/tph)。 

请注意，实体框架运行时 (TPC) 和混合继承模型支持每个具体的类型的表继承，但 EF 设计器不支持。 如果要使用 TPC 或混合继承，则有两个选项：使用 Code First 或手动编辑 EDMX 文件。 如果选择处理 EDMX 文件，"映射详细信息" 窗口将进入 "安全模式"，您将无法使用设计器来更改映射。

## <a name="prerequisites"></a>必备条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>设置项目

-   打开 Visual Studio 2012。
-   选择 **文件- &gt; 新建- &gt; 项目**
-   在左窗格中，单击 " **Visual \# C**"，然后选择 "**控制台**" 模板。
-   输入 **TPTDBFirstSample**   作为名称。
-   选择“确定”。 ****

## <a name="create-a-model"></a>创建模型

-   右键单击 "解决方案资源管理器中的项目，然后选择" **添加 &gt; 新项**"。
-   从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。
-   输入 **TPTModel** 作为文件名，然后单击 " **添加**"。
-   在 "选择模型内容" 对话框中，选择 "**   从数据库生成**"，然后单击 " **下一步**"。
-   单击 " **新建连接**"。
    在 "连接属性" 对话框中，输入服务器名称 (例如， ** (localdb) \\ mssqllocaldb**) ，选择身份验证方法，为数据库名称键入 **School**，然后   单击 **"确定"**。
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
-   在 "选择数据库对象" 对话框的 "表" 节点下，选择 " **部门**"、" **课程"、"OnlineCourse" 和 "为 onsitecourse** " 表。
-   单击 " **完成**"。

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。 您在 "选择数据库对象" 对话框中选择的所有对象都将添加到模型中。

## <a name="implement-table-per-type-inheritance"></a>实现每种类型一个表继承

-   在设计图面上，右键单击 **OnlineCourse** 实体类型，然后选择 " **属性**"。
-   在 " **属性** " 窗口中，将 "基类型" 属性设置为 " **课程**"。
-   右键单击 **为 onsitecourse** 实体类型，然后选择 " **属性**"。
-   在 " **属性** " 窗口中，将 "基类型" 属性设置为 " **课程**"。
-   右键单击 **OnlineCourse** 和 **课程** 实体类型之间) 行 (关联。
    选择 " **从模型删除**"。
-   右键单击 **为 onsitecourse** 和 **课程** 实体类型之间的关联。
    选择 " **从模型删除**"。

现在，我们将从**OnlineCourse**和**为 onsitecourse**中删除**CourseID**属性，因为这些类从**课程**基类型继承**CourseID** 。

-   右键单击**OnlineCourse**实体类型的**CourseID**属性，然后选择 "**从模型中删除**"。
-   右键单击**为 onsitecourse**实体类型的**CourseID**属性，然后选择 "**从模型删除**"
-   至此已实现每种类型一个表继承。

![表/类型](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>使用模型

打开 **Program.cs** 文件，其中定义了 **Main** 方法。 将以下代码粘贴到 **Main** 函数中。 此代码执行三个查询。 第一个查询返回与指定部门相关的所有 **课程** 。 第二个查询使用 **OfType** 方法返回与指定部门相关的 **OnlineCourses** 。 第三个查询返回 **OnsiteCourses**。

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
