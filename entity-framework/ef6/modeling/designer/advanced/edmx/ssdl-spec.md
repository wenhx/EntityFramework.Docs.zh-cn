---
title: SSDL 规范-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415471"
---
# <a name="ssdl-specification"></a>SSDL 规范
存储架构定义语言 (SSDL) 是一种基于 XML 的语言，用于描述实体框架应用程序的存储模型。

在实体框架应用程序中，存储模型元数据从 ssdl 文件（使用 SSDL 编写）加载到 StoreItemCollection 的实例中，可以使用中的方法进行访问。"System.object" 类。 实体框架使用存储模型元数据将针对概念模型的查询转换为特定于存储的命令。

Entity Framework Designer （EF 设计器）在设计时将存储模型信息存储在 .edmx 文件中。 在生成时，Entity Designer 使用 .edmx 文件中的信息来创建运行时实体框架所需的 ssdl 文件。

SSDL 的版本按 XML 命名空间进行区分。

| SSDL 版本 | XML 命名空间                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL v2      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association 元素 (SSDL)

存储架构定义语言（SSDL）中的**Association**元素指定参与基础数据库中外键约束的表列。 两个必需的子 End 元素指定位于关联两端的表和各端的重数。 一个可选的 ReferentialConstraint 元素指定关联的主体端和依赖端以及参与列。 如果不存在**ReferentialConstraint**元素，则必须使用 AssociationSetMapping 元素来指定关联的列映射。

**Association**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个）
-   结束（正好两个）
-   ReferentialConstraint （零个或一个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**Association**元素的特性。

| 属性名称 | 是否必需 | 值                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **名称**       | 是         | 基础数据库中相应外键约束的名称。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**Association**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个**Association**元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列：

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a>AssociationSet 元素 (SSDL)

存储架构定义语言（SSDL）中的**AssociationSet**元素表示基础数据库中两个表之间的外键约束。 参与外键约束的表列在 Association 元素中指定。 与给定**associationset**元素对应的**association**元素是在**AssociationSet**元素的**association**特性中指定的。

SSDL 关联集通过 AssociationSetMapping 元素映射到 CSDL 关联集。 但是，如果使用 ReferentialConstraint 元素定义给定 CSDL 关联集的 CSDL 关联，则不需要相应的**AssociationSetMapping**元素。 在这种情况下，如果存在**AssociationSetMapping**元素，该元素定义的映射将被**ReferentialConstraint**元素重写。

**AssociationSet**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个）
-   End（零个或两个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**AssociationSet**元素的特性。

| 属性名称  | 是否必需 | 值                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **名称**        | 是         | 关联集表示的外键约束的名称。                          |
| **关联** | 是         | 定义参与外键约束的列的关联的名称。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**AssociationSet**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示了一个**AssociationSet**元素，该元素表示基础数据库中的 `FK_CustomerOrders` foreign key 约束：

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>CollectionType 元素（SSDL）

存储架构定义语言（SSDL）中的**CollectionType**元素指定函数的返回类型是集合。 **CollectionType**元素是 ReturnType 元素的子元素。 集合的类型是通过使用 RowType 子元素指定的：

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**CollectionType**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个函数，该函数使用**CollectionType**元素来指定该函数返回行的集合。

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a>CommandText 元素 (SSDL)

存储架构定义语言（SSDL）中的**CommandText**元素是 Function 元素的子元素，它允许您定义在数据库中执行的 SQL 语句。 **CommandText**元素允许添加类似于数据库中的存储过程的功能，但在存储模型中定义**CommandText**元素。

**CommandText**元素不能有子元素。 **CommandText**元素的正文必须是基础数据库的有效 SQL 语句。

无特性适用于**CommandText**元素。

### <a name="example"></a>示例

下面的示例演示具有子**CommandText**元素的**函数**元素。 通过将**UpdateProductInOrder**函数导入到概念模型中，将其作为 ObjectContext 的方法公开。  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a>DefiningQuery 元素 (SSDL)

