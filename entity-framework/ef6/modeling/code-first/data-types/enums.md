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
# <a name="enum-support---code-first"></a>枚举支持-Code First
> [!NOTE]
> **EF5 仅向前** -实体框架5中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。

此视频和分步演练演示了如何使用实体框架 Code First 的枚举类型。 它还演示了如何在 LINQ 查询中使用枚举。

本演练将使用 Code First 创建新数据库，但也可以使用 [Code First 映射到现有数据库](xref:ef6/modeling/code-first/workflows/existing-database)。

实体框架5中引入了枚举支持。 若要使用枚举、空间数据类型和表值函数等新功能，则必须以 .NET Framework 4.5 为目标。 默认情况下，Visual Studio 2012 面向 .NET 4.5。

在实体框架中，枚举可以具有以下基础类型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。

## <a name="watch-the-video"></a>观看视频
此视频演示如何使用实体框架 Code First 的枚举类型。 它还演示了如何在 LINQ 查询中使用枚举。

**提供者**： Julia Kornich

**视频**： [wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [.wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [wmv (ZIP) ](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>先决条件

你将需要安装 Visual Studio 2012、旗舰版、高级版、专业版或 Web Express edition 才能完成此演练。

 

## <a name="set-up-the-project"></a>设置项目

1.  打开 Visual Studio 2012
2.  在 "**文件**" 菜单上，指向 "**新建**"，然后单击 "**项目**"
3.  在左窗格中，单击 " **Visual \# C**"，然后选择**控制台**模板
4.  输入 **EnumCodeFirst** 作为项目名称，然后单击 **"确定"**

## <a name="define-a-new-model-using-code-first"></a>使用 Code First 定义新模型

使用 Code First 开发时，通常首先编写 .NET Framework 类来定义概念 (域) 模型。 下面的代码定义了部门类。

该代码还定义了 DepartmentNames 枚举。 默认情况下，枚举的类型为 **int** 。 部门类的 Name 属性为 DepartmentNames 类型。

打开 Program.cs 文件并粘贴以下类定义。

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
 

## <a name="define-the-dbcontext-derived-type"></a>定义 DbContext 派生类型

除了定义实体外，还需要定义派生自 DbContext 并公开 DbSet&lt;TEntity&gt; 属性的类。 DbSet&lt;TEntity&gt; 属性让上下文知道要包括在模型中的类型。

DbContext 派生类型的实例在运行时管理实体对象，其中包括使用数据库中的数据填充对象、更改跟踪以及将数据保存到数据库。

DbContext 和 DbSet 类型是在 EntityFramework 程序集中定义的。 我们将使用 EntityFramework NuGet 包添加对此 DLL 的引用。

1.  在解决方案资源管理器中，右键单击项目名称。
2.  选择 "**管理 NuGet 包 ...** "
3.  在 "管理 NuGet 包" 对话框中，选择 " **联机** " 选项卡，然后选择 " **EntityFramework** " 包。
4.  单击“安装” 

请注意，除 EntityFramework 程序集之外，还添加了对 System.componentmodel 和 DataAnnotations 程序集的引用。

在 Program.cs 文件的顶部，添加以下 using 语句：

``` csharp
using System.Data.Entity;
```

在 Program.cs 中，添加上下文定义。 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>保留和检索数据

打开 Program.cs 文件，其中定义了 Main 方法。 将以下代码添加到 Main 函数中。 该代码将新的部门对象添加到上下文中。 然后，它会保存数据。 此代码还执行 LINQ 查询，该查询返回名称为 DepartmentNames 的部门。

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

编译并运行该应用程序。 该程序生成以下输出：

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>查看生成的数据库

首次运行应用程序时，实体框架会为您创建一个数据库。 由于我们安装了 Visual Studio 2012，因此将在 LocalDB 实例上创建数据库。 默认情况下，实体框架在派生上下文的完全限定名称后命名该数据库 (此示例的 **EnumCodeFirst. EnumTestContext**) 。 随后将使用现有数据库的时间。  

请注意，如果在创建数据库后对模型进行了任何更改，则应使用 Code First 迁移来更新数据库架构。 有关使用迁移的示例，请参阅 [Code First 到新的数据库](xref:ef6/modeling/code-first/workflows/new-database) 。

若要查看数据库和数据，请执行以下操作：

1.  在 Visual Studio 2012 主菜单中，选择 "**查看**  - &gt; **SQL Server 对象资源管理器**"。
2.  如果 LocalDB 不在服务器列表中，请在 **SQL Server** 上单击鼠标右键按钮，然后选择 " **添加 SQL Server** 使用默认 **Windows 身份验证** 连接到 LocalDB 实例
3.  展开 LocalDB 节点
4.  展开 " **数据库** " 文件夹以查看新数据库并浏览到 " **部门** " 表，该 Code First 不会创建映射到枚举类型的表
5.  若要查看数据，请右键单击该表，然后选择 "**查看数据**"

## <a name="summary"></a>摘要

在本演练中，我们介绍了如何使用实体框架 Code First 的枚举类型。 
