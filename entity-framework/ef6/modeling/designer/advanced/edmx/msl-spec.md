---
title: MSL 规范-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
ms.openlocfilehash: 8990d1373ea2121ce11337a43dbcdf3b9e1532bd
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415477"
---
# <a name="msl-specification"></a>MSL 规范
映射规范语言（MSL）是一种基于 XML 的语言，用于描述实体框架应用程序的概念模型和存储模型之间的映射。

在实体框架应用程序中，将在生成时从 msl 文件（用 MSL 编写）加载映射元数据。 实体框架在运行时使用映射元数据将针对概念模型的查询转换为特定于存储的命令。

Entity Framework Designer （EF 设计器）在设计时将映射信息存储在 .edmx 文件中。 在生成时，Entity Designer 使用 .edmx 文件中的信息创建实体框架在运行时所需的 msl 文件

MSL 中引用的所有概念模型类型或存储模型类型的名称必须由其各自的命名空间名称限定。 有关概念模型命名空间名称的信息，请参阅[CSDL 规范](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。 有关存储模型命名空间名称的信息，请参阅[SSDL 规范](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。

MSL 版本由 XML 命名空间进行区分。

| MSL 版本 | XML 命名空间                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn：架构-microsoft-com： windows： storage：映射： CS |
| MSL v2      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| MSL v3      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Alias 元素 (MSL)

映射规范语言（MSL）中的**Alias**元素是映射元素的子元素，用于定义概念模型和存储模型命名空间的别名。 MSL 中引用的所有概念模型类型或存储模型类型的名称必须由其各自的命名空间名称限定。 有关概念模型命名空间名称的信息，请参阅 Schema 元素（CSDL）。 有关存储模型命名空间名称的信息，请参阅 Schema 元素（SSDL）。

**Alias**元素不能有子元素。

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于**Alias**元素的特性。

| 属性名称 | 是否必需 | 值                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **Key**        | 是         | **值**特性指定的命名空间的别名。 |
| **值**      | 是         | **键**元素的值为其别名的命名空间。     |

### <a name="example"></a>示例

下面的示例演示一个**alias**元素，该元素为在概念模型中定义的类型定义别名 `c`。

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a>AssociationEnd 元素 (MSL)

当概念模型中的实体类型的修改函数映射到基础数据库中的存储过程时，将使用映射规范语言（MSL）中的**AssociationEnd**元素。 如果修改存储过程所使用的参数的值保存在关联属性中，则**AssociationEnd**元素会将属性值映射到该参数。 有关更多信息，请参见下面的示例。

有关将实体类型的修改函数映射到存储过程的详细信息，请参阅 ModificationFunctionMapping 元素（MSL）和演练：将实体映射到存储过程。

**AssociationEnd**元素可以具有以下子元素：

-   ScalarProperty

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**AssociationEnd**元素的特性。

| 属性名称     | 是否必需 | 值                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | 是         | 要映射的关联的名称。                                                                                                                                 |
| **From**           | 是         | 导航属性的**FromRole**属性的值，该属性对应于要映射的关联。 有关详细信息，请参阅 NavigationProperty 元素（CSDL）。 |
| **收件人**             | 是         | 导航属性的**ToRole**属性的值，该属性对应于要映射的关联。 有关详细信息，请参阅 NavigationProperty 元素（CSDL）。   |

### <a name="example"></a>示例

请考虑使用以下概念模型实体类型：

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

另请考虑使用以下存储过程：

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

若要将 `Course` 实体的更新函数映射到此存储过程，必须为**DepartmentID**参数提供一个值。 `DepartmentID` 的值与实体类型中的某个属性不对应；该值包含在独立的关联中，此关联的映射如下所示：

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

下面的代码演示了**AssociationEnd**元素，该元素用于将**FK\_课程\_部门**关联的**DepartmentID**属性映射到**UpdateCourse**存储过程（**课程**实体类型的更新函数映射到该存储过程）：

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a>AssociationSetMapping 元素 (MSL)

映射规范语言（MSL）中的**AssociationSetMapping**元素定义概念模型中的关联与基础数据库中的表列之间的映射。

概念模型中的关联是一些属性表示基础数据库中的主键列和外键列的类型。 **AssociationSetMapping**元素使用两个 EndProperty 元素来定义关联类型属性与数据库中的列之间的映射。 您可以使用 Condition 元素对这些映射施加条件。 使用 ModificationFunctionMapping 元素将关联的插入、更新和删除函数映射到数据库中的存储过程。 通过在 QueryView 元素中使用实体 SQL 字符串，定义 association 和表列之间的只读映射。

> [!NOTE]
> 如果为概念模型中的关联定义了引用约束，则不需要将关联映射到**AssociationSetMapping**元素。 如果具有引用约束的关联存在**AssociationSetMapping**元素，则将忽略在**AssociationSetMapping**元素中定义的映射。 有关详细信息，请参阅 ReferentialConstraint 元素（CSDL）。

**AssociationSetMapping**元素可以具有以下子元素

-   QueryView （零个或一个）
-   EndProperty（零个或两个）
-   条件（零个或多个）
-   ModificationFunctionMapping （零个或一个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**AssociationSetMapping**元素的特性。

| 属性名称     | 是否必需 | 值                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **名称**           | 是         | 要映射的概念模型关联集的名称。                      |
| **TypeName**       | 是          | 要映射的概念模型关联类型的命名空间限定的名称。 |
| **StoreEntitySet** | 是          | 要映射的表的名称。                                                 |

### <a name="example"></a>示例

下面的示例显示一个**AssociationSetMapping**元素，在该元素中，在概念模型中设置的**FK\_课程\_部门**关联将映射到数据库中的**课程**表。 在子**EndProperty**元素中指定关联类型属性与表列之间的映射。

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a>ComplexProperty 元素 (MSL)

映射规范语言（MSL）中的**ComplexProperty**元素定义概念模型实体类型上的复杂类型属性与基础数据库中的表列之间的映射。 属性-列映射在子 ScalarProperty 元素中指定。

**ComplexType**属性元素可以具有以下子元素：

-   ScalarProperty （零个或多个）
-   **ComplexProperty** （零个或多个）
-   ComplextTypeMapping （零个或多个）
-   条件（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**ComplexProperty**元素的属性：

| 属性名称 | 是否必需 | 值                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名称**       | 是         | 概念模型中要映射的实体类型的复杂属性的名称。 |
| **TypeName**   | 是          | 概念模型属性类型的命名空间限定名称。                              |

### <a name="example"></a>示例

下面的示例基于 School 模型。 下面的复杂类型已添加到概念模型中：

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

**Person**实体类型的**LastName**和**FirstName**属性已替换为一个复杂属性**名称**：

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

以下 MSL 显示了用于将**Name**属性映射到基础数据库中的列的**ComplexProperty**元素：

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a>ComplexTypeMapping 元素 (MSL)

映射规范语言（MSL）中的**ComplexTypeMapping**元素是 ResultMapping 元素的子元素，在以下条件成立时定义概念模型中的函数导入与基础数据库中的存储过程之间的映射：

-   函数导入返回一个概念复杂类型。
-   存储过程返回的列的名称与复杂类型的属性名称不完全匹配。

默认情况下，存储过程返回的列与复杂类型返回的列之间的映射基于列名称和属性名称。 如果列名称与属性名称不完全匹配，则必须使用**ComplexTypeMapping**元素来定义映射。 有关默认映射的示例，请参阅 FunctionImportMapping 元素（MSL）。

**ComplexTypeMapping**元素可以具有以下子元素：

-   ScalarProperty （零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**ComplexTypeMapping**元素的特性。

| 属性名称 | 是否必需 | 值                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | 是         | 要映射的复杂类型的命名空间限定的名称。 |

### <a name="example"></a>示例

请思考以下存储过程：

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

另请考虑使用以下概念模型复杂类型：

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

为了创建返回以前复杂类型的实例的函数导入，必须在**ComplexTypeMapping**元素中定义存储过程返回的列与实体类型返回的列之间的映射：

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a>Condition 元素 (MSL)

映射规范语言（MSL）中的**Condition**元素对概念模型和基础数据库之间的映射施加条件。 如果满足子**条件**元素中指定的所有条件，则在 XML 节点内定义的映射将有效。 否则，该映射无效。 例如，如果 MappingFragment 元素包含一个或多个**Condition**子元素，则在满足子**条件**元素的所有条件时，在**MappingFragment**节点内定义的映射将有效。

每个条件可以应用于**名称**（概念模型实体属性的名称，由**Name**属性指定），也可以应用于**columnname** （数据库中列的名称，由**ColumnName**特性指定）。 设置**Name**属性时，将根据实体属性值检查条件。 设置**ColumnName**属性后，将根据列值检查条件。 只能在**Condition**元素中指定**Name**或**ColumnName**特性中的一个。

> [!NOTE]
> 如果在 FunctionImportMapping 元素中使用**Condition**元素，则仅**Name**属性不适用。

**Condition**元素可以是以下元素的子元素：

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

**Condition**元素不能有子元素。

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**Condition**元素的属性：

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | 是          | 表列的名称，其值用于计算条件。                                                                                                                                                                                                                   |
| **IsNull**     | 是          | **True** 或 **False**。 如果值为**True** ，列值为**null**，或者如果值为**False**且列值不为**null**，则条件为 true。 否则，条件为 False。 <br/> 不能同时使用**IsNull**和**Value**属性。 |
| **值**      | 是          | 要与列值进行比较的值。 如果值不同，则该条件为 True。 否则，条件为 False。 <br/> 不能同时使用**IsNull**和**Value**属性。                                                                       |
| **名称**       | 是          | 概念模型实体属性的名称，其值用于计算条件。 <br/> 如果在 FunctionImportMapping 元素中使用**Condition**元素，则此属性不适用。                                                                           |

### <a name="example"></a>示例

下面的示例将**条件**元素显示为**MappingFragment**元素的子元素。 当 **"雇佣**日期" 不为 Null 并且**EnrollmentDate**为 null 时，将**在 SchoolModel**类型与**Person**表的**PersonID**和**雇用**日期列之间映射数据。 如果**EnrollmentDate**不为 Null 且**雇佣**日期为 null，则会在**SchoolModel**类型与**Person**表的**PersonID**和**注册**列之间映射数据。

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a>DeleteFunction 元素 (MSL)

映射规范语言（MSL）中的**DeleteFunction**元素将概念模型中的实体类型或关联的删除函数映射到基础数据库中的存储过程。 将修改函数映射到其中的存储过程必须在存储模型中声明。 有关详细信息，请参阅 Function 元素（SSDL）。

> [!NOTE]
> 如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。

### <a name="deletefunction-applied-to-entitytypemapping"></a>应用于 EntityTypeMapping 的 DeleteFunction

当应用于 EntityTypeMapping 元素时， **DeleteFunction**元素会将概念模型中的实体类型的删除函数映射到存储过程。

**DeleteFunction**元素在应用于**EntityTypeMapping**元素时可以具有以下子元素：

-   AssociationEnd （零个或多个）
-   ComplexProperty（零个或多个）
-   ScarlarProperty （零个或多个）

#### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于**EntityTypeMapping**元素的**DeleteFunction**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 删除函数要映射到的存储过程的命名空间限定名称。 存储过程必须在存储模型中声明。 |
| **RowsAffectedParameter** | 是          | 返回受影响行数的输出参数的名称。                                                                               |

#### <a name="example"></a>示例

下面的示例基于 School 模型，并显示将**Person**实体类型的 delete 函数映射到**DeletePerson**存储过程的**DeleteFunction**元素。 **DeletePerson**存储过程在存储模型中声明。

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a>应用于 AssociationSetMapping 的 DeleteFunction

当应用于 AssociationSetMapping 元素时， **DeleteFunction**元素会将概念模型中的关联的删除函数映射到存储过程。

**DeleteFunction**元素在应用于**AssociationSetMapping**元素时可以具有以下子元素：

-   EndProperty

#### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于**AssociationSetMapping**元素的**DeleteFunction**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 删除函数要映射到的存储过程的命名空间限定名称。 存储过程必须在存储模型中声明。 |
| **RowsAffectedParameter** | 是          | 返回受影响行数的输出参数的名称。                                                                               |

#### <a name="example"></a>示例

下面的示例基于 School 模型，并显示了用于将**courseinstructor.courseid**关联的删除函数映射到**DeleteCourseInstructor**存储过程的**DeleteFunction**元素。 **DeleteCourseInstructor**存储过程在存储模型中声明。

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a>EndProperty 元素 (MSL)

映射规范语言（MSL）中的**EndProperty**元素定义概念模型关联和基础数据库的结束或修改函数之间的映射。 属性-列映射在子 ScalarProperty 元素中指定。

当使用**EndProperty**元素定义概念模型关联末尾的映射时，它是 AssociationSetMapping 元素的子元素。 当使用**EndProperty**元素定义概念模型关联的修改函数的映射时，它是 InsertFunction 元素或 DeleteFunction 元素的子元素。

**EndProperty**元素可以具有以下子元素：

-   ScalarProperty （零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**EndProperty**元素的属性：

| 属性名称 | 是否必需 | 值                                                 |
|:---------------|:------------|:------------------------------------------------------|
| 名称           | 是         | 要映射的关联端的名称。 |

### <a name="example"></a>示例

下面的示例显示一个**AssociationSetMapping**元素，在该元素中，概念模型中的**FK\_课程\_部门**关联映射到数据库中的**课程**表。 在子**EndProperty**元素中指定关联类型属性与表列之间的映射。

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a>示例

下面的示例显示了**EndProperty**元素，该元素将关联（**courseinstructor.courseid**）的插入和删除函数映射到基础数据库中的存储过程。 映射到的函数在存储模型中声明。

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a>EntityContainerMapping 元素 (MSL)

映射规范语言（MSL）中的**EntityContainerMapping**元素将概念模型中的实体容器映射到存储模型中的实体容器。 **EntityContainerMapping**元素是 Mapping 元素的子元素。

**EntityContainerMapping**元素可以具有以下子元素（按所列顺序）：

-   EntitySetMapping（零个或多个）
-   AssociationSetMapping （零个或多个）
-   FunctionImportMapping （零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**EntityContainerMapping**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | 是         | 要映射到的存储模型实体容器的名称。                                                                                                                                                                                     |
| **CdmEntityContainer**    | 是         | 要映射的概念模型实体容器的名称。                                                                                                                                                                                  |
| **GenerateUpdateViews**   | 是          | **True** 或 **False**。 如果**为 False**，则不生成任何更新视图。 如果你具有只读映射，则此属性应设置为**False** ，因为数据可能无法成功往返。 <br/> 默认值为 **True**。 |

### <a name="example"></a>示例

下面的示例演示了一个**EntityContainerMapping**元素，该元素将**SchoolModelEntities**容器（概念模型实体容器）映射到**SchoolModelStoreContainer**容器（存储模型实体容器）：

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a>EntitySetMapping 元素 (MSL)

映射规范语言（MSL）中的**EntitySetMapping**元素将概念模型实体集中的所有类型都映射到存储模型中的实体集。 概念模型中的实体集为同一类型（和派生类型）实体的实例的逻辑容器。 存储模型中的实体集表示基础数据库中的一个表或视图。 概念模型实体集由**EntitySetMapping**元素的**Name**特性的值指定。 映射到的表或视图由每个子 MappingFragment 元素或**EntitySetMapping**元素本身中的**StoreEntitySet**属性指定。

**EntitySetMapping**元素可以具有以下子元素：

-   EntityTypeMapping（零个或多个）
-   QueryView （零个或一个）
-   MappingFragment （零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**EntitySetMapping**元素的特性。

| 属性名称           | 是否必需 | 值                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**                 | 是         | 要映射的概念模型实体集的名称。                                                                                                                                                             |
| **TypeName** **1**       | 是          | 要映射的概念模型实体类型的名称。                                                                                                                                                            |
| **StoreEntitySet** **1** | 是          | 要映射到的存储模型实体集的名称。                                                                                                                                                             |
| **MakeColumnsDistinct**  | 是          | **True**或**False** ，具体取决于是否只返回不同的行。 <br/> 如果此特性设置为**True**，则 EntityContainerMapping 元素的**GenerateUpdateViews**特性必须设置为**False**。 |

 

**1**可以使用**TypeName**和**StoreEntitySet**特性替代 EntityTypeMapping 和 MappingFragment 子元素，以便将单个实体类型映射到单个表。

### <a name="example"></a>示例

下面的示例显示一个**EntitySetMapping**元素，该元素将概念模型的**课程**实体集中的三个类型（基类型和两个派生类型）映射到基础数据库中的三个不同表。 这些表由每个**MappingFragment**元素中的**StoreEntitySet**属性指定。

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a>EntityTypeMapping 元素 (MSL)

映射规范语言（MSL）中的**EntityTypeMapping**元素定义概念模型中的实体类型与基础数据库中的表或视图之间的映射。 有关概念模型实体类型与基础数据库表或视图的信息，请参见 EntityType 元素 (CSDL) 和 EntitySet 元素 (SSDL)。 要映射的概念模型实体类型由**EntityTypeMapping**元素的**TypeName**特性指定。 要映射的表或视图由子 MappingFragment 元素的**StoreEntitySet**属性指定。

ModificationFunctionMapping 子元素可以用于将实体类型的插入、更新、或删除函数映射到数据库中的存储过程。

**EntityTypeMapping**元素可以具有以下子元素：

-   MappingFragment （零个或多个）
-   ModificationFunctionMapping （零个或一个）
-   ScalarProperty
-   条件

> [!NOTE]
> **MappingFragment**和**ModificationFunctionMapping**元素不能同时是**EntityTypeMapping**元素的子元素。


> [!NOTE]
> 当**ScalarProperty**和**Condition**元素在 FunctionImportMapping 元素中使用时，它们只能是**EntityTypeMapping**元素的子元素。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**EntityTypeMapping**元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | 是         | 要映射的概念模型实体类型的命名空间限定的名称。 <br/> 如果类型为抽象类型或派生类型，则值必须为 `IsOfType(Namespace-qualified_type_name)`。 |

### <a name="example"></a>示例

下面的示例演示一个具有两个子**EntityTypeMapping**元素的 EntitySetMapping 元素。 在第一个**EntityTypeMapping**元素中， **SchoolModel**实体类型映射到**person**表。 在第二个**EntityTypeMapping**元素中， **SchoolModel**类型的更新功能映射到数据库中的存储过程**UpdatePerson**。

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>示例

下一示例演示其中根类型为抽象类型的类型层次结构的映射。 请注意，将 `IsOfType` 语法用于**TypeName**特性。

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a>FunctionImportMapping 元素 (MSL)

映射规范语言（MSL）中的**FunctionImportMapping**元素定义概念模型中的函数导入与基础数据库中的存储过程或函数之间的映射。 函数导入必须在概念模型中进行声明，存储的过程必须在存储模型中进行声明。 有关详细信息，请参阅 FunctionImport 元素（CSDL）和 Function 元素（SSDL）。

> [!NOTE]
> 默认情况下，如果函数导入返回概念模型实体类型或复杂类型，则基础存储过程返回的列名称必须与概念模型类型中的属性名称完全匹配。 如果列名称与属性名称不完全匹配，则必须在 ResultMapping 元素中定义映射。

**FunctionImportMapping**元素可以具有以下子元素：

-   ResultMapping （零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**FunctionImportMapping**元素的属性：

| 属性名称         | 是否必需 | 值                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | 是         | 概念模型中要映射的函数导入的名称。           |
| **FunctionName**       | 是         | 存储模型中要映射的函数的命名空间限定名称。 |

### <a name="example"></a>示例

下面的示例基于 School 模型。 请考虑在存储模型中使用以下函数：

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

另请考虑在概念模型中使用此函数导入：

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

下面的示例显示一个**FunctionImportMapping**元素，该元素用于将上述函数和函数导入映射到彼此：

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction 元素 (MSL)

映射规范语言（MSL）中的**InsertFunction**元素将概念模型中的实体类型或关联的插入函数映射到基础数据库中的存储过程。 将修改函数映射到其中的存储过程必须在存储模型中声明。 有关详细信息，请参阅 Function 元素（SSDL）。

> [!NOTE]
> 如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。

**InsertFunction**元素可以是 ModificationFunctionMapping 元素的子元素，并应用于 EntityTypeMapping 元素或 AssociationSetMapping 元素。

### <a name="insertfunction-applied-to-entitytypemapping"></a>应用于 EntityTypeMapping 的 InsertFunction

当应用于 EntityTypeMapping 元素时， **InsertFunction**元素会将概念模型中的实体类型的插入函数映射到存储过程。

**InsertFunction**元素在应用于**EntityTypeMapping**元素时可以具有以下子元素：

-   AssociationEnd （零个或多个）
-   ComplexProperty（零个或多个）
-   ResultBinding （零个或一个）
-   ScarlarProperty （零个或多个）

#### <a name="applicable-attributes"></a>适用的属性

下表描述了应用于**EntityTypeMapping**元素时可应用于**InsertFunction**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 插入函数要映射到的存储过程的命名空间限定名称。 存储过程必须在存储模型中声明。 |
| **RowsAffectedParameter** | 是          | 返回受影响行数的输出参数的名称。                                                                               |

#### <a name="example"></a>示例

下面的示例基于 School 模型，并显示了用于将 Person 实体类型的插入函数映射到**InsertPerson**存储过程的**InsertFunction**元素。 **InsertPerson**存储过程在存储模型中声明。

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a>应用于 AssociationSetMapping 的 InsertFunction

当应用于 AssociationSetMapping 元素时， **InsertFunction**元素会将概念模型中的关联的插入函数映射到存储过程。

**InsertFunction**元素在应用于**AssociationSetMapping**元素时可以具有以下子元素：

-   EndProperty

#### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于**AssociationSetMapping**元素的**InsertFunction**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 插入函数要映射到的存储过程的命名空间限定名称。 存储过程必须在存储模型中声明。 |
| **RowsAffectedParameter** | 是          | 返回受影响行数的输出参数的名称。                                                                               |

#### <a name="example"></a>示例

下面的示例基于 School 模型，并显示了用于将**courseinstructor.courseid**关联的插入函数映射到**InsertCourseInstructor**存储过程的**InsertFunction**元素。 **InsertCourseInstructor**存储过程在存储模型中声明。

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a>Mapping 元素 (MSL)

映射规范语言（MSL）中的**mapping**元素包含用于将概念模型中定义的对象映射到数据库的信息（如存储模型中所述）。 有关详细信息，请参阅 CSDL 规范和 SSDL 规范。

**Mapping**元素是映射规范的根元素。 https://schemas.microsoft.com/ado/2009/11/mapping/cs映射规范的 XML 命名空间。

映射元素可以具有以下子元素（按所列顺序）：

-   别名（0个或多个）
-   EntityContainerMapping （恰好一个）

MSL 中引用的概念模型类型和存储模型类型的名称必须由其相应的命名空间名称限定。 有关概念模型命名空间名称的信息，请参阅 Schema 元素（CSDL）。 有关存储模型命名空间名称的信息，请参阅 Schema 元素（SSDL）。 可以使用 Alias 元素定义 MSL 中使用的命名空间的别名。

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用到**Mapping**元素的特性。

| 属性名称 | 是否必需 | 值                                                 |
|:---------------|:------------|:------------------------------------------------------|
| 空格键      | 是         | **C-S**。 这是固定值，因此不能更改。 |

### <a name="example"></a>示例

下面的示例演示一个基于 School 模型的一部分的**映射**元素。 有关 School 模型的详细信息，请参阅快速入门（实体框架）：

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a>MappingFragment 元素 (MSL)

映射规范语言（MSL）中的**MappingFragment**元素定义概念模型实体类型的属性与数据库中的表或视图之间的映射。 有关概念模型实体类型与基础数据库表或视图的信息，请参见 EntityType 元素 (CSDL) 和 EntitySet 元素 (SSDL)。 **MappingFragment**可以是 EntityTypeMapping 元素或 EntitySetMapping 元素的子元素。

**MappingFragment**元素可以具有以下子元素：

-   ComplexType （零个或多个）
-   ScalarProperty （零个或多个）
-   条件（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**MappingFragment**元素的特性。

| 属性名称          | 是否必需 | 值                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | 是         | 要映射的表或视图的名称。                                                                                                                                                                           |
| **MakeColumnsDistinct** | 是          | **True**或**False** ，具体取决于是否只返回不同的行。 <br/> 如果此特性设置为**True**，则 EntityContainerMapping 元素的**GenerateUpdateViews**特性必须设置为**False**。 |

### <a name="example"></a>示例

下面的示例演示一个**MappingFragment**元素作为**EntityTypeMapping**元素的子元素。 在此示例中，概念模型中的**课程**类型的属性将映射到数据库中**课程**表的列。

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>示例

下面的示例演示一个**MappingFragment**元素作为**EntitySetMapping**元素的子元素。 如上面的示例所示，概念模型中的**课程**类型的属性将映射到数据库中**课程**表的列。

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a>ModificationFunctionMapping 元素 (MSL)

映射规范语言（MSL）中的**ModificationFunctionMapping**元素将概念模型实体类型的插入、更新和删除函数映射到基础数据库中的存储过程。 **ModificationFunctionMapping**元素还可以将概念模型中的多对多关联的插入和删除函数映射到基础数据库中的存储过程。 将修改函数映射到其中的存储过程必须在存储模型中声明。 有关详细信息，请参阅 Function 元素（SSDL）。

> [!NOTE]
> 如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。


> [!NOTE]
> 如果将继承层次结构中的一个实体的修改函数映射到存储过程，则必须将该层次结构中所有类型的修改函数都映射到存储过程。

**ModificationFunctionMapping**元素可以是 EntityTypeMapping 元素或 AssociationSetMapping 元素的子元素。

**ModificationFunctionMapping**元素可以具有以下子元素：

-   DeleteFunction （零个或一个）
-   InsertFunction （零个或一个）
-   UpdateFunction （零个或一个）

没有适用于**ModificationFunctionMapping**元素的特性。

### <a name="example"></a>示例

下面的示例演示了 School 模型中 "**人员**" 实体集的实体集映射。 除了**person**实体类型的列映射外，还会显示 "**人员**" 类型的插入、更新和删除函数的映射。 映射到的函数在存储模型中声明。

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>示例

下面的示例演示了 School 模型中**courseinstructor.courseid**关联集的关联集映射。 除了**courseinstructor.courseid**关联的列映射外，还会显示**courseinstructor.courseid**关联的插入和删除函数的映射。 映射到的函数在存储模型中声明。

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a>QueryView 元素 (MSL)

映射规范语言（MSL）中的**QueryView**元素定义概念模型中的实体类型或关联与基础数据库中的表之间的只读映射。 映射是使用针对存储模型计算的实体 SQL 查询定义的，你可以根据概念模型中的实体或关联来表示结果集。 因为查询视图是只读的，所以不能使用标准更新命令来更新查询视图所定义的类型。 可以使用修改函数来更新这些类型。 有关详细信息，请参阅如何：将修改函数映射到存储过程。

> [!NOTE]
> 在**QueryView**元素中，不支持实体 SQL 包含**GroupBy**、组聚合或导航属性的表达式。

 

**QueryView**元素可以是 EntitySetMapping 元素或 AssociationSetMapping 元素的子元素。 在前一种情况中，查询视图定义概念模型中实体的只读映射。 在后一种情况中，查询视图定义概念模型中关联的只读映射。

> [!NOTE]
> 如果**AssociationSetMapping**元素用于与引用约束的关联，则忽略**AssociationSetMapping**元素。 有关详细信息，请参阅 ReferentialConstraint 元素（CSDL）。

**QueryView**元素不能具有任何子元素。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**QueryView**元素的特性。

| 属性名称 | 是否必需 | 值                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | 是          | 要由查询视图映射的概念模型类型的名称。 |

### <a name="example"></a>示例

下面的示例将**QueryView**元素显示为**EntitySetMapping**元素的子元素，并为 School 模型中的**部门**实体类型定义查询视图映射。

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

由于该查询仅返回存储模型中 "**部门**" 类型的成员子集，因此，School 模型中的**部门**类型已根据以下映射进行了修改：

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a>示例

下一个示例显示了**QueryView**元素作为**AssociationSetMapping**元素的子元素，并为 School 模型中的 `FK_Course_Department` 关联定义了只读映射。

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a>Comments

可以定义查询视图来实现以下方案：

-   在概念模型中定义一个实体，该实体不包含存储模型中的实体的所有属性。 这包括没有默认值并且不支持**null**值的属性。
-   将存储模型中计算的列映射到概念模型中实体类型的属性。
-   定义一个映射，其中用于对概念模型中的实体进行分区的条件不基于相等性。 使用**Condition**元素指定条件映射时，所提供的条件必须等于指定的值。 有关详细信息，请参阅 Condition 元素（MSL）。
-   将存储模型中的同一列映射到概念模型中的多个类型。
-   将多个类型映射到同一个表。
-   在概念模型中定义不基于关系架构中的外键的关联。
-   使用自定义业务逻辑设置概念模型中的属性值。 例如，您可以将数据源中的字符串值 "T" 映射到概念模型中的**true**值（布尔值）。
-   为查询结果定义条件筛选器。
-   强制对概念模型中的数据施加比存储模型中更少的限制。 例如，您可以将概念模型中的属性设置为可以为 null，即使它映射到的列不支持**null**值。

为实体定义查询视图时需要考虑以下注意事项：

-   查询视图是只读的。 只能使用修改函数来更新实体。
-   通过查询视图定义实体类型时，必须也通过查询视图来定义所有相关实体。
-   当您将多对多关联映射到存储模型中表示关系架构中的链接表的实体时，必须在此链接表的**AssociationSetMapping**元素中定义**QueryView**元素。
-   必须为类型层次结构中的所有类型定义查询视图。 可以使用下列方式来实现：
-   -   使用单个**QueryView**元素指定单个实体 SQL 查询，该查询返回层次结构中所有实体类型的联合。
    -   使用单个**QueryView**元素，该元素指定单个实体 SQL 查询，该查询使用 CASE 运算符根据特定条件返回层次结构中的特定实体类型。
    -   对于层次结构中的特定类型，使用额外的**QueryView**元素。 在这种情况下，请使用**QueryView**元素的**TypeName**特性来指定每个视图的实体类型。
-   定义查询视图时，不能在**EntitySetMapping**元素上指定**StorageSetName**属性。
-   定义查询视图时， **EntitySetMapping**元素不能同时包含**属性**映射。

## <a name="resultbinding-element-msl"></a>ResultBinding 元素 (MSL)

当实体类型修改函数映射到基础数据库中的存储过程时，映射规范语言（MSL）中的**ResultBinding**元素将存储过程返回的列值映射到概念模型中的实体属性。 例如，当 insert 存储过程返回标识列的值时， **ResultBinding**元素会将返回的值映射到概念模型中的实体类型属性。

**ResultBinding**元素可以是 InsertFunction 元素或 UpdateFunction 元素的子元素。

**ResultBinding**元素不能具有任何子元素。

### <a name="applicable-attributes"></a>适用的属性

下表描述了适用于**ResultBinding**元素的属性：

| 属性名称 | 是否必需 | 值                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **名称**       | 是         | 概念模型中要映射的实体属性的名称。 |
| **ColumnName** | 是         | 要映射的列的名称。                                          |

### <a name="example"></a>示例

下面的示例基于 School 模型，并显示用于将**Person**实体类型的插入函数映射到**InsertPerson**存储过程的**InsertFunction**元素。 （ **InsertPerson**存储过程如下所示，在存储模型中声明。）**ResultBinding**元素用于将存储过程（**NewPersonID**）返回的列值映射到实体类型属性（**PersonID**）。

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

以下 Transact-sql 介绍了**InsertPerson**存储过程：

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a>ResultMapping 元素 (MSL)

映射规范语言（MSL）中的**ResultMapping**元素定义概念模型中的函数导入与基础数据库中的存储过程之间的映射（如果满足以下条件）：

-   函数导入返回一个概念模型实体类型或复杂类型。
-   存储过程返回的列的名称与实体类型或复杂类型中的属性名称不完全匹配。

默认情况下，存储过程返回的列与实体类型或复杂类型中的属性之间的映射基于列名称和属性名称。 如果列名称与属性名称不完全匹配，则必须使用**ResultMapping**元素来定义映射。 有关默认映射的示例，请参阅 FunctionImportMapping 元素（MSL）。

**ResultMapping**元素是 FunctionImportMapping 元素的子元素。

**ResultMapping**元素可以具有以下子元素：

-   EntityTypeMapping（零个或多个）
-   ComplexTypeMapping

没有适用于**ResultMapping**元素的特性。

### <a name="example"></a>示例

请思考以下存储过程：

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

另请考虑使用以下概念模型实体类型：

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

若要创建返回以前实体类型的实例的函数导入，必须在**ResultMapping**元素中定义存储过程返回的列与实体类型返回的列之间的映射：

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a>ScalarProperty 元素 (MSL)

映射规范语言（MSL）中的**ScalarProperty**元素将概念模型实体类型、复杂类型或关联的属性映射到基础数据库中的表列或存储过程参数。

> [!NOTE]
> 将修改函数映射到其中的存储过程必须在存储模型中声明。 有关详细信息，请参阅 Function 元素（SSDL）。

**ScalarProperty**元素可以是以下元素的子元素：

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

作为**MappingFragment**、 **ComplexProperty**或**EndProperty**元素的子元素， **ScalarProperty**元素会将概念模型中的属性映射到数据库中的某一列。 作为**InsertFunction**、 **UpdateFunction**或**DeleteFunction**元素的子元素， **ScalarProperty**元素会将概念模型中的属性映射到存储过程参数。

**ScalarProperty**元素不能具有任何子元素。

### <a name="applicable-attributes"></a>适用的属性

应用于**ScalarProperty**元素的特性因元素的角色而异。

下表介绍了在**ScalarProperty**元素用于将概念模型属性映射到数据库中的列时适用的特性：

| 属性名称 | 是否必需 | 值                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名称**       | 是         | 要映射的概念模型属性的名称。 |
| **ColumnName** | 是         | 要映射的表列的名称。              |

下表描述了当**ScalarProperty**元素用于将概念模型属性映射到存储过程参数时适用的特性：

| 属性名称    | 是否必需 | 值                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**          | 是         | 要映射的概念模型属性的名称。                                                                                 |
| **ParameterName** | 是         | 正在映射的参数的名称。                                                                                                 |
| **版本**       | 是          | **当前**或**原始**值，具体取决于是否应将当前值或属性的原始值用于并发检查。 |

### <a name="example"></a>示例

下面的示例演示了两种方法中使用的**ScalarProperty**元素：

-   将**person**实体类型的属性映射到**person**表的列。
-   将**Person**实体类型的属性映射到**UpdatePerson**存储过程的参数。 存储过程在存储模型中声明。

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>示例

下一个示例显示了**ScalarProperty**元素，该元素用于将概念模型关联的插入和删除函数映射到数据库中的存储过程。 存储过程在存储模型中声明。

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a>UpdateFunction 元素 (MSL)

映射规范语言（MSL）中的**UpdateFunction**元素将概念模型中的实体类型的更新函数映射到基础数据库中的存储过程。 将修改函数映射到其中的存储过程必须在存储模型中声明。 有关详细信息，请参阅 Function 元素（SSDL）。

> [!NOTE]
>  如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。

**UpdateFunction**元素可以是 ModificationFunctionMapping 元素的子元素并应用于 EntityTypeMapping 元素。

**UpdateFunction**元素可以具有以下子元素：

-   AssociationEnd （零个或多个）
-   ComplexProperty（零个或多个）
-   ResultBinding （零个或一个）
-   ScarlarProperty （零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**UpdateFunction**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | 是         | 更新函数要映射到的存储过程的命名空间限定名称。 存储过程必须在存储模型中声明。 |
| **RowsAffectedParameter** | 是          | 返回受影响行数的输出参数的名称。                                                                               |

### <a name="example"></a>示例

下面的示例基于 School 模型，并显示用于将**Person**实体类型的更新函数映射到**UpdatePerson**存储过程的**UpdateFunction**元素。 **UpdatePerson**存储过程在存储模型中声明。

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