使用存储架构定义语言（SSDL）中的**DefiningQuery**元素，可以直接在基础数据库中执行 SQL 语句。 **DefiningQuery**元素通常用于数据库视图，但该视图是在存储模型中定义的，而不是在数据库中定义的。 在**DefiningQuery**元素中定义的视图可以通过 EntitySetMapping 元素映射到概念模型中的实体类型。 这些映射是只读的。  

下面的 SSDL 语法显示了**EntitySet**的声明，后跟包含用于检索视图的查询的**DefiningQuery**元素。

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

您可以使用实体框架中的存储过程来对视图启用读写方案。 您可以使用数据源视图或实体 SQL 视图作为基表来检索数据和存储过程的更改处理。

你可以使用**DefiningQuery**元素将 Microsoft SQL Server Compact 为3.5。 尽管 SQL Server Compact 3.5 不支持存储过程，但你可以通过**DefiningQuery**元素实现类似功能。 另一个有用之处在于，可以创建存储过程以克服编程语言中使用的数据类型与数据源的数据类型的不匹配。 您可以编写一个使用一组特定参数的**DefiningQuery** ，然后使用一组不同的参数调用存储过程，例如，删除数据的存储过程。

## <a name="dependent-element-ssdl"></a>Dependent 元素 (SSDL)

存储架构定义语言（SSDL）中的**依赖**元素是 ReferentialConstraint 元素的子元素，用于定义外键约束（也称为 "引用约束"）的依赖端。 **Dependent**元素指定引用主键列（或列）的表中的一个或多个列。 **PropertyRef**元素指定要引用的列。 主体元素指定**依赖**元素中指定的列所引用的主键列。

**依赖**元素可以具有以下子元素（按所列顺序）：

-   PropertyRef（一个或多个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**依赖**元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **角色**       | 是         | 与相应的 End 元素的**Role**特性（如果使用）相同的值;否则为包含引用列的表的名称。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**依赖**元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个 Association 元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列。 **Dependent**元素将**Order**表的**CustomerId**列指定为约束的依赖端。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a>Documentation 元素 (SSDL)

存储架构定义语言（SSDL）中的**文档**元素可用于提供有关在父元素中定义的对象的信息。

**文档**元素可以具有以下子元素（按所列顺序）：

-   **摘要**：父元素的简要说明。 （零个或一个元素）
-   **LongDescription**：父元素的详细说明。 （零个或一个元素）

### <a name="applicable-attributes"></a>适用的属性

可以将任意数量的批注特性（自定义 XML 特性）应用于**文档**元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例将**文档**元素显示为 EntityType 元素的子元素。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a>End 元素 (SSDL)

以存储架构定义语言（SSDL）表示的**结束**元素指定基础数据库中外键约束一端的表和行数。 **End**元素可以是 Association 元素或 AssociationSet 元素的子元素。 在每种情况下，可能的子元素以及适用的特性都不同。

### <a name="end-element-as-a-child-of-the-association-element"></a>End 元素作为 Association 元素的子元素

**End**元素（作为**Association**元素的子元素）使用**Type**和**多重性**特性指定 foreign key 约束末尾的表和行数。 外键约束的两端定义为 SSDL 关联的一部分；SSDL 关联必须仅具有两端。

**结束**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个元素）
-   OnDelete （零个或一个元素）
-   批注元素（零个或多个元素）

#### <a name="applicable-attributes"></a>适用的属性

下表描述了当**结束**元素是**Association**元素的子元素时，可应用于该元素的特性。

| 属性名称   | 是否必需 | 值                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 类型         | 是         | 处于外键约束端的 SSDL 实体集的完全限定名。                                                                                                                                                                                                                                                                                          |
| **角色**         | 是          | 相应 ReferentialConstraint 元素（如果使用）的主体或从属元素中的**Role**特性的值。                                                                                                                                                                                                                                             |
| **多重性** | 是         | **1**、 **0、1**或 **\*** ，具体取决于外键约束末尾的行数。 <br/> **1**指示在外键约束端刚好存在一行。 <br/> **0 .0**表示外键约束端处存在零个或一行。 <br/> **\*** 指示外键约束端处存在零行、一行或多行。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**结束**元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

#### <a name="example"></a>示例

