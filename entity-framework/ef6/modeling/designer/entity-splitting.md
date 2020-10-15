---
title: 设计器实体拆分-EF6
description: 实体框架6中的设计器实体拆分
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: 8fdb9037fc49045089016e6630316d05c8726ad8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064895"
---
# <a name="designer-entity-splitting"></a>设计器实体拆分
本演练演示如何通过使用 Entity Framework Designer (EF 设计器) 修改模型，将实体类型映射到两个表。 如果多个表共享同一个键，则可以将一个实体映射到这些表。 适用于将一个实体类型映射到两个表的概念，可以轻松扩展为将一个实体类型映射到两个以上的表。

下图显示了在使用 EF 设计器时使用的主窗口。

![EF 设计器](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>先决条件

Visual Studio 2012 或 Visual Studio 2010、旗舰版、高级版、专业版或 Web Express edition。

## <a name="create-the-database"></a>创建数据库

随 Visual Studio 一起安装的数据库服务器因安装的 Visual Studio 版本而异：

-   如果使用的是 Visual Studio 2012，则将创建一个 LocalDB 数据库。
-   如果使用的是 Visual Studio 2010，则将创建 SQL Express 数据库。

首先，我们将创建一个包含两个表的数据库，我们会将这些表合并到一个实体中。

-   打开 Visual Studio
-   **视图- &gt; 服务器资源管理器**
-   右键单击 "**数据连接- &gt; 添加连接 ...** "
-   如果尚未从服务器资源管理器连接到数据库，则需要选择 **Microsoft SQL Server** 作为数据源
-   连接到 LocalDB 或 SQL Express，具体取决于你安装的是哪个
-   输入 **EntitySplitting** 作为数据库名称
-   选择 **"确定"** ，系统会询问您是否要创建新数据库，请选择 **"是"**
-   新数据库现在将出现在服务器资源管理器
-   如果使用的是 Visual Studio 2012
    -   右键单击“服务器资源管理器”中的数据库，然后选择“新建查询”****
    -   将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行**"
-   如果使用的是 Visual Studio 2010
    -   选择 **数据 &gt; Transact-sql SQL 编辑器- &gt; 新建查询连接 ...**
    -   输入 **。 \\SQLEXPRESS** 作为服务器名称，然后单击 **"确定"**
    -   从 "查询编辑器" 顶部的下拉菜单中选择 " **EntitySplitting** " 数据库
    -   将以下 SQL 复制到新的查询中，然后右键单击该查询，然后选择 "**执行 SQL** "。

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

## <a name="create-the-project"></a>创建项目

-   在 **“文件”** 菜单上，指向 **“新建”** ，再单击 **“项目”** 。
-   在左窗格中，单击 " **Visual \# C**"，然后选择 "**控制台应用程序**" 模板。
-   输入 **MapEntityToTablesSample** 作为项目名称，然后单击 **"确定"**。
-   如果系统提示保存在第一部分中创建的 SQL 查询，请单击 " **否** "。

## <a name="create-a-model-based-on-the-database"></a>创建基于数据库的模型

-   右键单击 "解决方案资源管理器中的项目名称，指向" **添加**"，然后单击" **新建项**"。
-   从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。
-   输入 **MapEntityToTablesModel** 作为文件名，然后单击 " **添加**"。
-   在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步"。**
-   从下拉菜单中选择 " **EntitySplitting** " 连接，然后单击 " **下一步**"。
-   在 "选择数据库对象" 对话框中，选中 "**表**" 节点旁边的复选框   。
    这会将 **EntitySplitting** 数据库中的所有表添加到模型。
-   单击 " **完成**"。

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。

## <a name="map-an-entity-to-two-tables"></a>将一个实体映射到两个表

在此步骤中，我们将更新 **person** 实体类型以合并 **person** 和 **PersonInfo** 表中的数据。

-   选择 PersonInfo 实体的 **电子邮件**   和**电话**属性，并按**Ctrl + X**键。 **PersonInfo **
-   选择 " **人员 **" 实体，并按 **Ctrl + V** 键。
-   在设计图面上，选择 " **PersonInfo**"   实体，并按键盘上的 "**删除**" 按钮。
-   当系统询问你是否要从模型中删除**PersonInfo**表时，请单击 "**否**"，我们将把它映射到**Person**实体。

    ![删除表](~/ef6/media/deletetables.png)

接下来的步骤需要 " **映射详细信息**"   窗口。 如果看不到此窗口，请右键单击设计图面，然后选择 " **映射详细信息**"。

-   选择 " **人员**"   实体类型，然后在 "映射详细信息" 窗口中单击 " ** &lt; 添加表或视图 &gt; **   " **Mapping Details**   。
-    **PersonInfo **   从下拉列表中选择 "PersonInfo"。
    " **映射详细信息**"   窗口将用默认的列映射进行更新，它们适用于我们的方案。

 **Person**   实体类型现在映射到 **person**   和 **PersonInfo**   表。

![映射2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>使用模型

-   将以下代码粘贴到 Main 方法中。

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

-   编译并运行该应用程序。

由于运行此应用程序，对数据库执行了以下 T-sql 语句。 

-   以下两个 **INSERT** 语句是执行上下文的结果。SaveChanges ( # A1。 它们采用 **person** 实体的数据，并将其拆分为 **person** 和 **PersonInfo** 表。

    ![插入1](~/ef6/media/insert1.png)

    ![插入2](~/ef6/media/insert2.png)
-   由于枚举数据库中的人员，因此执行了下面的 **SELECT** 。 它将 **Person** 和 **PersonInfo** 表中的数据合并在一起。

    ![选择合并人员和 PersonInfo 数据](~/ef6/media/select.png)
