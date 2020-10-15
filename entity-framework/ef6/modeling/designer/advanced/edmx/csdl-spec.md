---
title: CSDL 规范-EF6
description: 实体框架6中的 CSDL 规范
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 9fdd8fc5ed16f7ba7d11e79a9449f120f5d579c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066351"
---
# <a name="csdl-specification"></a>CSDL 规范
概念架构定义语言 (CSDL) 是一种基于 XML 的语言，它描述构成数据驱动应用程序的概念模型的实体、关系和函数。 此概念模型可由实体框架或 WCF 数据服务使用。 实体框架使用 CSDL 描述的元数据将概念模型中定义的实体和关系映射到数据源。 有关详细信息，请参阅 [SSDL 规范](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) 和 [MSL 规范](xref:ef6/modeling/designer/advanced/edmx/msl-spec)。

CSDL 是实体数据模型的实体框架实现。

在实体框架应用程序中，概念模型的元数据将从使用) CSDL 编写的 csdl 文件 (加载到 EdmItemCollection 的实例中，并且可通过使用 System.web 类中的方法进行访问。 实体框架使用概念模型元数据将针对概念模型的查询转换为特定于数据源的命令。

EF 设计器会在设计时将概念模型信息存储在 .edmx 文件中。 在生成时，EF 设计器使用 .edmx 文件中的信息来创建运行时实体框架所需的 csdl 文件。

CSDL 的版本按 XML 命名空间进行区分。

| CSDL 版本 | XML 命名空间                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm |
| CSDL v2      | https://schemas.microsoft.com/ado/2008/09/edm |
| CSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Association 元素 (CSDL)

**Association**元素定义了两个实体类型之间的关系。 关联必须指定关系中涉及的实体类型和关系的每一端可能的实体类型数量（也称为重数）。 关联端的多重性的 (值可以是 1) 、零或一个 (0 .. 1) 或多个 (\*) 。 此信息在两个 End 子元素中指定。

通过导航属性或外键（如果在实体类型上公开了外键）可以访问关联一端的实体类型实例。

在应用程序中，关联的实例表示实体类型的实例之间的特定关联。 关联实例按逻辑分组在关联集中。

**Association**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   End（正好两个元素）
-   ReferentialConstraint（零个或多个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **Association** 元素的特性。

| 属性名称 | 是否必需 | 值                        |
|:---------------|:------------|:-----------------------------|
| **名称**       | 是         | 关联的名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **Association** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个**association**元素，该元素定义在**Customer**和**Order**实体类型上没有公开外键时的**CustomerOrders**关联。 关联的每个**端点**的**多重性**值指示可以将多个**订单**与一个**客户**关联，但只能有一个**客户**与一个**订单**相关联。 此外， **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

下面的示例演示一个**association**元素，该元素定义在**Customer**和**Order**实体类型上公开外键时的**CustomerOrders**关联。 公开外键后，实体之间的关系将通过 **ReferentialConstraint** 元素进行管理。 将此关联映射到数据源并不需要 AssociationSetMapping 元素。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a>AssociationSet 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **AssociationSet** 元素是同一类型的关联实例的逻辑容器。 关联集为对关联实例进行分组提供了定义，以便能够将它们映射到数据源。  

**AssociationSet**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（允许零个或一个元素）
-   End（需要正好两个元素）
-   Annotation 元素（允许零个或多个元素）

**Association**特性指定关联集包含的关联类型。 构成关联集末尾的实体集是通过恰好两个子 **End** 元素指定的。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **AssociationSet** 元素的特性。

| 属性名称  | 是否必需 | 值                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**        | 是         | 实体集的名称。 **Name**特性的值不能与**Association**特性的值相同。                                 |
| **关联** | 是         | 关联集包含其实例的关联的完全限定名称。 关联必须与关联集位于同一个命名空间中。 |

 

> [!NOTE]
> 任意数量的批注属性 (自定义 XML 特性) 可以应用于 **AssociationSet** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示具有两个**AssociationSet**元素的**EntityContainer**元素：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a>CollectionType 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **CollectionType** 元素指定函数参数或函数返回类型是集合。 **CollectionType**元素可以是参数元素的子元素，也可以是 ReturnType (函数) 元素的子元素。 可以使用 **type** 特性或以下子元素之一指定集合类型：

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> 如果集合类型是使用 **type** 特性和子元素指定的，则模型不会进行验证。

 

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **CollectionType** 元素的特性。 请注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **精度**、 **小数位数**、 **Unicode**和 **排序规则** 特性仅适用于 **edmsimpletype**的集合。

| 属性名称                                                          | 是否必需 | 值                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **类型**                                                                | 否          | 集合的类型。                                                                                                                                                                                                      |
| **可以为 Null**                                                            | 否          | **True**（默认值）或 **False**，取决于属性是否可以具有 null 值。 <br/> [!NOTE]                                                                                                                 |
| 在 CSDL v1 中 >，复杂类型属性必须具有 `Nullable="False"` 。 |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | 否          | 属性的默认值。                                                                                                                                                                                               |
| **MaxLength**                                                           | 否          | 属性值的最大长度。                                                                                                                                                                                        |
| **FixedLength**                                                         | 否          | **True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。                                                                                                                           |
| **精度**                                                           | 否          | 属性值的精度。                                                                                                                                                                                             |
| **缩放**                                                               | 否          | 属性值的刻度。                                                                                                                                                                                                 |
| SRID                                                                 | 否          | 空间系统引用标识符。 仅对空间类型的属性有效。有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx) |
| **Unicode**                                                             | 否          | **True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。                                                                                                                                |
| **排序规则**                                                           | 否          | 指定要在数据源中使用的排序的字符串。                                                                                                                                                    |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **CollectionType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个模型定义函数，该函数使用 **CollectionType** 元素来指定该函数返回 **Person** 类型的集合，该集合 (使用 **ElementType** 特性) 指定。

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

下面的示例演示一个模型定义函数，该函数使用 **CollectionType** 元素来指定该函数返回 (在 **RowType** 元素) 中指定的行的集合。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

下面的示例演示一个模型定义的函数，该函数使用 **CollectionType** 元素来指定该函数接受作为参数的 **部门** 实体类型的集合。

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a>ComplexType 元素 (CSDL)

**ComplexType**元素定义由**EdmSimpleType**属性或其他复杂类型组成的数据结构。复杂类型可以是实体类型的属性或其他复杂类型的属性。 复杂类型类似于复杂类型定义数据中的实体类型。 但是，在复杂类型与实体类型之间仍存在着一些重要区别：

-   复杂类型没有标识（或键），因此不能独立存在。 复杂类型只能作为实体类型或其他复杂类型的属性而存在。
-   复杂类型不能参与关联。 关联的任一端都不能是复杂类型，因此不能为复杂类型定义导航属性。
-   复杂类型属性不能具有 null 值，但可以将复杂类型的每个标量属性设置为 null。