下面的示例演示了一个**Association**元素，该元素定义了**FK\_CustomerOrders**外键约束。 在每个**End**元素上指定的**多重性**值指示**Orders**表中的多个行可以与**Customers**表中的某一行相关联，但**customers**表中只有一个行可以与**Orders**表中的一行相关联。 此外， **OnDelete**元素指示如果删除**customers**表中的行，则将删除**Orders**表中引用**customers**表中的特定行的所有行。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a>End 元素作为 AssociationSet 元素的子元素

**End**元素（作为**AssociationSet**元素的子元素的子元素）指定基础数据库中外键约束一端的表。

**结束**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个）
-   批注元素（零个或多个）

#### <a name="applicable-attributes"></a>适用的属性

下表描述了当**结束**元素为**AssociationSet**元素的子元素时，可应用于该元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | 是         | 处于外键约束一端的 SSDL 实体集的名称。                                      |
| **角色**       | 是          | 在对应的 Association 元素的一个**End**元素上指定的一个**角色**特性的值。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**结束**元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

#### <a name="example"></a>示例

下面的示例演示一个具有**AssociationSet**元素的**EntityContainer**元素，其中包含两个**End**元素：

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a>EntityContainer 元素 (SSDL)

存储架构定义语言（SSDL）中的**EntityContainer**元素描述实体框架应用程序中的基础数据源的结构： ssdl 实体集（在 EntitySet 元素中定义）表示数据库中的表，ssdl 实体类型（在 EntityType 元素中定义）表示表中的行，关联集（在 AssociationSet 元素中定义）表示数据库中的外键约束。 存储模型实体容器通过 EntityContainerMapping 元素映射到概念模型实体容器。

**EntityContainer**元素可以有零个或一个文档元素。 如果存在**文档**元素，则它必须位于所有其他子元素之前。

**EntityContainer**元素可包含零个或多个下列子元素（按所列顺序）：

-   EntitySet
-   AssociationSet
-   批注元素

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**EntityContainer**元素的特性。

| 属性名称 | 是否必需 | 值                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **名称**       | 是         | 实体容器的名称。 此名称不能包含句点 (.)。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**EntityContainer**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个**EntityContainer**元素，该元素定义了两个实体集和一个关联集。 注意，实体类型和关联类型名称由概念模型命名空间名称限定。

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a>EntitySet 元素 (SSDL)

存储架构定义语言（SSDL）中的**EntitySet**元素表示基础数据库中的表或视图。 以 SSDL 表示的 EntityType 元素表示表或视图中的行。 **EntitySet**元素的**EntityType**特性指定了表示 ssdl 实体集中的行的特定 SSDL 实体类型。 CSDL 实体集和 SSDL 实体集之间的映射在 EntitySetMapping 元素中指定。

**EntitySet**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个元素）
-   DefiningQuery （零个或一个元素）
-   批注元素

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**EntitySet**元素的特性。

> [!NOTE]
> 某些属性（此处未列出）可以用**存储**别名进行限定。 在更新模型时，模型更新向导会使用这些特性。

| 属性名称 | 是否必需 | 值                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名称**       | 是         | 实体集的名称。                                                              |
| **EntityType** | 是         | 实体集包含其实例的实体类型的完全限定名称。 |
| **架构**     | 是          | 数据库架构。                                                                     |
| **表**      | 是          | 数据库表。                                                                      |

> [!NOTE]
> 可以向**EntitySet**元素应用任意数量的批注特性（自定义 XML 特性）。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个具有两个**EntitySet**元素和一个**AssociationSet**元素的**EntityContainer**元素：

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a>EntityType 元素 (SSDL)

存储架构定义语言（SSDL）中的**EntityType**元素表示基础数据库的表或视图中的行。 以 SSDL 表示的 EntitySet 元素表示行所在的表或视图。 **EntitySet**元素的**EntityType**特性指定了表示 ssdl 实体集中的行的特定 SSDL 实体类型。 SSDL 实体类型与 CSDL 实体类型之间的映射在 EntityTypeMapping 元素中指定。

