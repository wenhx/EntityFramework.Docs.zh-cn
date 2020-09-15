---
title: 定义查询-EF 设计器-EF6
description: 在实体框架6中定义查询 EF 设计器
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: b5d24b9f6c047eb8681a0343c768d1ffa70ca6b8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070336"
---
# <a name="defining-query---ef-designer"></a>定义查询 EF 设计器
本演练演示如何使用 EF 设计器将定义查询和相应的实体类型添加到模型。 定义查询通常用于提供类似于数据库视图提供的功能，但该视图是在模型中定义的，而不是在数据库中定义的。 使用定义查询可执行在 .edmx 文件的 **DefiningQuery**元素中指定的 SQL 语句   。 有关详细信息，请参阅[SSDL 规范](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)中的 **DefiningQuery** 。

使用定义查询时，还必须在模型中定义一个实体类型。 实体类型用于呈现由定义查询公开的数据。 请注意，通过此实体类型显示的数据是只读的。

无法将参数化查询作为定义查询执行。 但是，可以通过将显示数据的实体类型的插入、更新和删除函数映射到存储过程来更新数据。 有关详细信息，请参阅 [对存储过程执行插入、更新和删除操作](xref:ef6/modeling/designer/stored-procedures/cud)。

本主题演示如何执行以下任务。

-   添加定义查询
-   向模型添加实体类型
-   将定义查询映射到实体类型

## <a name="prerequisites"></a>必备条件

若要完成此演练，您需要：

- Visual Studio 的最新版本。
- [School 示例数据库](xref:ef6/resources/school-database)。

## <a name="set-up-the-project"></a>设置项目

本演练使用 Visual Studio 2012 或更高版本。

-   打开 Visual Studio。
-   在 **“文件”** 菜单上，指向 **“新建”** ，再单击 **“项目”** 。
-   在左窗格中，单击 " **Visual \# C**"，然后选择 "**控制台应用程序**" 模板。
-   输入 **DefiningQuerySample** 作为项目名称，然后单击 **"确定"**。

 

## <a name="create-a-model-based-on-the-school-database"></a>基于 School 数据库创建模型

-   右键单击 "解决方案资源管理器中的项目名称，指向" **添加**"，然后单击" **新建项**"。
-   从左侧菜单中选择 " **数据** "，然后在 "模板" 窗格中选择 " **ADO.NET 实体数据模型** 。
-   输入 **DefiningQueryModel** 作为文件名，然后单击 " **添加**"。
-   在 "选择模型内容" 对话框中，选择 " **从数据库生成**"，然后单击 " **下一步**"。
-   单击 "新建连接"。 在 "连接属性" 对话框中，输入服务器名称 (例如， ** (localdb) \\ mssqllocaldb**) ，选择身份验证方法，为数据库名称键入 **School**，然后   单击 **"确定"**。
    "选择您的数据连接" 对话框将通过数据库连接设置进行更新。
-   在 "选择数据库对象" 对话框中，选中 "**表**"   节点。 这会将所有表添加到 **School** 模型。
-   单击 " **完成**"。
-   在解决方案资源管理器中，右键单击 **DefiningQueryModel** 文件并选择 " **打开方式 ...**"。
-   选择 " **XML (文本) 编辑器**"。

    ![XML 编辑器](~/ef6/media/xmleditor.png)

-   如果出现以下消息，请单击 **"是"** ：

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>添加定义查询

在此步骤中，我们将使用 "XML 编辑器" 向 .edmx 文件的 SSDL 部分添加定义查询和实体类型。 

-   将 **EntitySet**   元素添加到 .EDMX 文件的 SSDL 部分 (第5行到第 13) 。 指定下列各项：
    -   仅 **Name**   指定 EntitySet 元素的 Name 和 **EntityType**   特性 **EntitySet**   。
    -   实体类型的完全限定名称用于 **EntityType**   特性中。
    -   要执行的 SQL 语句是在 **DefiningQuery**元素中指定的   。

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   将 **EntityType** 元素添加到 .EDMX 的 SSDL 部分。 文件，如下所示。 注意以下事项：
    -   **Name**特性的值对应于上面的**EntitySet**元素中的**EntityType**特性的值，但该实体类型的完全限定名称在**EntityType**特性中使用。
    -   属性名称对应于上面)  (的 **DefiningQuery** 元素中的 SQL 语句返回的列名称。
    -   在此示例中，实体键由三个属性组成以确保唯一键值。

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> 如果以后运行 **模型更新向导** 对话框，对存储模型所做的任何更改（包括定义查询）都将被覆盖。

 

## <a name="add-an-entity-type-to-the-model"></a>向模型添加实体类型

在此步骤中，我们将使用 EF 设计器将实体类型添加到概念模型。 注意以下事项：

-   实体的**名称**与上面的**EntitySet**元素中的**EntityType**属性的值相对应。
-   属性名称对应于上面的 **DefiningQuery** 元素中的 SQL 语句返回的列名称。
-   在此示例中，实体键由三个属性组成以确保唯一键值。

在 EF 设计器中打开模型。

-   双击 "DefiningQueryModel"。
-   对以下消息说为 **"是"** ：

    ![警告2](~/ef6/media/warning2.png)

 

此时会显示 Entity Designer，它提供了用于编辑模型的设计图面。

-   右键单击设计器图面，然后选择 "**添加新** - &gt; **实体 ...**"。
-   为 "**键" 属性**的 "实体名称" 和 " **CourseID** " 指定**GradeReport** 。
-   右键单击 " **GradeReport** " 实体，然后选择 "**添加新** - &gt; 的**标量属性**"。
-   将属性的默认名称更改为 **FirstName**。
-   添加另一个标量属性，并为名称指定 **LastName** 。
-   添加另一个标量属性，并为该名称指定 **等级** 。
-   在 " **属性** " 窗口中，将 **评分**的 " **类型** " 属性更改为 **Decimal**。
-   选择 **FirstName** 和 **LastName** 属性。
-   在 " **属性** " 窗口中，将 **EntityKey** 属性值更改为 **True**。

因此，将以下元素添加到 .edmx 文件的 **CSDL** 部分。

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>将定义查询映射到实体类型

在此步骤中，我们将使用 "映射详细信息" 窗口来映射概念性实体类型和存储实体类型。

-   右键单击设计图面上的 " **GradeReport** " 实体，然后选择 " **表映射**"。  
    将显示 " **映射详细信息** " 窗口。
-   从 " ** &lt; 添加表" 或 "视图 &gt; ** " 下拉列表中选择 " **GradeReport** "， (位于**表**s) 下。  
    显示概念和存储 **GradeReport** 实体类型之间的默认映射。  
    ![映射 Details3](~/ef6/media/mappingdetails.png)

因此，会将**EntitySetMapping**   元素添加到 .edmx 文件的映射部分。 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   编译该应用程序。

 

## <a name="call-the-defining-query-in-your-code"></a>在代码中调用定义查询

现在可以使用 **GradeReport** 实体类型执行定义查询。 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