**ComplexType**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   Property（零个或多个元素）
-   批注元素（零个或多个元素）

下表介绍可应用于 **ComplexType** 元素的特性。

| 属性名称                                                                                                 | 是否必需 | 值                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名称                                                                                                           | 是         | 复杂类型的名称。 复杂类型的名称不能与模型作用域中的其他复杂类型、实体类型或关联的名称相同。 |
| BaseType                                                                                                       | 否          | 作为所定义的复杂类型的基类型的另一个复杂类型的名称。 <br/> [!NOTE]                                                                     |
| > 此属性在 CSDL v1 中不适用。 在该版本中不支持复杂类型的继承。 |             |                                                                                                                                                                                     |
| 摘要                                                                                                       | 否          | **True** 或 **False** (默认值) 取决于复杂类型是否为抽象类型。 <br/> [!NOTE]                                                                  |
| > 此属性在 CSDL v1 中不适用。 该版本中的复杂类型不能是抽象类型。         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **ComplexType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例显示了一个复杂类型 **Address**，其中包含 **EdmSimpleType** 属性 **StreetAddress**、 **City**、 **StateOrProvince**、 **国家/地区**和 **邮政编码**。

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

若要将上述复杂类型 **地址** 定义 () 为某个实体类型的属性，必须在实体类型定义中声明该属性类型。 下面的示例将 **Address** 属性显示为) 的实体类型 (**发布服务器** 上的复杂类型：

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a>DefiningExpression 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **DefiningExpression** 元素包含一个实体 SQL 定义概念模型中的函数的表达式。  

> [!NOTE]
> 出于验证目的， **DefiningExpression** 元素可以包含任意内容。 但是，如果 **DefiningExpression** 元素不包含有效的实体 SQL，则实体框架在运行时将引发异常。

 

### <a name="applicable-attributes"></a>适用的属性

可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **DefiningExpression** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例使用 **DefiningExpression** 元素来定义一个函数，该函数返回发布书籍之后的年数。 **DefiningExpression**元素的内容是以实体 SQL 编写的。

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent 元素 (CSDL)

概念架构定义语言中 (CSDL) 的 **依赖** 元素是 ReferentialConstraint 元素的子元素，用于定义引用约束的依赖端。 **ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。 与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。 引用的实体类型称为约束的 *主体端* 。 引用主体端的实体类型称为约束的 *依赖端* 。 **PropertyRef** 元素用于指定哪些键引用主体端。

**依赖**元素可以具有以下子元素 (按列出的顺序) ：

-   PropertyRef（一个或多个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **依赖** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **角色**       | 是         | 关联的依赖端的实体类型的名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **依赖** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示了在**PublishedBy**关联的定义中使用的**ReferentialConstraint**元素。 **Book**实体类型的**PublisherId**属性构成了引用约束的依赖端。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a>Documentation 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **文档** 元素可用于提供有关在父元素中定义的对象的信息。 在 .edmx 文件中，当 **文档** 元素是元素的子元素（该元素显示为 EF 设计器设计图面上的一个对象） (如实体、关联或属性) ）时， **文档** 元素的内容将显示在该对象的 Visual Studio " **属性** " 窗口中。

**文档**元素可以具有以下子元素 (按列出的顺序) ：

-   **摘要**：父元素的简要说明。 （零个或一个元素）
-   **LongDescription**：父元素的详细说明。 （零个或一个元素）
-   批注元素. （零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **文档** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例将 **文档** 元素显示为 EntityType 元素的子元素。 如果下面的代码片段在 .edmx 文件的 CSDL 内容中，则当您单击该实体类型时， **Summary** 和 **LongDescription** 元素的内容将显示在 Visual Studio 的 " **属性** " 窗口中 `Customer` 。

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a>End 元素 (CSDL)

概念架构定义语言中 (CSDL) 的 **结束** 元素可以是 Association 元素或 AssociationSet 元素的子元素。 在每种情况下， **End** 元素的角色不同，适用的属性也不同。

### <a name="end-element-as-a-child-of-the-association-element"></a>End 元素作为 Association 元素的子元素

**End**元素 (作为**association**元素的子元素，) 标识关联一端的实体类型以及该关联端可以存在的实体类型实例的数量。 关联端定义为关联的一部分；关联必须正好有两个关联端。 通过导航属性或外键（如果在实体类型上公开了外键）可以访问关联一端的实体类型实例。  

**End**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   OnDelete（零个或一个元素）
-   批注元素（零个或多个元素）

#### <a name="applicable-attributes"></a>适用的属性

下表描述了当 **结束** 元素是 **Association** 元素的子元素时，可应用于该元素的特性。

| 属性名称   | 是否必需 | 值                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 类型         | 是         | 关联一端的实体类型的名称。                                                                                                                                                                                                                                                                                                                                                         |
| **角色**         | 否          | 关联端的名称。 如果不提供名称，将使用关联端的实体类型的名称。                                                                                                                                                                                                                                                                                           |
| **多重性** | 是         | **1**、 **0 或 1**或， **\*** 具体取决于可在关联末尾的实体类型实例的数量。 <br/> **1** 指示关联端刚好存在一个实体类型实例。 <br/> **0 .0** 表示在关联端存在零个或一个实体类型实例。 <br/> **\*** 指示在关联端存在零个、一个或多个实体类型实例。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **结束** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

#### <a name="example"></a>示例

下面的示例演示了一个定义**CustomerOrders**关联的**association**元素。 关联的每个**端点**的**多重性**值指示可以将多个**订单**与一个**客户**关联，但只能有一个**客户**与一个**订单**相关联。 此外， **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>End 元素作为 AssociationSet 元素的子元素

**End**元素指定关联集的一端。 **AssociationSet**元素必须包含两个**结束**元素。 **End**元素中包含的信息用于将关联集映射到数据源。

**End**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   批注元素（零个或多个元素）

> [!NOTE]
> Annotation 元素必须出现在所有其他子元素之后。 仅在 CSDL v2 和更高版本中允许使用批注元素。

 

#### <a name="applicable-attributes"></a>适用的属性

下表描述了当 **结束** 元素为 **AssociationSet** 元素的子元素时，可应用于该元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | 是         | 定义父**AssociationSet**元素一端的**EntitySet**元素的名称。 必须在与父**AssociationSet**元素相同的实体容器中定义**EntitySet**元素。 |
| **角色**       | 否          | 关联集端的名称。 如果未使用 **Role** 属性，则关联集端的名称将为实体集的名称。                                                                   |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **结束** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

#### <a name="example"></a>示例

下面的示例演示具有两个**AssociationSet**元素的**EntityContainer**元素，每个元素都有两个**End**元素：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a>EntityContainer 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **EntityContainer** 元素是实体集、关联集和函数导入的逻辑容器。 概念模型实体容器通过 EntityContainerMapping 元素映射到存储模型实体容器。 存储模型实体容器描述数据库的结构：实体集描述表、关联集描述外键约束、函数导入描述数据库中的存储过程。

**EntityContainer**元素可以有零个或一个文档元素。 如果存在 **文档** 元素，则它必须位于所有 **EntitySet**、 **AssociationSet**和 **FunctionImport** 元素之前。

**EntityContainer**元素可以具有零个或多个下列子元素 (按列出的顺序) ：

-   EntitySet
-   AssociationSet
-   FunctionImport
-   批注元素

可以扩展 **EntityContainer** 元素，使其包含同一命名空间中的另一个 **entitycontainer** 的内容。 若要包含另一个 **entitycontainer**的内容，请在引用 **entitycontainer** 元素中，将 " **扩展** " 属性的值设置为要包括的 **EntityContainer** 元素的名称。 包含的 **entitycontainer** 元素的所有子元素将被视为引用 **entitycontainer** 元素的子元素。

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **Using** 元素的特性。

| 属性名称 | 是否必需 | 值                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名称**       | 是         | 实体容器的名称。                               |
| **延续**    | 否          | 同一命名空间中另一实体容器的名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **EntityContainer** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个 **EntityContainer** 元素，该元素定义三个实体集和两个关联集。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a>EntitySet 元素 (CSDL)

概念架构定义语言中的 **EntitySet** 元素是某个实体类型的实例和从该实体类型派生的任何类型的实例的逻辑容器。 实体类型与实体集之间的关系类似于关系数据库中行与表之间的关系。 实体类型与行类似，它定义一组相关数据；而实体集与表类似，它包含该定义的实例。 实体集为对实体类型实例分组提供了一个构造，以便能够将它们映射到数据源中的相关数据结构。  

可以为特定实体类型定义多个实体集。

> [!NOTE]
> EF 设计器不支持每个类型包含多个实体集的概念模型。

 

**EntitySet**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation 元素（允许零个或一个元素）
-   Annotation 元素（允许零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **EntitySet** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名称**       | 是         | 实体集的名称。                                                              |
| **EntityType** | 是         | 实体集包含其实例的实体类型的完全限定名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **EntitySet** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示具有三个**EntitySet**元素的**EntityContainer**元素：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

每个类型可以定义多个实体集 (MEST)。 下面的示例定义了一个实体容器，其中包含 **书籍** 实体类型的两个实体集：

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a>EntityType 元素 (CSDL)

**EntityType**元素表示概念模型中的顶级概念（例如客户或订单）的结构。 实体类型是应用程序中实体类型实例的模板。 每个模板都包含以下信息：

-   唯一名称。 （必选。）
-   由一个或多个属性定义的实体键。 （必选。）
-   用于包含数据的属性。 （可选。）
-   导航属性，用于从关联的一端导航至另一端。 （可选。）

在应用程序中，实体类型的实例表示一个特定对象（例如特定客户或订单）。 实体类型的每个实例在实体集中都必须具有唯一的实体键。

只有两个实体类型实例的类型相同且其实体键的值也相同时，才认为它们是相等的。

**EntityType**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   Key（零个或一个元素）
-   Property（零个或多个元素）
-   NavigationProperty（零个或多个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **EntityType** 元素的特性。

| 属性名称                                                                                                                                  | 是否必需 | 值                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名称**                                                                                                                                        | 是         | 实体类型的名称。                                                                     |
| **BaseType**                                                                                                                                    | 否          | 作为正在定义的实体类型的基类型的另一个实体类型。  |
| **抽象**                                                                                                                                    | 否          | **True** 或 **False**，具体取决于实体类型是否为抽象类型。                 |
| **OpenType**                                                                                                                                    | 否          | **True** 或 **False** ，具体取决于实体类型是否为开放式实体类型。 <br/> [!NOTE] |
| > **OpenType** 特性仅适用于在与 ADO.NET Data Services 一起使用的概念模型中定义的实体类型。 |             |                                                                                                  |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **EntityType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个具有三个**属性**元素和两个**NavigationProperty**元素的**EntityType**元素：

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a>EnumType 元素 (CSDL) 

**EnumType**元素表示一个枚举类型。

**EnumType**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   成员 (零个或多个元素) 
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **EnumType** 元素的特性。

| 属性名称     | 是否必需 | 值                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**           | 是         | 实体类型的名称。                                                                                                                                                                  |
| **IsFlags**        | 否          | **True** 或 **False**，具体取决于枚举类型是否可用作一组标志。 默认值为**False。**                                                                     |
| **UnderlyingType** | 否          | **Edm**、 **edm**、 **edm**、 **edm** 或 **edm** ，定义类型的值的范围的。 枚举元素的默认基础类型为 **Edm**。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **EnumType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示具有三个**成员**元素的**EnumType**元素：

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function 元素 (CSDL)

概念架构定义语言中 (CSDL) 的 **函数** 元素用于在概念模型中定义或声明函数。 函数通过使用 DefiningExpression 元素来定义。  

**函数**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   Parameter（零个或多个元素）
-   DefiningExpression（零个或一个元素）
-   ReturnType (函数)  (零个或一个元素) 
-   批注元素（零个或多个元素）

函数的返回类型必须与 **returntype** (函数) 元素或 **returntype** 属性一起指定 (参见下面) ，但不能同时指定两者。 可能的返回类型为任何 EdmSimpleType、实体类型、复杂类型、行类型或引用类型（或这些类型之一的集合）。

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **Function** 元素的特性。

| 属性名称 | 是否必需 | 值                              |
|:---------------|:------------|:-----------------------------------|
| **名称**       | 是         | 函数名。          |
| **ReturnType** | 否          | 函数返回的类型。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Function** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例使用 **function** 元素来定义一个函数，该函数返回一个指导员后的年数。

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **FunctionImport** 元素表示在数据源中定义但可通过概念模型用于对象的函数。 例如，存储模型中的 Function 元素可用于表示数据库中的存储过程。 概念模型中的 **FunctionImport** 元素表示实体框架应用程序中的相应函数并通过使用 FunctionImportMapping 元素映射到存储模型函数。 在应用程序中调用函数时，会在数据库中执行相应的存储过程。

**FunctionImport**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（允许零个或一个元素）
-   Parameter（允许零个或多个元素）
-   Annotation 元素（允许零个或多个元素）
-   ReturnType (FunctionImport)  (允许零个或多个元素) 

应为函数接受的每个参数定义一个 **参数** 元素。

函数的返回类型必须与 **returntype** (FunctionImport) 元素或 **returntype** 属性一起指定 (参见下面) ，但不能同时指定两者。 返回类型值必须是 EdmSimpleType、EntityType 或 ComplexType 的集合。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **FunctionImport** 元素的特性。

| 属性名称   | 是否必需 | 值                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**         | 是         | 导入的函数的名称。                                                                                                                                                                    |
| **ReturnType**   | 否          | 函数返回的类型。 如果函数未返回值，则不要使用此属性。 否则，该值必须是 ComplexType、EntityType 或 EDMSimpleType 的集合。        |
| **EntitySet**    | 否          | 如果函数返回实体类型的集合，则 **EntitySet** 的值必须是该集合所属的实体集。 否则，不得使用 **EntitySet** 属性。 |
| **IsComposable** | 否          | 如果将此值设置为 true，则函数可组合 (表值函数) 并可在 LINQ 查询中使用。默认值为 **false**。                                                           |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **FunctionImport** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个 **FunctionImport** 元素，该元素接受一个参数并返回实体类型的集合：

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>Key 元素 (CSDL)

**Key**元素是 EntityType 元素的子元素，用于*定义实体类型 (确定*标识) 的实体类型的属性或一组属性。 构成实体键的属性是在设计时选择的。 实体键属性的值必须在运行时唯一标识实体集中的实体类型实例。 在选择构成实体键的属性时应确保实例在实体集中的唯一性。 **Key**元素通过引用实体类型的一个或多个属性来定义实体键。

**Key**元素可以具有以下子元素：

-   PropertyRef（一个或多个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **该键** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例定义了一个名为 **Book**的实体类型。 实体键通过引用实体类型的 **ISBN** 属性来定义。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

**Isbn**属性是实体键的一个不错选择，因为国际标准图书号码 (ISBN) 唯一标识书。

下面的示例演示了一个实体类型 (**作者**) ，其中包含一个由两个属性、 **名称** 和 **地址**组成的实体键。

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

使用实体键的 **名称** 和 **地址** 是合理的选择，因为同一名称的两个作者不会居住在同一地址。 但是，针对实体键的这种选择并不能绝对确保实体键在实体集中的唯一性。 在这种情况下，建议添加可用于唯一标识作者的属性，例如 **AuthorId**：。

 

## <a name="member-element-csdl"></a> (CSDL) 成员元素

**Member**元素是 EnumType 元素的子元素，用于定义枚举类型的成员。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **FunctionImport** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**       | 是         | 成员名。                                                                                                                                                                  |
| **值**      | 否          | 成员的值。 默认情况下，第一个成员的值为0，并且每个连续枚举数的值将增加1。 可能存在多个具有相同值的成员。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **FunctionImport** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示具有三个**成员**元素的**EnumType**元素：

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty 元素 (CSDL)

**NavigationProperty**元素定义导航属性，该属性提供对关联的另一端的引用。 与使用 Property 元素定义的属性不同，导航属性不定义数据的形状和特征。 它们提供了一种在两个实体类型之间导航关联的方法。

注意，对于关联两端的两种实体类型，导航属性都是可选的。 如果您对于位于关联一端的实体类型定义一个导航属性，则不需要对于位于关联另一端的实体类型定义导航属性。

导航属性返回的数据类型是由其远程关联端的重数确定的。 例如，假设有一个导航属性 **OrdersNavProp**，该属性存在于 **客户** 实体类型上，并在 **customer** 和 **Order**之间导航一对多关联。 因为导航属性的远程关联端的重数)  (多 \* ，所以其数据类型是) **顺序** 的集合 (。 同样，如果 "**订单**" 实体类型上存在导航属性**CustomerNavProp**，则其数据类型将为**Customer** ，因为远程端的重数为 1)  (1。

**NavigationProperty**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **NavigationProperty** 元素的特性。

| 属性名称   | 是否必需 | 值                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**         | 是         | 导航属性的名称。                                                                                                                                                                                                             |
| **Relationship** | 是         | 处于模型的作用域中的关联的名称。                                                                                                                                                                                |
| **ToRole**       | 是         | 导航在此结束的关联端。 **ToRole**属性的值必须与在 AssociationEnd 元素) 中定义的某个关联端 (定义的某个**角色**属性的值相同。       |
| **FromRole**     | 是         | 导航从其开始的关联端。 **FromRole**属性的值必须与在 AssociationEnd 元素) 中定义的某个关联端 (定义的某个**角色**属性的值相同。 |

 

> [!NOTE]
>  (自定义 XML 特性) 任意数量的批注特性可应用于 **NavigationProperty** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例定义了一个实体类型 (**书籍**) ，其中包含两个导航属性 (**PublishedBy** 和 **WrittenBy**) ：

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a>OnDelete 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **OnDelete** 元素定义与关联关联的行为。 如果在关联的一端将 **Action** 特性设置为 **Cascade** ，则在删除第一条上的实体类型时，将删除关联的另一端上的相关实体类型。 如果两个实体类型之间的关联是主键到主键关系，则当删除关联的另一端上的主体对象时，将删除加载的依赖对象，而不考虑 **OnDelete** 规范。  

> [!NOTE]
> **OnDelete**元素只影响应用程序的运行时行为;它不会影响数据源中的行为。 在数据源中定义的行为应与在应用程序中定义的行为相同。

 

**OnDelete**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **OnDelete** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **操作**     | 是         | **Cascade** 或 **None**。 如果在删除主体实体类型时将删除 **级联**的依赖实体类型，则为。 如果为 " **无**"，则在删除主体实体类型时不会删除从属实体类型。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **Association** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示了一个定义**CustomerOrders**关联的**association**元素。 **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter 元素 (CSDL)

概念架构定义语言中 (CSDL) 的 **参数** 元素可以是 FunctionImport 元素或 Function 元素的子元素。

### <a name="functionimport-element-application"></a>FunctionImport 元素应用程序

**参数**元素 (作为**FunctionImport**元素的子元素) 用于定义在 CSDL 中声明的函数导入的输入和输出参数。

**Parameter**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（允许零个或一个元素）
-   Annotation 元素（允许零个或多个元素）

#### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **参数** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**       | 是         | 参数的名称。                                                                                                                                                                                                      |
| 类型       | 是         | 参数类型。 值必须为 **EDMSimpleType** 或模型范围内的复杂类型。                                                                                                             |
| **模式**       | 否          | **In**、 **Out**或 **InOut** ，具体取决于参数是输入、输出还是输入/输出参数。                                                                                                                |
| **MaxLength**  | 否          | 允许的参数最长长度。                                                                                                                                                                                    |
| **精度**  | 否          | 参数的精度。                                                                                                                                                                                                 |
| **缩放**      | 否          | 参数的确定位数。                                                                                                                                                                                                     |
| SRID        | 否          | 空间系统引用标识符。 仅对空间类型的参数有效。 有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可应用于 **参数** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

#### <a name="example"></a>示例

下面的示例演示一个具有一个**参数**子元素的**FunctionImport**元素。 函数接受一个输入参数并返回实体类型的集合。

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Function 元素应用程序

**参数**元素 (作为**Function**元素的子元素) 为在概念模型中定义或声明的函数定义参数。

**Parameter**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   CollectionType（零个或一个元素）
-   ReferenceType（零个或一个元素）
-   RowType（零个或一个元素）

> [!NOTE]
> 只有一个 **CollectionType**、 **ReferenceType**或 **RowType** 元素可以是 **属性** 元素的子元素。

 

-   Annotation 元素（允许零个或多个元素）

> [!NOTE]
> Annotation 元素必须出现在所有其他子元素之后。 仅在 CSDL v2 和更高版本中允许使用批注元素。

 

#### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **参数** 元素的特性。

| 属性名称   | 是否必需 | 值                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**         | 是         | 参数的名称。                                                                                                                                                                                                      |
| **类型**         | 否          | 参数类型。 参数可以是以下任意类型（或这些类型的集合）： <br/> **EdmSimpleType** <br/> Entity Type — 实体类型 <br/> Complex Type — 复杂类型 <br/> 行类型 <br/> Reference Type — 引用类型                             |
| **可以为 Null**     | 否          | **True** (默认值) 或 **False** ，具体取决于属性是否可以具有 **null** 值。                                                                                                                          |
| **DefaultValue** | 否          | 属性的默认值。                                                                                                                                                                                              |
| **MaxLength**    | 否          | 属性值的最大长度。                                                                                                                                                                                       |
| **FixedLength**  | 否          | **True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。                                                                                                                          |
| **精度**    | 否          | 属性值的精度。                                                                                                                                                                                            |
| **缩放**        | 否          | 属性值的刻度。                                                                                                                                                                                                |
| SRID          | 否          | 空间系统引用标识符。 仅对空间类型的属性有效。 有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**      | 否          | **True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。                                                                                                                               |
| **排序规则**    | 否          | 指定要在数据源中使用的排序的字符串。                                                                                                                                                   |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可应用于 **参数** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

#### <a name="example"></a>示例

下面的示例演示一个使用一个**参数**子元素定义函数参数的**函数**元素。

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a>Principal 元素 (CSDL)

概念架构定义语言中 (CSDL) 的 **主体** 元素是 ReferentialConstraint 元素的子元素，用于定义引用约束的主体端。 **ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。 与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。 引用的实体类型称为约束的 *主体端* 。 引用主体端的实体类型称为约束的 *依赖端* 。 **PropertyRef** 元素用于指定依赖端引用的键。

**主体**元素可以具有以下子元素 (按列出的顺序) ：

-   PropertyRef（一个或多个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **主体** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **角色**       | 是         | 关联的主体端的实体类型的名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **主体** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例显示了 **ReferentialConstraint** 元素，它是 **PublishedBy** 关联定义的一部分。 **发行者**实体类型的**Id**属性构成了引用约束的主体端。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a>Property 元素 (CSDL)

概念架构定义语言中 (CSDL) 的 **属性** 元素可以是 EntityType 元素、ComplexType 元素或 RowType 元素的子元素。

### <a name="entitytype-and-complextype-element-applications"></a>EntityType 和 ComplexType 元素应用程序

**属性** 元素 (为 **EntityType** 或 **ComplexType** 元素的子元素) 定义实体类型实例或复杂类型实例将包含的数据的形状和特征。 概念模型中的属性类似于为类定义的属性。 正如类的属性定义类的形状和携带有关对象的信息一样，概念模型中的属性也定义实体类型的形状和携带有关实体类型实例的信息。

**属性**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation 元素（允许零个或一个元素）
-   Annotation 元素（允许零个或多个元素）

以下方面可应用于 **属性** 元素：可以 **为 null**、 **默认**值、 **MaxLength**、 **FixedLength**、 **精度**、 **小数位数**、 **Unicode**、 **排序规则**、 **ConcurrencyMode**。 方面是一些 XML 特性，它们提供有关如何在数据存储区中存储属性值的信息。

> [!NOTE]
> Facet 只能应用于 **EDMSimpleType**类型的属性。

 

#### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **属性** 元素的特性。

| 属性名称                                                         | 是否必需 | 值                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**                                                               | 是         | 属性的名称。                                                                                                                                                                                                       |
| 类型                                                               | 是         | 属性值的类型。 属性值类型必须为 **EDMSimpleType** 或模型范围内的复杂类型（由完全限定名称指示）。                                                 |
| **可以为 Null**                                                           | 否          | **True**（默认值）或 <strong>False</strong>，取决于属性是否可以具有 null 值。 <br/> [!NOTE]                                                                                                   |
| 在 CSDL v1 中，复杂类型属性的 > 必须具有 `Nullable="False"` 。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | 否          | 属性的默认值。                                                                                                                                                                                              |
| **MaxLength**                                                          | 否          | 属性值的最大长度。                                                                                                                                                                                       |
| **FixedLength**                                                        | 否          | **True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。                                                                                                                          |
| **精度**                                                          | 否          | 属性值的精度。                                                                                                                                                                                            |
| **缩放**                                                              | 否          | 属性值的刻度。                                                                                                                                                                                                |
| SRID                                                                | 否          | 空间系统引用标识符。 仅对空间类型的属性有效。 有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**                                                            | 否          | **True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。                                                                                                                               |
| **排序规则**                                                          | 否          | 指定要在数据源中使用的排序的字符串。                                                                                                                                                   |
| **ConcurrencyMode**                                                    | 否          | **无**（默认值）或**固定**。 如果此值设置为**固定**，会在乐观并发检查中使用属性值。                                                                                  |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Property** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

#### <a name="example"></a>示例

下面的示例演示一个具有三个**属性**元素的**EntityType**元素：

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

下面的示例演示一个包含五个**属性**元素的**ComplexType**元素：

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>RowType 元素应用

**属性** 元素 (作为 **RowType** 元素的子元素) 定义可传递到模型定义函数或从模型定义函数返回的数据的形状和特征。  

**Property**元素可以具有以下子元素之一：

-   CollectionType
-   ReferenceType
-   RowType

**Property**元素可以有任意数量的子批注元素。

> [!NOTE]
> 仅在 CSDL v2 和更高版本中允许使用批注元素。

 

#### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **属性** 元素的特性。

| 属性名称                                                     | 是否必需 | 值                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**                                                           | 是         | 属性的名称。                                                                                                                                                                                                       |
| 类型                                                           | 是         | 属性值的类型。                                                                                                                                                                                                 |
| **可以为 Null**                                                       | 否          | **True**（默认值）或 **False**，取决于属性是否可以具有 null 值。 <br/> [!NOTE]                                                                                                                |
| CSDL v1 中的 >，复杂类型属性必须具有 `Nullable="False"` 。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | 否          | 属性的默认值。                                                                                                                                                                                              |
| **MaxLength**                                                      | 否          | 属性值的最大长度。                                                                                                                                                                                       |
| **FixedLength**                                                    | 否          | **True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。                                                                                                                          |
| **精度**                                                      | 否          | 属性值的精度。                                                                                                                                                                                            |
| **缩放**                                                          | 否          | 属性值的刻度。                                                                                                                                                                                                |
| SRID                                                            | 否          | 空间系统引用标识符。 仅对空间类型的属性有效。 有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**                                                        | 否          | **True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。                                                                                                                               |
| **排序规则**                                                      | 否          | 指定要在数据源中使用的排序的字符串。                                                                                                                                                   |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Property** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

#### <a name="example"></a>示例

下面的示例演示了用于定义模型定义函数的返回类型的形状的 **属性** 元素。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a>PropertyRef 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **PropertyRef** 元素引用实体类型的属性，以指示该属性将执行以下角色之一：

-   实体键的一部分（实体类型的用于确定标识的一个或一组属性）。 一个或多个 **PropertyRef** 元素可用于定义实体键。
-   引用约束的依赖端或主体端。

**PropertyRef**元素只能有 (零个或多个) 作为子元素的批注元素。

> [!NOTE]
> 仅在 CSDL v2 和更高版本中允许使用批注元素。

 

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **PropertyRef** 元素的特性。

| 属性名称 | 是否必需 | 值                                |
|:---------------|:------------|:-------------------------------------|
| **名称**       | 是         | 所引用属性的名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **PropertyRef** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例定义了 (**Book**) 的实体类型。 实体键通过引用实体类型的 **ISBN** 属性来定义。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

在下一个示例中，使用两个 **PropertyRef** 元素来指示 (**Id** 和 **PublisherId** 的两个属性) 是引用约束的主体端和依赖端。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a>ReferenceType 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **ReferenceType** 元素指定对实体类型的引用。 **ReferenceType**元素可以是以下元素的子元素：

-   ReturnType (函数) 
-   参数
-   CollectionType

在定义函数的参数或返回类型时，将使用 **ReferenceType** 元素。

**ReferenceType**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **ReferenceType** 元素的特性。

| 属性名称 | 是否必需 | 值                                         |
|:---------------|:------------|:----------------------------------------------|
| 类型       | 是         | 所引用的实体类型的名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **ReferenceType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例显示了**ReferenceType**元素，该元素在接受对**Person**实体类型的引用的模型定义函数中用作**Parameter**元素的子元素：

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

下面的示例显示了**ReferenceType**元素，该元素用于在模型定义的函数中返回对**Person**实体类型的引用的**ReturnType** (函数) 元素：

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a>ReferentialConstraint 元素 (CSDL)

 (CSDL) 概念架构定义语言中的 **ReferentialConstraint** 元素定义的功能与关系数据库中的引用完整性约束类似。 与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。 引用的实体类型称为约束的 *主体端* 。 引用主体端的实体类型称为约束的 *依赖端* 。

如果一个实体类型上公开的外键引用另一个实体类型上的属性，则 **ReferentialConstraint** 元素将定义这两个实体类型之间的关联。 由于 **ReferentialConstraint** 元素提供有关两个实体类型之间的关系的信息，因此在映射规范语言 (MSL) 中不需要对应的 AssociationSetMapping 元素。 没有公开外键的两个实体类型之间的关联必须具有相应的 **AssociationSetMapping** 元素，才能将关联信息映射到数据源。

如果在实体类型上没有公开外键，则 **ReferentialConstraint** 元素只能定义实体类型和其他实体类型之间的主键到主键约束。

**ReferentialConstraint**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   Principal（恰好一个元素）
-   Dependent（恰好一个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

**ReferentialConstraint**元素可以具有任意数量的批注属性 (自定义 XML 特性) 。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示了在**PublishedBy**关联的定义中使用的**ReferentialConstraint**元素。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a>ReturnType (函数) 元素 (CSDL) 

使用概念架构定义语言中的 **ReturnType** (函数) 元素 (CSDL) 指定函数元素中定义的函数的返回类型。 还可以使用 **ReturnType** 特性指定函数返回类型。

返回类型可以是任何 **EdmSimpleType**、实体类型、复杂类型、行类型、引用类型或这些类型之一的集合。

函数的返回类型可使用**ReturnType** (函数) 元素的**type**特性指定，或使用以下子元素之一指定：

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> 如果指定的函数返回类型同时具有**ReturnType** (函数的**type**特性) 元素和其中一个子元素，则模型将不会进行验证。

 

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **ReturnType** (函数) 元素的特性。

| 属性名称 | 是否必需 | 值                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | 否          | 函数返回的类型。 |

 

> [!NOTE]
>  (自定义 XML 特性) 任意数量的批注特性可应用于 **ReturnType** (函数) 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例使用 **function** 元素来定义一个函数，该函数返回书籍已打印的年数。 请注意，返回类型由**ReturnType** (函数) 元素的**type**属性指定。

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>ReturnType (FunctionImport) 元素 (CSDL) 

使用概念架构定义语言中的 **ReturnType** (FunctionImport) 元素 (CSDL) 指定在 FunctionImport 元素中定义的函数的返回类型。 还可以使用 **ReturnType** 特性指定函数返回类型。

返回类型可以是实体类型、复杂类型或 **EdmSimpleType**的任何集合，

函数的返回类型是使用**ReturnType** (FunctionImport) 元素的**type**属性指定的。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **ReturnType** (FunctionImport) 元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **类型**       | 否          | 函数返回的类型。 该值必须是 ComplexType、EntityType 或 EDMSimpleType 的集合。                                                                                      |
| **EntitySet**  | 否          | 如果函数返回实体类型的集合，则 **EntitySet** 的值必须是该集合所属的实体集。 否则，不得使用 **EntitySet** 属性。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **ReturnType** (FunctionImport) 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例使用返回书籍和出版商的 **FunctionImport** 。 请注意，函数将返回两个结果集，因此指定了两个 **ReturnType** (FunctionImport) 元素。

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>RowType 元素 (CSDL)

概念架构定义语言 (CSDL) 中的 **RowType** 元素将未命名的结构定义为概念模型中定义的函数的参数或返回类型。

**RowType**元素可以是以下元素的子元素：

-   CollectionType
-   参数
-   ReturnType (函数) 

**RowType**元素可以具有以下子元素 (按列出的顺序) ：

-   Property（一个或多个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **RowType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个模型定义函数，该函数使用 **CollectionType** 元素来指定该函数返回 (在 **RowType** 元素) 中指定的行的集合。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a>Schema 元素 (CSDL)

**Schema**元素是概念模型定义的根元素。 它包括构成概念模型的对象、函数和容器的定义。

**Schema**元素可包含零个或多个下列子元素：

-   使用
-   EntityContainer
-   EntityType
-   EnumType
-   关联
-   ComplexType
-   函数

一个 **Schema** 元素可包含零个或一个批注元素。

> [!NOTE]
> 仅在 CSDL v2 和更高版本中允许 **Function** 元素和 annotation 元素。

 

**Schema**元素使用**namespace**特性为概念模型中的实体类型、复杂类型和关联对象定义命名空间。 在命名空间内，任何两个对象都不能同名。 命名空间可以跨多个 **架构** 元素和多个 csdl 文件。

概念模型命名空间与 **Schema** 元素的 XML 命名空间不同。 **命名空间**特性定义的概念模型命名空间 () 是实体类型、复杂类型和关联类型的逻辑容器。 **Schema**元素的**xmlns**特性) 指示 (XML 命名空间是**schema**元素的子元素和属性的默认命名空间。 格式为 (的 XML 命名空间 https://schemas.microsoft.com/ado/YYYY/MM/edm 分别表示年份和月份，) 是为 CSDL 保留的。 自定义元素和特性不能位于具有此格式的命名空间中。

### <a name="applicable-attributes"></a>适用的属性

下表描述了可对 **Schema** 元素应用的属性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | 是         | 概念模型的命名空间。 **命名空间**属性的值用于构成类型的完全限定名称。 例如，如果名为*Customer*的**entitytype**位于 Simple 命名空间中，则**entitytype**的完全限定名称为 simpleexamplemodel.customer。 <br/> 以下字符串不能用作 **Namespace** 特性的值： **System**、 **暂时性**或 **Edm**。 **命名空间**属性的值不能与 SSDL 架构元素中**namespace**属性的值相同。 |
| **Alias**      | 否          | 用于取代命名空间名称的标识符。 例如，如果名为*Customer*的**EntityType**位于 Simple 命名空间，并且**Alias**属性的值为*Model*，则可以使用 model 作为 EntityType 的完全限定名称 **。**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Schema** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个 **架构** 元素，该元素包含一个 **EntityContainer** 元素、两个 **EntityType** 元素和一个 **Association** 元素。

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a>TypeRef 元素 (CSDL)

概念架构定义语言中的 **TypeRef** 元素 (CSDL) 提供对现有命名类型的引用。 **TypeRef**元素可以是 CollectionType 元素的子元素，用于指定函数具有集合作为参数或返回类型。

**TypeRef**元素可以具有以下子元素 (按列出的顺序) ：

-   Documentation（零个或一个元素）
-   批注元素（零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于 **TypeRef** 元素的特性。 请注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **精度**、 **小数位数**、 **Unicode**和 **排序规则** 特性仅适用于 **edmsimpletype**。

| 属性名称                                                     | 是否必需 | 值                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **类型**                                                           | 否          | 所引用的类型的名称。                                                                                                                                                                                          |
| **可以为 Null**                                                       | 否          | **True**（默认值）或 **False**，取决于属性是否可以具有 null 值。 <br/> [!NOTE]                                                                                                                |
| CSDL v1 中的 >，复杂类型属性必须具有 `Nullable="False"` 。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | 否          | 属性的默认值。                                                                                                                                                                                              |
| **MaxLength**                                                      | 否          | 属性值的最大长度。                                                                                                                                                                                       |
| **FixedLength**                                                    | 否          | **True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。                                                                                                                          |
| **精度**                                                      | 否          | 属性值的精度。                                                                                                                                                                                            |
| **缩放**                                                          | 否          | 属性值的刻度。                                                                                                                                                                                                |
| SRID                                                            | 否          | 空间系统引用标识符。 仅对空间类型的属性有效。 有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **Unicode**                                                        | 否          | **True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。                                                                                                                               |
| **排序规则**                                                      | 否          | 指定要在数据源中使用的排序的字符串。                                                                                                                                                   |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **CollectionType** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示一个模型定义函数，该函数使用 **TypeRef** 元素 (作为 **CollectionType** 元素的子元素，) 指定该函数接受 **部门** 实体类型的集合。

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a>Using 元素 (CSDL)

概念性架构定义语言中的 **Using** 元素 (CSDL) 导入在其他命名空间中存在的概念模型的内容。 通过设置 **Namespace** 属性的值，您可以引用在其他概念模型中定义的实体类型、复杂类型和关联类型。 多个 **Using** 元素可以是 **Schema** 元素的子元素。

> [!NOTE]
> CSDL 中的 **using** 元素的作用与编程语言中的 **using** 语句完全相同。 通过使用编程语言中的 **using** 语句导入命名空间，不会影响原始命名空间中的对象。 在 CSDL 中，导入的命名空间可以包含从原始命名空间中的实体类型导出的实体类型。 这可能影响在原始命名空间中声明的实体集。

 

**Using**元素可以具有以下子元素：

-   Documentation（允许零个或一个元素）
-   Annotation 元素（允许零个或多个元素）

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于 **Using** 元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | 是         | 导入的命名空间的名称。                                                                                                                                                |
| **Alias**      | 是         | 用于取代命名空间名称的标识符。 尽管此特性是必需的，但并不要求使用它来取代命名空间以限定对象名称。 |

 

> [!NOTE]
> 可以将任意数量的批注属性 (自定义 XML 特性) 可应用于 **Using** 元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

 

### <a name="example"></a>示例

下面的示例演示用于导入在其他位置定义的命名空间的 **Using** 元素。 请注意，显示的 **架构** 元素的命名空间为 `BooksModel` 。 `Address`EntityType 上的属性 `Publisher` **EntityType**是在命名空间中定义的复杂类型 `ExtendedBooksModel` (**使用 Using**元素) 导入。

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a>Annotation 特性 (CSDL)

以概念架构定义语言 (CSDL) 表示的批注特性是概念模型中的自定义 XML 特性。 除了具有有效的 XML 结构之外，还必须满足批注特性的以下各项条件：

-   批注特性不能位于为 CSDL 保留的任何 XML 命名空间中。
-   可以将多个批注特性应用于给定的 CSDL 元素。
-   任何两个批注特性的完全限定名称都不能相同。

可以使用批注特性提供有关概念模型中元素的额外元数据。 可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。

### <a name="example"></a>示例

下面的示例演示一个具有批注特性 (**CustomAttribute**) 的**EntityType**元素。 本示例还演示了一个应用于实体类型元素的批注元素。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

下面的代码检索批注特性中的元数据并将其写入控制台：

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

上面的代码假定 `School.csdl` 文件位于项目的输出目录中，且您已将下面的 `Imports` 和 `Using` 语句添加到项目中：

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>Annotation 元素 (CSDL)

以概念架构定义语言 (CSDL) 表示的批注元素是概念模型中的自定义 XML 元素。 除了具有有效的 XML 结构之外，还必须满足批注元素的以下各项条件：

-   批注元素不能位于为 CSDL 保留的任何 XML 命名空间中。
-   多个批注元素可能是某个给定 CSDL 元素的子元素。
-   任何两个批注元素的完全限定名称都不能相同。
-   批注元素必须出现在给定 CSDL 元素的所有其他子元素之后。

可以使用批注元素提供有关概念模型中元素的额外元数据。 从 .NET Framework 版本4开始，可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。

### <a name="example"></a>示例

下面的示例演示一个 **EntityType** 元素，该元素具有一个 (**CustomElement**) 的批注元素。 本示例还演示了一个应用于实体类型元素的批注特性。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

下面的代码检索批注元素中的元数据并将其写入控制台：

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

上面的代码假定 School.csdl 文件位于项目的输出目录中并且您已将下面的 `Imports` 和 `Using` 语句添加到项目中：

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>概念模型类型 (CSDL)

概念架构定义语言 (CSDL) 支持一组抽象基元数据类型（称为 **edmsimpletype**），用于定义概念模型中的属性。 **Edmsimpletype** 是存储或承载环境中支持的基元数据类型的代理。

下表列出了 CSDL 支持的基元数据类型。 该表还列出了可应用于每个 **EDMSimpleType**的分面。

| EDMSimpleType                    | 说明                                                | 适用的方面                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.Binary**                   | 包含二进制数据。                                      | MaxLength、FixedLength、Nullable、Default                                |
| **Edm.Boolean**                  | 包含值 **true** 或 **false**。                  | Nullable、Default                                                        |
| **Edm.Byte**                     | 包含一个无符号的 8 位整数值。                  | Precision、Nullable、Default                                             |
| **Edm.DateTime**                 | 表示日期和时间。                                | Precision、Nullable、Default                                             |
| **Edm.DateTimeOffset**           | 包含以相对于 GMT 的偏移量（以分钟为单位）表示的日期和时间。 | Precision、Nullable、Default                                             |
| **Edm.Decimal**                  | 包含一个具有固定精度和小数位数的数值。   | Precision、Nullable、Default                                             |
| **Edm.Double**                   | 包含具有15位精度的浮点数   | Precision、Nullable、Default                                             |
| **Edm**                    | 包含一个具有 7 位精度的浮点数。   | Precision、Nullable、Default                                             |
| **Edm.Guid**                     | 包含一个 16 字节的唯一标识符。                      | Precision、Nullable、Default                                             |
| **Edm.Int16**                    | 包含一个带符号的 16 位整数值。                    | Precision、Nullable、Default                                             |
| **Edm.Int32**                    | 包含一个带符号的 32 位整数值。                    | Precision、Nullable、Default                                             |
| **Edm.Int64**                    | 包含一个带符号的 64 位整数值。                    | Precision、Nullable、Default                                             |
| **Edm.SByte**                    | 包含一个带符号的 8 位整数值。                     | Precision、Nullable、Default                                             |
| **Edm.String**                   | 包含字符数据。                                   | Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default |
| **Edm.Time**                     | 包含当天的时间。                                    | Precision、Nullable、Default                                             |
| **Edm**                |                                                            | Nullable、Default、SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyLineString**      |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyPolygon**         |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyMultiPoint**      |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyMultiLineString** |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyMultiPolygon**    |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyCollection**      |                                                            | Nullable、Default、SRID                                                  |
| **Edm**                 |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryPoint**            |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryLineString**       |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryPolygon**          |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryMultiPoint**       |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryMultiLineString**  |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryMultiPolygon**     |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeometryCollection**       |                                                            | Nullable、Default、SRID                                                  |

## <a name="facets-csdl"></a>方面 (CSDL)

以概念架构定义语言 (CSDL) 表示的方面表示对于实体类型和复杂类型的属性的约束。 方面作为 XML 特性出现在以下 CSDL 元素上：

-   properties
-   TypeRef
-   参数

下表描述了 CSDL 中支持的方面。 所有方面都是可选的。 从概念模型生成数据库时，实体框架将使用下面列出的一些方面。

> [!NOTE]
> 有关概念模型中的数据类型的信息，请参阅概念模型类型 (CSDL) 。

| 方面               | 说明                                                                                                                                                                                                                                                   | 适用于                                                                                                                                                                                                                                                                                                                                                                           | 用于生成数据库 | 由运行时使用 |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **排序规则**       | 指定在对属性值执行比较和排序操作时要使用的排序序列。                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | 是                              | 否                  |
| **ConcurrencyMode** | 表示应使用属性的值来进行开放式并发检查。                                                                                                                                                                    | 所有 **EDMSimpleType** 属性                                                                                                                                                                                                                                                                                                                                                     | 否                               | 是                 |
| **默认**         | 如果在安装时未提供值，则指定属性的默认值。                                                                                                                                                                       | 所有 **EDMSimpleType** 属性                                                                                                                                                                                                                                                                                                                                                     | 是                              | 是                 |
| **FixedLength**     | 指定属性值的长度是否可变。                                                                                                                                                                                                  | **Edm**、 **edm**                                                                                                                                                                                                                                                                                                                                                       | 是                              | 否                  |
| **MaxLength**       | 指定属性值的最大长度。                                                                                                                                                                                                           | **Edm**、 **edm**                                                                                                                                                                                                                                                                                                                                                       | 是                              | 否                  |
| **可以为 Null**        | 指定属性是否可以具有 **null** 值。                                                                                                                                                                                                     | 所有 **EDMSimpleType** 属性                                                                                                                                                                                                                                                                                                                                                     | 是                              | 是                 |
| **精度**       | 对于 **Decimal**类型的属性，指定属性值可以具有的位数。 对于类型为 **Time**、 **DateTime**和 **DateTimeOffset**的属性，指定属性值的秒的小数部分的位数。 | **Edm. DateTime**、 **edm**、 **edm**、 **edm 和 Time**                                                                                                                                                                                                                                                                                                              | 是                              | 否                  |
| **缩放**           | 指定属性值小数点右侧的位数。                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | 是                              | 否                  |
| SRID             | 指定空间系统引用系统 ID。 有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。                                                              | **Edm、GeographyPoint、GeographyLineString、GeographyPolygon、GeographyMultiPoint、、、、、edm、GeographyMultiLineString、GeographyMultiPolygon、Edm、GeographyCollection、GeometryPoint、GeometryLineString、GeometryPolygon、GeometryMultiPoint、GeometryMultiLineString、GeometryMultiPolygon、GeometryCollection、、、、** | 否                               | 是                 |
| **Unicode**         | 指示是否将属性值存储为 Unicode。                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | 是                              | 是                 |

>[!NOTE]
> 当从概念模型生成数据库时，如果属性元素位于以下命名空间中，则生成数据库向导将识别该**属性**元素的**StoreGeneratedPattern**属性值： https://schemas.microsoft.com/ado/2009/02/edm/annotation 。 特性支持的值为 **Identity** 和 **计算**。 **标识**值将生成一个数据库列，其中包含在数据库中生成的标识值。 **计算得出**的值将生成一个列，该列具有在数据库中计算的值。

### <a name="example"></a>示例

下面的示例演示了应用于实体类型的属性的方面：

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