**EntityType**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个元素）
-   Key（零个或一个元素）
-   批注元素

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**EntityType**元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**       | 是         | 实体类型的名称。 此值通常与以实体类型表示行的表的名称相同。 此值可以不包含句点 (.)。 |

> [!NOTE]
> 可以向**EntityType**元素应用任意数量的批注特性（自定义 XML 特性）。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示具有两个属性的**EntityType**元素：

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a>Function 元素 (SSDL)

存储架构定义语言（SSDL）中的**Function**元素指定基础数据库中存在的存储过程。

**函数**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个）
-   参数（零个或多个）
-   CommandText （0个或1个）
-   ReturnType （零个或多个）
-   批注元素（零个或多个）

函数的返回类型必须与**returntype**元素或**returntype**属性（见下文）一起指定，但不能同时指定两者。

可以将在存储模型中指定的存储过程导入应用程序的概念模型。 有关详细信息，请参阅[用存储过程查询](~/ef6/modeling/designer/stored-procedures/query.md)。 **函数**元素还可用于在存储模型中定义自定义函数。  

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**Function**元素的特性。

> [!NOTE]
> 某些属性（此处未列出）可以用**存储**别名进行限定。 在更新模型时，模型更新向导会使用这些特性。

| 属性名称             | 是否必需 | 值                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**                   | 是         | 存储过程的名称。                                                                                                                                                                                  |
| **ReturnType**             | 是          | 存储过程的返回类型。                                                                                                                                                                           |
| 聚合              | 是          | 如果存储过程返回聚合值，**则为 True** ;否则**为 False**。                                                                                                                                  |
| **L**                | 是          | 如果函数是内置的<sup>1</sup>函数，则为**True** ; 否则为。否则**为 False**。                                                                                                                                  |
| **StoreFunctionName**      | 是          | 存储过程的名称。                                                                                                                                                                                  |
| **NiladicFunction**        | 是          | 如果函数是 niladic<sup>2</sup>函数，则为**True** ;否则**为 False** 。                                                                                                                                   |
| **IsComposable**           | 是          | 如果函数是可组合的<sup>3</sup>函数，则为**True** ;否则**为 False** 。                                                                                                                                |
| **ParameterTypeSemantics** | 是          | 定义用于解析函数重载的类型语义的枚举。 该枚举是在提供程序清单中根据函数定义来定义的。 默认值为**AllowImplicitConversion**。 |
| **架构**                 | 是          | 在其中定义存储过程的架构的名称。                                                                                                                                                   |

<sup>1</sup>内置函数是在数据库中定义的函数。 有关在存储模型中定义的函数的信息，请参阅 CommandText 元素（SSDL）。

<sup>2</sup> niladic 函数是不接受任何参数的函数，并且在调用时不需要括号。

<sup>3</sup>如果一个函数的输出可以是另一个函数的输入，则这两个函数是可组合的。

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**Function**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例显示了与**UpdateOrderQuantity**存储过程对应的**Function**元素。 该存储过程接受两个参数，且不返回值。

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a>Key 元素 (SSDL)

以存储架构定义语言（SSDL）表示的**Key**元素表示基础数据库中的表的主键。 **Key**是 EntityType 元素的子元素，它表示表中的行。 通过引用**EntityType**元素中定义的一个或多个属性元素，在**key**元素中定义主键。

**Key**元素可以具有以下子元素（按所列顺序）：

-   PropertyRef（一个或多个）
-   批注元素

没有任何特性适用于**该键**元素。

### <a name="example"></a>示例

下面的示例演示一个具有引用一个属性的键的**EntityType**元素：

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a>OnDelete 元素 (SSDL)

存储架构定义语言（SSDL）中的**OnDelete**元素反映在删除参与 foreign key 约束的行时的数据库行为。 如果将操作设置为**Cascade**，则还将删除引用要删除的行的行。 如果将操作设置为 "**无**"，则不会同时删除引用要删除行的行。 **OnDelete**元素是结束元素的子元素。

**OnDelete**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**OnDelete**元素的特性。

| 属性名称 | 是否必需 | 值                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **Action**     | 是         | **Cascade**或**None**。 （**限制**值有效，但行为与**None**相同。） |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**OnDelete**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示了一个**Association**元素，该元素定义了**FK\_CustomerOrders**外键约束。 **OnDelete**元素指示在删除**customers**表中的某一行时，该**订单**表中引用**该特定**行的所有行都将被删除。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a>Parameter 元素 (SSDL)

存储架构定义语言（SSDL）中的**Parameter**元素是 Function 元素的子元素，它指定数据库中存储过程的参数。

**Parameter**元素可以具有以下子元素（按所列顺序）：

-   文档（零个或一个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表描述了可应用于**参数**元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**       | 是         | 参数的名称。                                                                                                                                                                                                      |
| 类型       | 是         | 参数类型。                                                                                                                                                                                                             |
| **模式**       | 是          | **In**、 **Out**或**InOut** ，具体取决于参数是输入、输出还是输入/输出参数。                                                                                                                |
| **MaxLength**  | 是          | 参数的最大长度。                                                                                                                                                                                            |
| **精度**  | 是          | 参数的精度。                                                                                                                                                                                                 |
| **缩放**      | 是          | 参数的确定位数。                                                                                                                                                                                                     |
| SRID       | 是          | 空间系统引用标识符。 仅对空间类型的参数有效。 有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**参数**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个具有两个参数元素的**函数**元素，这些**参数**元素指定输入参数：

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a>Principal 元素 (SSDL)

以存储架构定义语言（SSDL）表示的**主体**元素是 ReferentialConstraint 元素的子元素，用于定义外键约束（也称为 "引用约束"）的主体端。 **主体**元素指定表中一个或多个列（或列）引用的主键列。 **PropertyRef**元素指定要引用的列。 Dependent 元素指定引用**主体**元素中指定的主键列的列。

**主体**元素可以具有以下子元素（按所列顺序）：

-   PropertyRef（一个或多个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**主体**元素的特性。

| 属性名称 | 是否必需 | 值                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **角色**       | 是         | 与相应的 End 元素的**Role**特性（如果使用）相同的值;否则，为包含被引用列的表的名称。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**主体**元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个 Association 元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列。 **主体**元素将**Customer**表的**CustomerId**列指定为约束的主体端。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a>Property 元素 (SSDL)

存储架构定义语言（SSDL）中的**属性**元素表示基础数据库中的表的列。 **属性**元素是 EntityType 元素的子元素，表示表中的行。 **EntityType**元素上定义的每个**属性**元素都表示一列。

**属性**元素不能具有任何子元素。

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**属性**元素的特性。

| 属性名称            | 是否必需 | 值                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名称**                  | 是         | 对应列的名称。                                                                                                                                                                                           |
| 类型                  | 是         | 对应列的类型。                                                                                                                                                                                           |
| **可以为 Null**              | 是          | **True** （默认值）或**False** ，具体取决于对应列是否可以具有 null 值。                                                                                                                  |
| **值**          | 是          | 对应列的默认值。                                                                                                                                                                                  |
| **MaxLength**             | 是          | 对应列的最大长度。                                                                                                                                                                                 |
| **FixedLength**           | 是          | **True**或**False** ，具体取决于相应的列值是否将作为固定长度字符串存储。                                                                                                              |
| **精度**             | 是          | 对应列的精度。                                                                                                                                                                                      |
| **缩放**                 | 是          | 对应列的小数位数。                                                                                                                                                                                          |
| **Unicode**               | 是          | **True**或**False** ，具体取决于对应列值是否将存储为 Unicode 字符串。                                                                                                                   |
| **排序规则**             | 是          | 指定要在数据源中使用的排序的字符串。                                                                                                                                                   |
| SRID                  | 是          | 空间系统引用标识符。 仅对空间类型的属性有效。 有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。 |
| **StoreGeneratedPattern** | 是          | **无**、**标识**（如果相应的列值是在数据库中生成的标识）或**计算**（如果在数据库中计算了相应的列值）。 对于 RowType 属性无效。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**Property**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个具有两个子**属性**元素的**EntityType**元素：

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a>PropertyRef 元素 (SSDL)

存储架构定义语言（SSDL）中的**PropertyRef**元素引用 EntityType 元素上定义的属性，以指示该属性将执行下列角色之一：

-   是**EntityType**表示的表的主键的一部分。 一个或多个**PropertyRef**元素可用于定义主键。 有关更多信息，请参见 Key 元素。
-   是引用约束的依赖端或主体端。 有关更多信息，请参见 ReferentialConstraint 元素。

**PropertyRef**元素只能具有以下子元素：

-   文档（零个或一个）
-   批注元素

### <a name="applicable-attributes"></a>适用的属性

下表介绍可应用于**PropertyRef**元素的特性。

| 属性名称 | 是否必需 | 值                                |
|:---------------|:------------|:-------------------------------------|
| **名称**       | 是         | 所引用属性的名称。 |

> [!NOTE]
> 可以将任意数量的批注特性（自定义 XML 特性）应用于**PropertyRef**元素。 然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示了一个**PropertyRef**元素，该元素用于通过引用**EntityType**元素上定义的属性来定义主键。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a>ReferentialConstraint 元素 (SSDL)

存储架构定义语言（SSDL）中的**ReferentialConstraint**元素表示基础数据库中的外键约束（也称为引用完整性约束）。 约束的主体端和依赖端分别由 Principal 和 Dependent 子元素指定。 通过 PropertyRef 元素引用参与主体端和依赖端的列。

**ReferentialConstraint**元素是 Association 元素的可选子元素。 如果未使用**ReferentialConstraint**元素映射**Association**元素中指定的 foreign key 约束，则必须使用 AssociationSetMapping 元素来执行此操作。

**ReferentialConstraint**元素可以具有以下子元素：

-   文档（零个或一个）
-   Principal（恰好一个）
-   依赖项（恰好一个）
-   批注元素（零个或多个）

### <a name="applicable-attributes"></a>适用的属性

可以将任意数量的批注特性（自定义 XML 特性）应用于**ReferentialConstraint**元素。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例演示一个**Association**元素，该元素使用**ReferentialConstraint**元素指定参与**FK\_CustomerOrders**外键约束的列：

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a>ReturnType 元素（SSDL）

存储架构定义语言（SSDL）中的**ReturnType**元素指定**函数**元素中定义的函数的返回类型。 还可以使用**ReturnType**特性指定函数返回类型。

函数的返回类型是使用**type**特性或**ReturnType**元素指定的。

**ReturnType**元素可以具有以下子元素：

- CollectionType （一）  

> [!NOTE]
> 可以向**ReturnType**元素应用任意数量的批注特性（自定义 XML 特性）。 然而，自定义特性可能不属于为 SSDL 保留的任何 XML 命名空间。 任何两个自定义特性的完全限定名称都不能相同。

### <a name="example"></a>示例

下面的示例使用一个**函数**，该函数返回行的集合。

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a>RowType 元素（SSDL）

存储架构定义语言（SSDL）中的**RowType**元素将未命名的结构定义为在存储区中定义的函数的返回类型。

**RowType**元素是**CollectionType**元素的子元素：

**RowType**元素可以具有以下子元素：

- Property（一个或多个）  

### <a name="example"></a>示例

下面的示例演示一个存储函数，该函数使用**CollectionType**元素来指定该函数返回行的集合（在**RowType**元素中指定）。


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a>Schema 元素 (SSDL)

存储架构定义语言（SSDL）中的**架构**元素是存储模型定义的根元素。 它包括构成存储模型的对象、函数和容器的定义。

**Schema**元素可包含零个或多个下列子元素：

-   关联
-   EntityType
-   EntityContainer
-   函数

**Schema**元素使用**namespace**属性为存储模型中的实体类型和关联对象定义命名空间。 在命名空间内，任何两个对象都不能同名。

存储模型命名空间不同于**Schema**元素的 XML 命名空间。 存储模型命名空间（由**命名空间**特性定义）是实体类型和关联类型的逻辑容器。 **Schema**元素的 XML 命名空间（由**xmlns**特性指示）是**schema**元素的子元素和属性的默认命名空间。 格式为 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl （其中，YYYY 和 MM 分别表示年份和月份）的 XML 命名空间是为 SSDL 保留的。 自定义元素和特性不能位于具有此格式的命名空间中。

### <a name="applicable-attributes"></a>适用的属性

下表描述了可对**Schema**元素应用的属性。

| 属性名称            | 是否必需 | 值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**             | 是         | 存储模型的命名空间。 **命名空间**属性的值用于构成类型的完全限定名称。 例如，如果名为*Customer*的**entitytype**在位于 examplemodel.store 命名空间中，则**entitytype**的完全限定名称为位于 examplemodel.store。 <br/> 以下字符串不能用作**Namespace**特性的值： **System**、**暂时性**或**Edm**。 **Namespace**属性的值不能与 CSDL Schema 元素中**namespace**属性的值相同。 |
| **Alias**                 | 是          | 用于取代命名空间名称的标识符。 例如，如果名为*Customer*的**EntityType**位于位于 examplemodel.store 命名空间中，并且**Alias**属性的值为*Storagemodel.customer*，则可以使用 storagemodel.customer 作为 EntityType 的完全限定名称 **。**                                                                                                                                                                                                                                                                                    |
| **提供程序**              | 是         | 数据提供程序。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | 是         | 一个标记，该标记指示提供程序清单返回到的提供程序。 没有为该标记定义格式。 标记的值由提供程序定义。 有关 SQL Server 提供程序清单令牌的信息，请参阅 SqlClient for 实体框架。                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>示例

下面的示例演示一个**架构**元素，该元素包含一个**EntityContainer**元素、两个**EntityType**元素和一个**Association**元素。

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a>Annotation 特性

以存储架构定义语言 (SSDL) 表示的批注特性在存储模型中是自定义 XML 特性，这些特性提供有关存储模型中元素的额外元数据。 除了具有有效的 XML 结构之外，以下约束也适用于批注特性：

-   批注特性不能位于为 SSDL 保留的任何 XML 命名空间中。
-   任何两个批注特性的完全限定名称都不能相同。

可以将多个批注特性应用于一个给定的 SSDL 元素。 可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。

### <a name="example"></a>示例

下面的示例演示一个 EntityType 元素，该元素具有应用于 "**订单 id** " 属性的批注特性。 该示例还显示了添加到**EntityType**元素的批注元素。

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a>批注元素 (SSDL)

以存储架构定义语言 (SSDL) 表示的批注元素是存储模型中的自定义 XML 元素，可提供有关存储模型的额外元数据。 除了具有有效的 XML 结构之外，批注元素还应满足以下约束：

-   批注元素不能位于为 SSDL 保留的任何 XML 命名空间中。
-   任何两个批注元素的完全限定名称都不能相同。
-   批注元素必须出现在给定 SSDL 元素的所有其他子元素之后。

多个批注元素可能是某个给定 SSDL 元素的子元素。 从 .NET Framework 版本4开始，可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。

### <a name="example"></a>示例

下面的示例显示具有 annotation 元素（**CustomElement**）的 EntityType 元素。 该示例还显示了应用于 "**订单 id** " 属性的批注特性。

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a>方面 (SSDL)

以存储架构定义语言 (SSDL) 表示的方面表示对于 Property 元素中指定的列类型的约束。 Facet 作为**属性**元素上的 XML 特性实现。

下表描述了 SSDL 中支持的方面：

| 方面           | 说明                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **排序规则**   | 指定在对属性值执行比较和排序操作时要使用的排序序列。                                                                                                             |
| **FixedLength** | 指定列值的长度是否可变。                                                                                                                                                                                                  |
| **MaxLength**   | 指定列值的最大长度。                                                                                                                                                                                                           |
| **精度**   | 对于**Decimal**类型的属性，指定属性值可以具有的位数。 对于类型为**Time**、 **DateTime**和**DateTimeOffset**的属性，指定列值的秒小数部分的位数。 |
| **缩放**       | 指定列值小数点右侧的位数。                                                                                                                                                                      |
| **Unicode**     | 指示是否将列值存储为 Unicode。                                                                                                                                                                                                    |
