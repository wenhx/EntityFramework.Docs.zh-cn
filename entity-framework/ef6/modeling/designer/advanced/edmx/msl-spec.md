---
title: MSL 规范-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
ms.openlocfilehash: 8990d1373ea2121ce11337a43dbcdf3b9e1532bd
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182551"
---
# <a name="msl-specification"></a><span data-ttu-id="c4b05-102">MSL 规范</span><span class="sxs-lookup"><span data-stu-id="c4b05-102">MSL Specification</span></span>
<span data-ttu-id="c4b05-103">映射规范语言（MSL）是一种基于 XML 的语言，用于描述实体框架应用程序的概念模型和存储模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-103">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="c4b05-104">在实体框架应用程序中，将在生成时从 msl 文件（用 MSL 编写）加载映射元数据。</span><span class="sxs-lookup"><span data-stu-id="c4b05-104">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="c4b05-105">实体框架在运行时使用映射元数据将针对概念模型的查询转换为特定于存储的命令。</span><span class="sxs-lookup"><span data-stu-id="c4b05-105">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="c4b05-106">Entity Framework Designer （EF 设计器）在设计时将映射信息存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="c4b05-106">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="c4b05-107">在生成时，Entity Designer 使用 .edmx 文件中的信息创建实体框架在运行时所需的 msl 文件</span><span class="sxs-lookup"><span data-stu-id="c4b05-107">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="c4b05-108">MSL 中引用的所有概念模型类型或存储模型类型的名称必须由其各自的命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-108">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="c4b05-109">有关概念模型命名空间名称的信息，请参阅[CSDL 规范](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="c4b05-109">For information about the conceptual model namespace name, see [CSDL Specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span> <span data-ttu-id="c4b05-110">有关存储模型命名空间名称的信息，请参阅[SSDL 规范](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="c4b05-110">For information about the storage model namespace name, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="c4b05-111">MSL 版本由 XML 命名空间进行区分。</span><span class="sxs-lookup"><span data-stu-id="c4b05-111">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="c4b05-112">MSL 版本</span><span class="sxs-lookup"><span data-stu-id="c4b05-112">MSL Version</span></span> | <span data-ttu-id="c4b05-113">XML 命名空间</span><span class="sxs-lookup"><span data-stu-id="c4b05-113">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="c4b05-114">MSL v1</span><span class="sxs-lookup"><span data-stu-id="c4b05-114">MSL v1</span></span>      | <span data-ttu-id="c4b05-115">urn：架构-microsoft-com： windows： storage：映射： CS</span><span class="sxs-lookup"><span data-stu-id="c4b05-115">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="c4b05-116">MSL v2</span><span class="sxs-lookup"><span data-stu-id="c4b05-116">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="c4b05-117">MSL v3</span><span class="sxs-lookup"><span data-stu-id="c4b05-117">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="c4b05-118">Alias 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-118">Alias Element (MSL)</span></span>

<span data-ttu-id="c4b05-119">映射规范语言（MSL）中的**Alias**元素是映射元素的子元素，用于定义概念模型和存储模型命名空间的别名。</span><span class="sxs-lookup"><span data-stu-id="c4b05-119">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="c4b05-120">MSL 中引用的所有概念模型类型或存储模型类型的名称必须由其各自的命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-120">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="c4b05-121">有关概念模型命名空间名称的信息，请参阅 Schema 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-121">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="c4b05-122">有关存储模型命名空间名称的信息，请参阅 Schema 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-122">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="c4b05-123">**Alias**元素不能有子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-123">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-124">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-124">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-125">下表描述了可应用于**Alias**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-125">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="c4b05-126">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-126">Attribute Name</span></span> | <span data-ttu-id="c4b05-127">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-127">Is Required</span></span> | <span data-ttu-id="c4b05-128">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-128">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-129">**Key**</span><span class="sxs-lookup"><span data-stu-id="c4b05-129">**Key**</span></span>        | <span data-ttu-id="c4b05-130">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-130">Yes</span></span>         | <span data-ttu-id="c4b05-131">**值**特性指定的命名空间的别名。</span><span class="sxs-lookup"><span data-stu-id="c4b05-131">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="c4b05-132">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="c4b05-132">**Value**</span></span>      | <span data-ttu-id="c4b05-133">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-133">Yes</span></span>         | <span data-ttu-id="c4b05-134">**键**元素的值为其别名的命名空间。</span><span class="sxs-lookup"><span data-stu-id="c4b05-134">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="c4b05-135">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-135">Example</span></span>

<span data-ttu-id="c4b05-136">下面的示例演示一个**alias**元素，该元素为在概念模型中定义的类型定义别名 `c`。</span><span class="sxs-lookup"><span data-stu-id="c4b05-136">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

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

## <a name="associationend-element-msl"></a><span data-ttu-id="c4b05-137">AssociationEnd 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-137">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="c4b05-138">当概念模型中的实体类型的修改函数映射到基础数据库中的存储过程时，将使用映射规范语言（MSL）中的**AssociationEnd**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-138">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="c4b05-139">如果修改存储过程所使用的参数的值保存在关联属性中，则**AssociationEnd**元素会将属性值映射到该参数。</span><span class="sxs-lookup"><span data-stu-id="c4b05-139">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="c4b05-140">有关更多信息，请参见下面的示例。</span><span class="sxs-lookup"><span data-stu-id="c4b05-140">For more information, see the example below.</span></span>

<span data-ttu-id="c4b05-141">有关将实体类型的修改函数映射到存储过程的详细信息，请参阅 ModificationFunctionMapping 元素（MSL）和演练：将实体映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-141">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="c4b05-142">**AssociationEnd**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-142">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-143">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-143">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-144">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-144">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-145">下表描述了适用于**AssociationEnd**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-145">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="c4b05-146">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-146">Attribute Name</span></span>     | <span data-ttu-id="c4b05-147">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-147">Is Required</span></span> | <span data-ttu-id="c4b05-148">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-148">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-149">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="c4b05-149">**AssociationSet**</span></span> | <span data-ttu-id="c4b05-150">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-150">Yes</span></span>         | <span data-ttu-id="c4b05-151">要映射的关联的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-151">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="c4b05-152">**From**</span><span class="sxs-lookup"><span data-stu-id="c4b05-152">**From**</span></span>           | <span data-ttu-id="c4b05-153">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-153">Yes</span></span>         | <span data-ttu-id="c4b05-154">导航属性的**FromRole**属性的值，该属性对应于要映射的关联。</span><span class="sxs-lookup"><span data-stu-id="c4b05-154">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="c4b05-155">有关详细信息，请参阅 NavigationProperty 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-155">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="c4b05-156">**若要**</span><span class="sxs-lookup"><span data-stu-id="c4b05-156">**To**</span></span>             | <span data-ttu-id="c4b05-157">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-157">Yes</span></span>         | <span data-ttu-id="c4b05-158">导航属性的**ToRole**属性的值，该属性对应于要映射的关联。</span><span class="sxs-lookup"><span data-stu-id="c4b05-158">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="c4b05-159">有关详细信息，请参阅 NavigationProperty 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-159">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="c4b05-160">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-160">Example</span></span>

<span data-ttu-id="c4b05-161">请考虑使用以下概念模型实体类型：</span><span class="sxs-lookup"><span data-stu-id="c4b05-161">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="c4b05-162">另请考虑使用以下存储过程：</span><span class="sxs-lookup"><span data-stu-id="c4b05-162">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="c4b05-163">若要将 `Course` 实体的更新函数映射到此存储过程，必须为**DepartmentID**参数提供一个值。</span><span class="sxs-lookup"><span data-stu-id="c4b05-163">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="c4b05-164">`DepartmentID` 的值与实体类型中的某个属性不对应；该值包含在独立的关联中，此关联的映射如下所示：</span><span class="sxs-lookup"><span data-stu-id="c4b05-164">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="c4b05-165">下面的代码演示了**AssociationEnd**元素，该元素用于将**FK\_课程\_部门**关联的**DepartmentID**属性映射到**UpdateCourse**存储过程（**课程**实体类型的更新函数映射到该存储过程）：</span><span class="sxs-lookup"><span data-stu-id="c4b05-165">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="c4b05-166">AssociationSetMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-166">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-167">映射规范语言（MSL）中的**AssociationSetMapping**元素定义概念模型中的关联与基础数据库中的表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-167">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="c4b05-168">概念模型中的关联是一些属性表示基础数据库中的主键列和外键列的类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-168">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="c4b05-169">**AssociationSetMapping**元素使用两个 EndProperty 元素来定义关联类型属性与数据库中的列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-169">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="c4b05-170">您可以使用 Condition 元素对这些映射施加条件。</span><span class="sxs-lookup"><span data-stu-id="c4b05-170">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="c4b05-171">使用 ModificationFunctionMapping 元素将关联的插入、更新和删除函数映射到数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-171">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="c4b05-172">通过在 QueryView 元素中使用实体 SQL 字符串，定义 association 和表列之间的只读映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-172">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-173">如果为概念模型中的关联定义了引用约束，则不需要将关联映射到**AssociationSetMapping**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-173">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="c4b05-174">如果具有引用约束的关联存在**AssociationSetMapping**元素，则将忽略在**AssociationSetMapping**元素中定义的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-174">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="c4b05-175">有关详细信息，请参阅 ReferentialConstraint 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-175">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="c4b05-176">**AssociationSetMapping**元素可以具有以下子元素</span><span class="sxs-lookup"><span data-stu-id="c4b05-176">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="c4b05-177">QueryView （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-177">QueryView (zero or one)</span></span>
-   <span data-ttu-id="c4b05-178">EndProperty（零个或两个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-178">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="c4b05-179">条件（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-179">Condition (zero or more)</span></span>
-   <span data-ttu-id="c4b05-180">ModificationFunctionMapping （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-180">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-181">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-181">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-182">下表介绍可应用于**AssociationSetMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-182">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="c4b05-183">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-183">Attribute Name</span></span>     | <span data-ttu-id="c4b05-184">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-184">Is Required</span></span> | <span data-ttu-id="c4b05-185">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-185">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-186">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-186">**Name**</span></span>           | <span data-ttu-id="c4b05-187">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-187">Yes</span></span>         | <span data-ttu-id="c4b05-188">要映射的概念模型关联集的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-188">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="c4b05-189">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-189">**TypeName**</span></span>       | <span data-ttu-id="c4b05-190">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-190">No</span></span>          | <span data-ttu-id="c4b05-191">要映射的概念模型关联类型的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-191">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="c4b05-192">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="c4b05-192">**StoreEntitySet**</span></span> | <span data-ttu-id="c4b05-193">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-193">No</span></span>          | <span data-ttu-id="c4b05-194">要映射的表的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-194">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="c4b05-195">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-195">Example</span></span>

<span data-ttu-id="c4b05-196">下面的示例显示一个**AssociationSetMapping**元素，在该元素中，在概念模型中设置的**FK\_课程\_部门**关联将映射到数据库中的**课程**表。</span><span class="sxs-lookup"><span data-stu-id="c4b05-196">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="c4b05-197">在子**EndProperty**元素中指定关联类型属性与表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-197">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="c4b05-198">ComplexProperty 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-198">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="c4b05-199">映射规范语言（MSL）中的**ComplexProperty**元素定义概念模型实体类型上的复杂类型属性与基础数据库中的表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-199">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="c4b05-200">属性-列映射在子 ScalarProperty 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-200">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="c4b05-201">**ComplexType**属性元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-201">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-202">ScalarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-202">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="c4b05-203">**ComplexProperty** （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-203">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="c4b05-204">ComplextTypeMapping （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-204">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="c4b05-205">条件（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-205">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-206">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-206">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-207">下表描述了适用于**ComplexProperty**元素的属性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-207">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="c4b05-208">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-208">Attribute Name</span></span> | <span data-ttu-id="c4b05-209">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-209">Is Required</span></span> | <span data-ttu-id="c4b05-210">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-210">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-211">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-211">**Name**</span></span>       | <span data-ttu-id="c4b05-212">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-212">Yes</span></span>         | <span data-ttu-id="c4b05-213">概念模型中要映射的实体类型的复杂属性的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-213">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="c4b05-214">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-214">**TypeName**</span></span>   | <span data-ttu-id="c4b05-215">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-215">No</span></span>          | <span data-ttu-id="c4b05-216">概念模型属性类型的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-216">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="c4b05-217">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-217">Example</span></span>

<span data-ttu-id="c4b05-218">下面的示例基于 School 模型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-218">The following example is based on the School model.</span></span> <span data-ttu-id="c4b05-219">下面的复杂类型已添加到概念模型中：</span><span class="sxs-lookup"><span data-stu-id="c4b05-219">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="c4b05-220">**Person**实体类型的**LastName**和**FirstName**属性已替换为一个复杂属性**名称**：</span><span class="sxs-lookup"><span data-stu-id="c4b05-220">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="c4b05-221">以下 MSL 显示了用于将**Name**属性映射到基础数据库中的列的**ComplexProperty**元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-221">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="c4b05-222">ComplexTypeMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-222">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-223">映射规范语言（MSL）中的**ComplexTypeMapping**元素是 ResultMapping 元素的子元素，在以下条件成立时定义概念模型中的函数导入与基础数据库中的存储过程之间的映射：</span><span class="sxs-lookup"><span data-stu-id="c4b05-223">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="c4b05-224">函数导入返回一个概念复杂类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-224">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="c4b05-225">存储过程返回的列的名称与复杂类型的属性名称不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="c4b05-225">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="c4b05-226">默认情况下，存储过程返回的列与复杂类型返回的列之间的映射基于列名称和属性名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-226">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="c4b05-227">如果列名称与属性名称不完全匹配，则必须使用**ComplexTypeMapping**元素来定义映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-227">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="c4b05-228">有关默认映射的示例，请参阅 FunctionImportMapping 元素（MSL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-228">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="c4b05-229">**ComplexTypeMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-229">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-230">ScalarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-230">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-231">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-231">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-232">下表描述了适用于**ComplexTypeMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-232">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="c4b05-233">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-233">Attribute Name</span></span> | <span data-ttu-id="c4b05-234">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-234">Is Required</span></span> | <span data-ttu-id="c4b05-235">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-235">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="c4b05-236">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-236">**TypeName**</span></span>   | <span data-ttu-id="c4b05-237">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-237">Yes</span></span>         | <span data-ttu-id="c4b05-238">要映射的复杂类型的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-238">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-239">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-239">Example</span></span>

<span data-ttu-id="c4b05-240">请考虑使用以下存储过程：</span><span class="sxs-lookup"><span data-stu-id="c4b05-240">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="c4b05-241">另请考虑使用以下概念模型复杂类型：</span><span class="sxs-lookup"><span data-stu-id="c4b05-241">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="c4b05-242">为了创建返回以前复杂类型的实例的函数导入，必须在**ComplexTypeMapping**元素中定义存储过程返回的列与实体类型返回的列之间的映射：</span><span class="sxs-lookup"><span data-stu-id="c4b05-242">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="c4b05-243">Condition 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-243">Condition Element (MSL)</span></span>

<span data-ttu-id="c4b05-244">映射规范语言（MSL）中的**Condition**元素对概念模型和基础数据库之间的映射施加条件。</span><span class="sxs-lookup"><span data-stu-id="c4b05-244">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="c4b05-245">如果满足子**条件**元素中指定的所有条件，则在 XML 节点内定义的映射将有效。</span><span class="sxs-lookup"><span data-stu-id="c4b05-245">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="c4b05-246">否则，该映射无效。</span><span class="sxs-lookup"><span data-stu-id="c4b05-246">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="c4b05-247">例如，如果 MappingFragment 元素包含一个或多个**Condition**子元素，则在满足子**条件**元素的所有条件时，在**MappingFragment**节点内定义的映射将有效。</span><span class="sxs-lookup"><span data-stu-id="c4b05-247">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="c4b05-248">每个条件可以应用于**名称**（概念模型实体属性的名称，由**Name**属性指定），也可以应用于**columnname** （数据库中列的名称，由**ColumnName**特性指定）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-248">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="c4b05-249">设置**Name**属性时，将根据实体属性值检查条件。</span><span class="sxs-lookup"><span data-stu-id="c4b05-249">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="c4b05-250">设置**ColumnName**属性后，将根据列值检查条件。</span><span class="sxs-lookup"><span data-stu-id="c4b05-250">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="c4b05-251">只能在**Condition**元素中指定**Name**或**ColumnName**特性中的一个。</span><span class="sxs-lookup"><span data-stu-id="c4b05-251">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-252">如果在 FunctionImportMapping 元素中使用**Condition**元素，则仅**Name**属性不适用。</span><span class="sxs-lookup"><span data-stu-id="c4b05-252">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="c4b05-253">**Condition**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-253">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="c4b05-254">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="c4b05-254">AssociationSetMapping</span></span>
-   <span data-ttu-id="c4b05-255">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-255">ComplexProperty</span></span>
-   <span data-ttu-id="c4b05-256">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="c4b05-256">EntitySetMapping</span></span>
-   <span data-ttu-id="c4b05-257">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="c4b05-257">MappingFragment</span></span>
-   <span data-ttu-id="c4b05-258">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="c4b05-258">EntityTypeMapping</span></span>

<span data-ttu-id="c4b05-259">**Condition**元素不能有子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-259">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-260">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-260">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-261">下表描述了适用于**Condition**元素的属性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-261">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="c4b05-262">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-262">Attribute Name</span></span> | <span data-ttu-id="c4b05-263">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-263">Is Required</span></span> | <span data-ttu-id="c4b05-264">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-264">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-265">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-265">**ColumnName**</span></span> | <span data-ttu-id="c4b05-266">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-266">No</span></span>          | <span data-ttu-id="c4b05-267">表列的名称，其值用于计算条件。</span><span class="sxs-lookup"><span data-stu-id="c4b05-267">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="c4b05-268">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="c4b05-268">**IsNull**</span></span>     | <span data-ttu-id="c4b05-269">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-269">No</span></span>          | <span data-ttu-id="c4b05-270">**True**或**False**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-270">**True** or **False**.</span></span> <span data-ttu-id="c4b05-271">如果值为**True** ，列值为**null**，或者如果值为**False**且列值不为**null**，则条件为 true。</span><span class="sxs-lookup"><span data-stu-id="c4b05-271">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="c4b05-272">否则，条件为 False。</span><span class="sxs-lookup"><span data-stu-id="c4b05-272">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="c4b05-273">不能同时使用**IsNull**和**Value**属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-273">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="c4b05-274">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="c4b05-274">**Value**</span></span>      | <span data-ttu-id="c4b05-275">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-275">No</span></span>          | <span data-ttu-id="c4b05-276">要与列值进行比较的值。</span><span class="sxs-lookup"><span data-stu-id="c4b05-276">The value with which the column value is compared.</span></span> <span data-ttu-id="c4b05-277">如果值不同，则该条件为 True。</span><span class="sxs-lookup"><span data-stu-id="c4b05-277">If the values are the same, the condition is true.</span></span> <span data-ttu-id="c4b05-278">否则，条件为 False。</span><span class="sxs-lookup"><span data-stu-id="c4b05-278">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="c4b05-279">不能同时使用**IsNull**和**Value**属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-279">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="c4b05-280">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-280">**Name**</span></span>       | <span data-ttu-id="c4b05-281">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-281">No</span></span>          | <span data-ttu-id="c4b05-282">概念模型实体属性的名称，其值用于计算条件。</span><span class="sxs-lookup"><span data-stu-id="c4b05-282">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="c4b05-283">如果在 FunctionImportMapping 元素中使用**Condition**元素，则此属性不适用。</span><span class="sxs-lookup"><span data-stu-id="c4b05-283">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="c4b05-284">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-284">Example</span></span>

<span data-ttu-id="c4b05-285">下面的示例将**条件**元素显示为**MappingFragment**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-285">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="c4b05-286">当 **"雇佣**日期" 不为 Null 并且**EnrollmentDate**为 null 时，将**在 SchoolModel**类型与**Person**表的**PersonID**和**雇用**日期列之间映射数据。</span><span class="sxs-lookup"><span data-stu-id="c4b05-286">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="c4b05-287">如果**EnrollmentDate**不为 Null 且**雇佣**日期为 null，则会在**SchoolModel**类型与**Person**表的**PersonID**和**注册**列之间映射数据。</span><span class="sxs-lookup"><span data-stu-id="c4b05-287">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="c4b05-288">DeleteFunction 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-288">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="c4b05-289">映射规范语言（MSL）中的**DeleteFunction**元素将概念模型中的实体类型或关联的删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-289">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="c4b05-290">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-290">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="c4b05-291">有关详细信息，请参阅 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-291">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-292">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="c4b05-292">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="c4b05-293">应用于 EntityTypeMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-293">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="c4b05-294">当应用于 EntityTypeMapping 元素时， **DeleteFunction**元素会将概念模型中的实体类型的删除函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-294">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="c4b05-295">**DeleteFunction**元素在应用于**EntityTypeMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-295">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="c4b05-296">AssociationEnd （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-296">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="c4b05-297">ComplexProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-297">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="c4b05-298">ScarlarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-298">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-299">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-299">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-300">下表描述了可应用于**EntityTypeMapping**元素的**DeleteFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-300">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="c4b05-301">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-301">Attribute Name</span></span>            | <span data-ttu-id="c4b05-302">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-302">Is Required</span></span> | <span data-ttu-id="c4b05-303">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-303">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-304">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-304">**FunctionName**</span></span>          | <span data-ttu-id="c4b05-305">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-305">Yes</span></span>         | <span data-ttu-id="c4b05-306">删除函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-306">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="c4b05-307">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-307">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="c4b05-308">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="c4b05-308">**RowsAffectedParameter**</span></span> | <span data-ttu-id="c4b05-309">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-309">No</span></span>          | <span data-ttu-id="c4b05-310">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-310">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="c4b05-311">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-311">Example</span></span>

<span data-ttu-id="c4b05-312">下面的示例基于 School 模型，并显示将**Person**实体类型的 delete 函数映射到**DeletePerson**存储过程的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-312">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="c4b05-313">**DeletePerson**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-313">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="c4b05-314">应用于 AssociationSetMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-314">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="c4b05-315">当应用于 AssociationSetMapping 元素时， **DeleteFunction**元素会将概念模型中的关联的删除函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-315">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="c4b05-316">**DeleteFunction**元素在应用于**AssociationSetMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-316">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="c4b05-317">EndProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-317">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-318">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-318">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-319">下表描述了可应用于**AssociationSetMapping**元素的**DeleteFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-319">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="c4b05-320">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-320">Attribute Name</span></span>            | <span data-ttu-id="c4b05-321">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-321">Is Required</span></span> | <span data-ttu-id="c4b05-322">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-322">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-323">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-323">**FunctionName**</span></span>          | <span data-ttu-id="c4b05-324">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-324">Yes</span></span>         | <span data-ttu-id="c4b05-325">删除函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-325">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="c4b05-326">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-326">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="c4b05-327">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="c4b05-327">**RowsAffectedParameter**</span></span> | <span data-ttu-id="c4b05-328">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-328">No</span></span>          | <span data-ttu-id="c4b05-329">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-329">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="c4b05-330">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-330">Example</span></span>

<span data-ttu-id="c4b05-331">下面的示例基于 School 模型，并显示了用于将**courseinstructor.courseid**关联的删除函数映射到**DeleteCourseInstructor**存储过程的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-331">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="c4b05-332">**DeleteCourseInstructor**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-332">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="c4b05-333">EndProperty 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-333">EndProperty Element (MSL)</span></span>

<span data-ttu-id="c4b05-334">映射规范语言（MSL）中的**EndProperty**元素定义概念模型关联和基础数据库的结束或修改函数之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-334">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="c4b05-335">属性-列映射在子 ScalarProperty 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-335">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="c4b05-336">当使用**EndProperty**元素定义概念模型关联末尾的映射时，它是 AssociationSetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-336">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="c4b05-337">当使用**EndProperty**元素定义概念模型关联的修改函数的映射时，它是 InsertFunction 元素或 DeleteFunction 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-337">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="c4b05-338">**EndProperty**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-338">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-339">ScalarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-339">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-340">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-340">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-341">下表描述了适用于**EndProperty**元素的属性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-341">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="c4b05-342">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-342">Attribute Name</span></span> | <span data-ttu-id="c4b05-343">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-343">Is Required</span></span> | <span data-ttu-id="c4b05-344">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-344">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="c4b05-345">名称</span><span class="sxs-lookup"><span data-stu-id="c4b05-345">Name</span></span>           | <span data-ttu-id="c4b05-346">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-346">Yes</span></span>         | <span data-ttu-id="c4b05-347">要映射的关联端的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-347">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-348">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-348">Example</span></span>

<span data-ttu-id="c4b05-349">下面的示例显示一个**AssociationSetMapping**元素，在该元素中，概念模型中的**FK\_课程\_部门**关联映射到数据库中的**课程**表。</span><span class="sxs-lookup"><span data-stu-id="c4b05-349">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="c4b05-350">在子**EndProperty**元素中指定关联类型属性与表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-350">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="c4b05-351">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-351">Example</span></span>

<span data-ttu-id="c4b05-352">下面的示例显示了**EndProperty**元素，该元素将关联（**courseinstructor.courseid**）的插入和删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-352">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="c4b05-353">映射到的函数在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-353">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="c4b05-354">EntityContainerMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-354">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-355">映射规范语言（MSL）中的**EntityContainerMapping**元素将概念模型中的实体容器映射到存储模型中的实体容器。</span><span class="sxs-lookup"><span data-stu-id="c4b05-355">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="c4b05-356">**EntityContainerMapping**元素是 Mapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-356">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="c4b05-357">**EntityContainerMapping**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="c4b05-357">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c4b05-358">EntitySetMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-358">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="c4b05-359">AssociationSetMapping （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-359">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="c4b05-360">FunctionImportMapping （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-360">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-361">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-361">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-362">下表介绍可应用于**EntityContainerMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-362">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="c4b05-363">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-363">Attribute Name</span></span>            | <span data-ttu-id="c4b05-364">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-364">Is Required</span></span> | <span data-ttu-id="c4b05-365">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-365">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-366">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="c4b05-366">**StorageModelContainer**</span></span> | <span data-ttu-id="c4b05-367">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-367">Yes</span></span>         | <span data-ttu-id="c4b05-368">要映射到的存储模型实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-368">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="c4b05-369">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="c4b05-369">**CdmEntityContainer**</span></span>    | <span data-ttu-id="c4b05-370">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-370">Yes</span></span>         | <span data-ttu-id="c4b05-371">要映射的概念模型实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-371">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="c4b05-372">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="c4b05-372">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="c4b05-373">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-373">No</span></span>          | <span data-ttu-id="c4b05-374">**True**或**False**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-374">**True** or **False**.</span></span> <span data-ttu-id="c4b05-375">如果**为 False**，则不生成任何更新视图。</span><span class="sxs-lookup"><span data-stu-id="c4b05-375">If **False**, no update views are generated.</span></span> <span data-ttu-id="c4b05-376">如果你具有只读映射，则此属性应设置为**False** ，因为数据可能无法成功往返。</span><span class="sxs-lookup"><span data-stu-id="c4b05-376">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="c4b05-377">默认值为 **True**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-377">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-378">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-378">Example</span></span>

<span data-ttu-id="c4b05-379">下面的示例演示了一个**EntityContainerMapping**元素，该元素将**SchoolModelEntities**容器（概念模型实体容器）映射到**SchoolModelStoreContainer**容器（存储模型实体容器）：</span><span class="sxs-lookup"><span data-stu-id="c4b05-379">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="c4b05-380">EntitySetMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-380">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-381">映射规范语言（MSL）中的**EntitySetMapping**元素将概念模型实体集中的所有类型都映射到存储模型中的实体集。</span><span class="sxs-lookup"><span data-stu-id="c4b05-381">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="c4b05-382">概念模型中的实体集为同一类型（和派生类型）实体的实例的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="c4b05-382">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="c4b05-383">存储模型中的实体集表示基础数据库中的一个表或视图。</span><span class="sxs-lookup"><span data-stu-id="c4b05-383">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="c4b05-384">概念模型实体集由**EntitySetMapping**元素的**Name**特性的值指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-384">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="c4b05-385">映射到的表或视图由每个子 MappingFragment 元素或**EntitySetMapping**元素本身中的**StoreEntitySet**属性指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-385">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="c4b05-386">**EntitySetMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-386">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-387">EntityTypeMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-387">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="c4b05-388">QueryView （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-388">QueryView (zero or one)</span></span>
-   <span data-ttu-id="c4b05-389">MappingFragment （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-389">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-390">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-390">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-391">下表介绍可应用于**EntitySetMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-391">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="c4b05-392">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-392">Attribute Name</span></span>           | <span data-ttu-id="c4b05-393">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-393">Is Required</span></span> | <span data-ttu-id="c4b05-394">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-394">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-395">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-395">**Name**</span></span>                 | <span data-ttu-id="c4b05-396">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-396">Yes</span></span>         | <span data-ttu-id="c4b05-397">要映射的概念模型实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-397">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="c4b05-398">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="c4b05-398">**TypeName** **1**</span></span>       | <span data-ttu-id="c4b05-399">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-399">No</span></span>          | <span data-ttu-id="c4b05-400">要映射的概念模型实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-400">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="c4b05-401">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="c4b05-401">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="c4b05-402">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-402">No</span></span>          | <span data-ttu-id="c4b05-403">要映射到的存储模型实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-403">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="c4b05-404">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="c4b05-404">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="c4b05-405">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-405">No</span></span>          | <span data-ttu-id="c4b05-406">**True**或**False** ，具体取决于是否只返回不同的行。</span><span class="sxs-lookup"><span data-stu-id="c4b05-406">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="c4b05-407">如果此特性设置为**True**，则 EntityContainerMapping 元素的**GenerateUpdateViews**特性必须设置为**False**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-407">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="c4b05-408">**1**可以使用**TypeName**和**StoreEntitySet**特性替代 EntityTypeMapping 和 MappingFragment 子元素，以便将单个实体类型映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="c4b05-408">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="c4b05-409">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-409">Example</span></span>

<span data-ttu-id="c4b05-410">下面的示例显示一个**EntitySetMapping**元素，该元素将概念模型的**课程**实体集中的三个类型（基类型和两个派生类型）映射到基础数据库中的三个不同表。</span><span class="sxs-lookup"><span data-stu-id="c4b05-410">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="c4b05-411">这些表由每个**MappingFragment**元素中的**StoreEntitySet**属性指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-411">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="c4b05-412">EntityTypeMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-412">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-413">映射规范语言（MSL）中的**EntityTypeMapping**元素定义概念模型中的实体类型与基础数据库中的表或视图之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-413">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="c4b05-414">有关概念模型实体类型与基础数据库表或视图的信息，请参见 EntityType 元素 (CSDL) 和 EntitySet 元素 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="c4b05-414">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="c4b05-415">要映射的概念模型实体类型由**EntityTypeMapping**元素的**TypeName**特性指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-415">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="c4b05-416">要映射的表或视图由子 MappingFragment 元素的**StoreEntitySet**属性指定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-416">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="c4b05-417">ModificationFunctionMapping 子元素可以用于将实体类型的插入、更新、或删除函数映射到数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-417">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="c4b05-418">**EntityTypeMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-418">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-419">MappingFragment （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-419">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="c4b05-420">ModificationFunctionMapping （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-420">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="c4b05-421">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-421">ScalarProperty</span></span>
-   <span data-ttu-id="c4b05-422">条件</span><span class="sxs-lookup"><span data-stu-id="c4b05-422">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-423">**MappingFragment**和**ModificationFunctionMapping**元素不能同时是**EntityTypeMapping**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-423">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="c4b05-424">当**ScalarProperty**和**Condition**元素在 FunctionImportMapping 元素中使用时，它们只能是**EntityTypeMapping**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-424">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-425">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-425">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-426">下表介绍可应用于**EntityTypeMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-426">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="c4b05-427">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-427">Attribute Name</span></span> | <span data-ttu-id="c4b05-428">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-428">Is Required</span></span> | <span data-ttu-id="c4b05-429">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-429">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-430">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-430">**TypeName**</span></span>   | <span data-ttu-id="c4b05-431">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-431">Yes</span></span>         | <span data-ttu-id="c4b05-432">要映射的概念模型实体类型的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-432">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="c4b05-433">如果类型为抽象类型或派生类型，则值必须为 `IsOfType(Namespace-qualified_type_name)`。</span><span class="sxs-lookup"><span data-stu-id="c4b05-433">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-434">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-434">Example</span></span>

<span data-ttu-id="c4b05-435">下面的示例演示一个具有两个子**EntityTypeMapping**元素的 EntitySetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-435">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="c4b05-436">在第一个**EntityTypeMapping**元素中， **SchoolModel**实体类型映射到**person**表。</span><span class="sxs-lookup"><span data-stu-id="c4b05-436">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="c4b05-437">在第二个**EntityTypeMapping**元素中， **SchoolModel**类型的更新功能映射到数据库中的存储过程**UpdatePerson**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-437">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="c4b05-438">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-438">Example</span></span>

<span data-ttu-id="c4b05-439">下一示例演示其中根类型为抽象类型的类型层次结构的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-439">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="c4b05-440">请注意，将 `IsOfType` 语法用于**TypeName**特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-440">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="c4b05-441">FunctionImportMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-441">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-442">映射规范语言（MSL）中的**FunctionImportMapping**元素定义概念模型中的函数导入与基础数据库中的存储过程或函数之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-442">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="c4b05-443">函数导入必须在概念模型中进行声明，存储的过程必须在存储模型中进行声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-443">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="c4b05-444">有关详细信息，请参阅 FunctionImport 元素（CSDL）和 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-444">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-445">默认情况下，如果函数导入返回概念模型实体类型或复杂类型，则基础存储过程返回的列名称必须与概念模型类型中的属性名称完全匹配。</span><span class="sxs-lookup"><span data-stu-id="c4b05-445">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="c4b05-446">如果列名称与属性名称不完全匹配，则必须在 ResultMapping 元素中定义映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-446">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="c4b05-447">**FunctionImportMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-447">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-448">ResultMapping （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-448">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-449">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-449">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-450">下表描述了适用于**FunctionImportMapping**元素的属性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-450">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="c4b05-451">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-451">Attribute Name</span></span>         | <span data-ttu-id="c4b05-452">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-452">Is Required</span></span> | <span data-ttu-id="c4b05-453">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-453">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-454">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-454">**FunctionImportName**</span></span> | <span data-ttu-id="c4b05-455">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-455">Yes</span></span>         | <span data-ttu-id="c4b05-456">概念模型中要映射的函数导入的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-456">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="c4b05-457">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-457">**FunctionName**</span></span>       | <span data-ttu-id="c4b05-458">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-458">Yes</span></span>         | <span data-ttu-id="c4b05-459">存储模型中要映射的函数的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-459">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-460">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-460">Example</span></span>

<span data-ttu-id="c4b05-461">下面的示例基于 School 模型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-461">The following example is based on the School model.</span></span> <span data-ttu-id="c4b05-462">请考虑在存储模型中使用以下函数：</span><span class="sxs-lookup"><span data-stu-id="c4b05-462">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="c4b05-463">另请考虑在概念模型中使用此函数导入：</span><span class="sxs-lookup"><span data-stu-id="c4b05-463">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="c4b05-464">下面的示例显示一个**FunctionImportMapping**元素，该元素用于将上述函数和函数导入映射到彼此：</span><span class="sxs-lookup"><span data-stu-id="c4b05-464">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="c4b05-465">InsertFunction 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-465">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="c4b05-466">映射规范语言（MSL）中的**InsertFunction**元素将概念模型中的实体类型或关联的插入函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-466">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="c4b05-467">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-467">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="c4b05-468">有关详细信息，请参阅 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-468">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-469">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="c4b05-469">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="c4b05-470">**InsertFunction**元素可以是 ModificationFunctionMapping 元素的子元素，并应用于 EntityTypeMapping 元素或 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-470">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="c4b05-471">应用于 EntityTypeMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-471">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="c4b05-472">当应用于 EntityTypeMapping 元素时， **InsertFunction**元素会将概念模型中的实体类型的插入函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-472">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="c4b05-473">**InsertFunction**元素在应用于**EntityTypeMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-473">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="c4b05-474">AssociationEnd （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-474">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="c4b05-475">ComplexProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-475">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="c4b05-476">ResultBinding （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-476">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="c4b05-477">ScarlarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-477">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-478">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-478">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-479">下表描述了应用于**EntityTypeMapping**元素时可应用于**InsertFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-479">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="c4b05-480">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-480">Attribute Name</span></span>            | <span data-ttu-id="c4b05-481">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-481">Is Required</span></span> | <span data-ttu-id="c4b05-482">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-482">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-483">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-483">**FunctionName**</span></span>          | <span data-ttu-id="c4b05-484">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-484">Yes</span></span>         | <span data-ttu-id="c4b05-485">插入函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-485">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="c4b05-486">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-486">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="c4b05-487">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="c4b05-487">**RowsAffectedParameter**</span></span> | <span data-ttu-id="c4b05-488">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-488">No</span></span>          | <span data-ttu-id="c4b05-489">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-489">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="c4b05-490">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-490">Example</span></span>

<span data-ttu-id="c4b05-491">下面的示例基于 School 模型，并显示了用于将 Person 实体类型的插入函数映射到**InsertPerson**存储过程的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-491">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="c4b05-492">**InsertPerson**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-492">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="c4b05-493">应用于 AssociationSetMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-493">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="c4b05-494">当应用于 AssociationSetMapping 元素时， **InsertFunction**元素会将概念模型中的关联的插入函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-494">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="c4b05-495">**InsertFunction**元素在应用于**AssociationSetMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-495">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="c4b05-496">EndProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-496">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-497">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-497">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-498">下表描述了可应用于**AssociationSetMapping**元素的**InsertFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-498">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="c4b05-499">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-499">Attribute Name</span></span>            | <span data-ttu-id="c4b05-500">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-500">Is Required</span></span> | <span data-ttu-id="c4b05-501">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-501">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-502">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-502">**FunctionName**</span></span>          | <span data-ttu-id="c4b05-503">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-503">Yes</span></span>         | <span data-ttu-id="c4b05-504">插入函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-504">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="c4b05-505">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-505">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="c4b05-506">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="c4b05-506">**RowsAffectedParameter**</span></span> | <span data-ttu-id="c4b05-507">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-507">No</span></span>          | <span data-ttu-id="c4b05-508">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-508">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="c4b05-509">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-509">Example</span></span>

<span data-ttu-id="c4b05-510">下面的示例基于 School 模型，并显示了用于将**courseinstructor.courseid**关联的插入函数映射到**InsertCourseInstructor**存储过程的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-510">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="c4b05-511">**InsertCourseInstructor**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-511">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="c4b05-512">Mapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-512">Mapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-513">映射规范语言（MSL）中的**mapping**元素包含用于将概念模型中定义的对象映射到数据库的信息（如存储模型中所述）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-513">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="c4b05-514">有关详细信息，请参阅 CSDL 规范和 SSDL 规范。</span><span class="sxs-lookup"><span data-stu-id="c4b05-514">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="c4b05-515">**Mapping**元素是映射规范的根元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-515">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="c4b05-516">映射规范的 XML 命名空间是 https://schemas.microsoft.com/ado/2009/11/mapping/cs 。</span><span class="sxs-lookup"><span data-stu-id="c4b05-516">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="c4b05-517">映射元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="c4b05-517">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c4b05-518">别名（0个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-518">Alias (zero or more)</span></span>
-   <span data-ttu-id="c4b05-519">EntityContainerMapping （恰好一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-519">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="c4b05-520">MSL 中引用的概念模型类型和存储模型类型的名称必须由其相应的命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="c4b05-520">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="c4b05-521">有关概念模型命名空间名称的信息，请参阅 Schema 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-521">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="c4b05-522">有关存储模型命名空间名称的信息，请参阅 Schema 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-522">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="c4b05-523">可以使用 Alias 元素定义 MSL 中使用的命名空间的别名。</span><span class="sxs-lookup"><span data-stu-id="c4b05-523">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-524">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-524">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-525">下表描述了可应用到**Mapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-525">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="c4b05-526">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-526">Attribute Name</span></span> | <span data-ttu-id="c4b05-527">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-527">Is Required</span></span> | <span data-ttu-id="c4b05-528">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-528">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="c4b05-529">空格键</span><span class="sxs-lookup"><span data-stu-id="c4b05-529">**Space**</span></span>      | <span data-ttu-id="c4b05-530">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-530">Yes</span></span>         | <span data-ttu-id="c4b05-531">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-531">**C-S**.</span></span> <span data-ttu-id="c4b05-532">这是固定值，因此不能更改。</span><span class="sxs-lookup"><span data-stu-id="c4b05-532">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-533">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-533">Example</span></span>

<span data-ttu-id="c4b05-534">下面的示例演示一个基于 School 模型的一部分的**映射**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-534">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="c4b05-535">有关 School 模型的详细信息，请参阅快速入门（实体框架）：</span><span class="sxs-lookup"><span data-stu-id="c4b05-535">For more information about the School model, see Quickstart (Entity Framework):</span></span>

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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="c4b05-536">MappingFragment 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-536">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="c4b05-537">映射规范语言（MSL）中的**MappingFragment**元素定义概念模型实体类型的属性与数据库中的表或视图之间的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-537">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="c4b05-538">有关概念模型实体类型与基础数据库表或视图的信息，请参见 EntityType 元素 (CSDL) 和 EntitySet 元素 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="c4b05-538">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="c4b05-539">**MappingFragment**可以是 EntityTypeMapping 元素或 EntitySetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-539">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="c4b05-540">**MappingFragment**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-540">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-541">ComplexType （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-541">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="c4b05-542">ScalarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-542">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="c4b05-543">条件（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-543">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-544">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-544">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-545">下表介绍可应用于**MappingFragment**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-545">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="c4b05-546">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-546">Attribute Name</span></span>          | <span data-ttu-id="c4b05-547">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-547">Is Required</span></span> | <span data-ttu-id="c4b05-548">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-548">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-549">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="c4b05-549">**StoreEntitySet**</span></span>      | <span data-ttu-id="c4b05-550">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-550">Yes</span></span>         | <span data-ttu-id="c4b05-551">要映射的表或视图的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-551">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="c4b05-552">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="c4b05-552">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="c4b05-553">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-553">No</span></span>          | <span data-ttu-id="c4b05-554">**True**或**False** ，具体取决于是否只返回不同的行。</span><span class="sxs-lookup"><span data-stu-id="c4b05-554">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="c4b05-555">如果此特性设置为**True**，则 EntityContainerMapping 元素的**GenerateUpdateViews**特性必须设置为**False**。</span><span class="sxs-lookup"><span data-stu-id="c4b05-555">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-556">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-556">Example</span></span>

<span data-ttu-id="c4b05-557">下面的示例演示一个**MappingFragment**元素作为**EntityTypeMapping**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-557">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="c4b05-558">在此示例中，概念模型中的**课程**类型的属性将映射到数据库中**课程**表的列。</span><span class="sxs-lookup"><span data-stu-id="c4b05-558">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="c4b05-559">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-559">Example</span></span>

<span data-ttu-id="c4b05-560">下面的示例演示一个**MappingFragment**元素作为**EntitySetMapping**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-560">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="c4b05-561">如上面的示例所示，概念模型中的**课程**类型的属性将映射到数据库中**课程**表的列。</span><span class="sxs-lookup"><span data-stu-id="c4b05-561">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="c4b05-562">ModificationFunctionMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-562">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-563">映射规范语言（MSL）中的**ModificationFunctionMapping**元素将概念模型实体类型的插入、更新和删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-563">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="c4b05-564">**ModificationFunctionMapping**元素还可以将概念模型中的多对多关联的插入和删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-564">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="c4b05-565">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-565">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="c4b05-566">有关详细信息，请参阅 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-566">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-567">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="c4b05-567">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="c4b05-568">如果将继承层次结构中的一个实体的修改函数映射到存储过程，则必须将该层次结构中所有类型的修改函数都映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-568">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="c4b05-569">**ModificationFunctionMapping**元素可以是 EntityTypeMapping 元素或 AssociationSetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-569">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="c4b05-570">**ModificationFunctionMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-570">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-571">DeleteFunction （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-571">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="c4b05-572">InsertFunction （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-572">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="c4b05-573">UpdateFunction （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-573">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="c4b05-574">没有适用于**ModificationFunctionMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-574">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="c4b05-575">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-575">Example</span></span>

<span data-ttu-id="c4b05-576">下面的示例演示了 School 模型中 "**人员**" 实体集的实体集映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-576">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="c4b05-577">除了**person**实体类型的列映射外，还会显示 "**人员**" 类型的插入、更新和删除函数的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-577">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="c4b05-578">映射到的函数在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-578">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="c4b05-579">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-579">Example</span></span>

<span data-ttu-id="c4b05-580">下面的示例演示了 School 模型中**courseinstructor.courseid**关联集的关联集映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-580">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="c4b05-581">除了**courseinstructor.courseid**关联的列映射外，还会显示**courseinstructor.courseid**关联的插入和删除函数的映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-581">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="c4b05-582">映射到的函数在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-582">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="c4b05-583">QueryView 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-583">QueryView Element (MSL)</span></span>

<span data-ttu-id="c4b05-584">映射规范语言（MSL）中的**QueryView**元素定义概念模型中的实体类型或关联与基础数据库中的表之间的只读映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-584">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="c4b05-585">映射是使用针对存储模型计算的实体 SQL 查询定义的，你可以根据概念模型中的实体或关联来表示结果集。</span><span class="sxs-lookup"><span data-stu-id="c4b05-585">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="c4b05-586">因为查询视图是只读的，所以不能使用标准更新命令来更新查询视图所定义的类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-586">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="c4b05-587">可以使用修改函数来更新这些类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-587">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="c4b05-588">有关详细信息，请参阅如何：将修改函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-588">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-589">在**QueryView**元素中，不支持实体 SQL 包含**GroupBy**、组聚合或导航属性的表达式。</span><span class="sxs-lookup"><span data-stu-id="c4b05-589">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="c4b05-590">**QueryView**元素可以是 EntitySetMapping 元素或 AssociationSetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-590">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="c4b05-591">在前一种情况中，查询视图定义概念模型中实体的只读映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-591">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="c4b05-592">在后一种情况中，查询视图定义概念模型中关联的只读映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-592">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-593">如果**AssociationSetMapping**元素用于与引用约束的关联，则忽略**AssociationSetMapping**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-593">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="c4b05-594">有关详细信息，请参阅 ReferentialConstraint 元素（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-594">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="c4b05-595">**QueryView**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-595">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-596">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-596">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-597">下表介绍可应用于**QueryView**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-597">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="c4b05-598">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-598">Attribute Name</span></span> | <span data-ttu-id="c4b05-599">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-599">Is Required</span></span> | <span data-ttu-id="c4b05-600">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-600">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-601">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-601">**TypeName**</span></span>   | <span data-ttu-id="c4b05-602">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-602">No</span></span>          | <span data-ttu-id="c4b05-603">要由查询视图映射的概念模型类型的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-603">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-604">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-604">Example</span></span>

<span data-ttu-id="c4b05-605">下面的示例将**QueryView**元素显示为**EntitySetMapping**元素的子元素，并为 School 模型中的**部门**实体类型定义查询视图映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-605">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="c4b05-606">由于该查询仅返回存储模型中 "**部门**" 类型的成员子集，因此，School 模型中的**部门**类型已根据以下映射进行了修改：</span><span class="sxs-lookup"><span data-stu-id="c4b05-606">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="c4b05-607">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-607">Example</span></span>

<span data-ttu-id="c4b05-608">下一个示例显示了**QueryView**元素作为**AssociationSetMapping**元素的子元素，并为 School 模型中的 `FK_Course_Department` 关联定义了只读映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-608">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="c4b05-609">注释</span><span class="sxs-lookup"><span data-stu-id="c4b05-609">Comments</span></span>

<span data-ttu-id="c4b05-610">可以定义查询视图来实现以下方案：</span><span class="sxs-lookup"><span data-stu-id="c4b05-610">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="c4b05-611">在概念模型中定义一个实体，该实体不包含存储模型中的实体的所有属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-611">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="c4b05-612">这包括没有默认值并且不支持**null**值的属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-612">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="c4b05-613">将存储模型中计算的列映射到概念模型中实体类型的属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-613">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="c4b05-614">定义一个映射，其中用于对概念模型中的实体进行分区的条件不基于相等性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-614">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="c4b05-615">使用**Condition**元素指定条件映射时，所提供的条件必须等于指定的值。</span><span class="sxs-lookup"><span data-stu-id="c4b05-615">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="c4b05-616">有关详细信息，请参阅 Condition 元素（MSL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-616">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="c4b05-617">将存储模型中的同一列映射到概念模型中的多个类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-617">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="c4b05-618">将多个类型映射到同一个表。</span><span class="sxs-lookup"><span data-stu-id="c4b05-618">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="c4b05-619">在概念模型中定义不基于关系架构中的外键的关联。</span><span class="sxs-lookup"><span data-stu-id="c4b05-619">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="c4b05-620">使用自定义业务逻辑设置概念模型中的属性值。</span><span class="sxs-lookup"><span data-stu-id="c4b05-620">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="c4b05-621">例如，您可以将数据源中的字符串值 "T" 映射到概念模型中的**true**值（布尔值）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-621">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="c4b05-622">为查询结果定义条件筛选器。</span><span class="sxs-lookup"><span data-stu-id="c4b05-622">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="c4b05-623">强制对概念模型中的数据施加比存储模型中更少的限制。</span><span class="sxs-lookup"><span data-stu-id="c4b05-623">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="c4b05-624">例如，您可以将概念模型中的属性设置为可以为 null，即使它映射到的列不支持**null**值。</span><span class="sxs-lookup"><span data-stu-id="c4b05-624">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="c4b05-625">为实体定义查询视图时需要考虑以下注意事项：</span><span class="sxs-lookup"><span data-stu-id="c4b05-625">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="c4b05-626">查询视图是只读的。</span><span class="sxs-lookup"><span data-stu-id="c4b05-626">Query views are read-only.</span></span> <span data-ttu-id="c4b05-627">只能使用修改函数来更新实体。</span><span class="sxs-lookup"><span data-stu-id="c4b05-627">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="c4b05-628">通过查询视图定义实体类型时，必须也通过查询视图来定义所有相关实体。</span><span class="sxs-lookup"><span data-stu-id="c4b05-628">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="c4b05-629">当您将多对多关联映射到存储模型中表示关系架构中的链接表的实体时，必须在此链接表的**AssociationSetMapping**元素中定义**QueryView**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-629">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="c4b05-630">必须为类型层次结构中的所有类型定义查询视图。</span><span class="sxs-lookup"><span data-stu-id="c4b05-630">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="c4b05-631">可以使用下列方式来实现：</span><span class="sxs-lookup"><span data-stu-id="c4b05-631">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="c4b05-632">使用单个**QueryView**元素指定单个实体 SQL 查询，该查询返回层次结构中所有实体类型的联合。</span><span class="sxs-lookup"><span data-stu-id="c4b05-632">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="c4b05-633">使用单个**QueryView**元素，该元素指定单个实体 SQL 查询，该查询使用 CASE 运算符根据特定条件返回层次结构中的特定实体类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-633">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="c4b05-634">对于层次结构中的特定类型，使用额外的**QueryView**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-634">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="c4b05-635">在这种情况下，请使用**QueryView**元素的**TypeName**特性来指定每个视图的实体类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-635">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="c4b05-636">定义查询视图时，不能在**EntitySetMapping**元素上指定**StorageSetName**属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-636">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="c4b05-637">定义查询视图时， **EntitySetMapping**元素不能同时包含**属性**映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-637">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="c4b05-638">ResultBinding 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-638">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="c4b05-639">当实体类型修改函数映射到基础数据库中的存储过程时，映射规范语言（MSL）中的**ResultBinding**元素将存储过程返回的列值映射到概念模型中的实体属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-639">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="c4b05-640">例如，当 insert 存储过程返回标识列的值时， **ResultBinding**元素会将返回的值映射到概念模型中的实体类型属性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-640">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="c4b05-641">**ResultBinding**元素可以是 InsertFunction 元素或 UpdateFunction 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-641">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="c4b05-642">**ResultBinding**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-642">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-643">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-643">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-644">下表描述了适用于**ResultBinding**元素的属性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-644">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="c4b05-645">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-645">Attribute Name</span></span> | <span data-ttu-id="c4b05-646">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-646">Is Required</span></span> | <span data-ttu-id="c4b05-647">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-647">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-648">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-648">**Name**</span></span>       | <span data-ttu-id="c4b05-649">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-649">Yes</span></span>         | <span data-ttu-id="c4b05-650">概念模型中要映射的实体属性的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-650">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="c4b05-651">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-651">**ColumnName**</span></span> | <span data-ttu-id="c4b05-652">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-652">Yes</span></span>         | <span data-ttu-id="c4b05-653">要映射的列的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-653">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="c4b05-654">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-654">Example</span></span>

<span data-ttu-id="c4b05-655">下面的示例基于 School 模型，并显示用于将**Person**实体类型的插入函数映射到**InsertPerson**存储过程的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-655">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="c4b05-656">（ **InsertPerson**存储过程如下所示，在存储模型中声明。）**ResultBinding**元素用于将存储过程（**NewPersonID**）返回的列值映射到实体类型属性（**PersonID**）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-656">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="c4b05-657">以下 Transact-sql 介绍了**InsertPerson**存储过程：</span><span class="sxs-lookup"><span data-stu-id="c4b05-657">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="c4b05-658">ResultMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-658">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="c4b05-659">映射规范语言（MSL）中的**ResultMapping**元素定义概念模型中的函数导入与基础数据库中的存储过程之间的映射（如果满足以下条件）：</span><span class="sxs-lookup"><span data-stu-id="c4b05-659">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="c4b05-660">函数导入返回一个概念模型实体类型或复杂类型。</span><span class="sxs-lookup"><span data-stu-id="c4b05-660">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="c4b05-661">存储过程返回的列的名称与实体类型或复杂类型中的属性名称不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="c4b05-661">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="c4b05-662">默认情况下，存储过程返回的列与实体类型或复杂类型中的属性之间的映射基于列名称和属性名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-662">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="c4b05-663">如果列名称与属性名称不完全匹配，则必须使用**ResultMapping**元素来定义映射。</span><span class="sxs-lookup"><span data-stu-id="c4b05-663">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="c4b05-664">有关默认映射的示例，请参阅 FunctionImportMapping 元素（MSL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-664">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="c4b05-665">**ResultMapping**元素是 FunctionImportMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-665">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="c4b05-666">**ResultMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-666">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-667">EntityTypeMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-667">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="c4b05-668">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="c4b05-668">ComplexTypeMapping</span></span>

<span data-ttu-id="c4b05-669">没有适用于**ResultMapping**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-669">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="c4b05-670">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-670">Example</span></span>

<span data-ttu-id="c4b05-671">请考虑使用以下存储过程：</span><span class="sxs-lookup"><span data-stu-id="c4b05-671">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="c4b05-672">另请考虑使用以下概念模型实体类型：</span><span class="sxs-lookup"><span data-stu-id="c4b05-672">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="c4b05-673">若要创建返回以前实体类型的实例的函数导入，必须在**ResultMapping**元素中定义存储过程返回的列与实体类型返回的列之间的映射：</span><span class="sxs-lookup"><span data-stu-id="c4b05-673">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="c4b05-674">ScalarProperty 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-674">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="c4b05-675">映射规范语言（MSL）中的**ScalarProperty**元素将概念模型实体类型、复杂类型或关联的属性映射到基础数据库中的表列或存储过程参数。</span><span class="sxs-lookup"><span data-stu-id="c4b05-675">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="c4b05-676">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-676">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="c4b05-677">有关详细信息，请参阅 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-677">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="c4b05-678">**ScalarProperty**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-678">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="c4b05-679">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="c4b05-679">MappingFragment</span></span>
-   <span data-ttu-id="c4b05-680">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-680">InsertFunction</span></span>
-   <span data-ttu-id="c4b05-681">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-681">UpdateFunction</span></span>
-   <span data-ttu-id="c4b05-682">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="c4b05-682">DeleteFunction</span></span>
-   <span data-ttu-id="c4b05-683">EndProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-683">EndProperty</span></span>
-   <span data-ttu-id="c4b05-684">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="c4b05-684">ComplexProperty</span></span>
-   <span data-ttu-id="c4b05-685">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="c4b05-685">ResultMapping</span></span>

<span data-ttu-id="c4b05-686">作为**MappingFragment**、 **ComplexProperty**或**EndProperty**元素的子元素， **ScalarProperty**元素会将概念模型中的属性映射到数据库中的某一列。</span><span class="sxs-lookup"><span data-stu-id="c4b05-686">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="c4b05-687">作为**InsertFunction**、 **UpdateFunction**或**DeleteFunction**元素的子元素， **ScalarProperty**元素会将概念模型中的属性映射到存储过程参数。</span><span class="sxs-lookup"><span data-stu-id="c4b05-687">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="c4b05-688">**ScalarProperty**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-688">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-689">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-689">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-690">应用于**ScalarProperty**元素的特性因元素的角色而异。</span><span class="sxs-lookup"><span data-stu-id="c4b05-690">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="c4b05-691">下表介绍了在**ScalarProperty**元素用于将概念模型属性映射到数据库中的列时适用的特性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-691">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="c4b05-692">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-692">Attribute Name</span></span> | <span data-ttu-id="c4b05-693">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-693">Is Required</span></span> | <span data-ttu-id="c4b05-694">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-694">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="c4b05-695">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-695">**Name**</span></span>       | <span data-ttu-id="c4b05-696">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-696">Yes</span></span>         | <span data-ttu-id="c4b05-697">要映射的概念模型属性的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-697">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="c4b05-698">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-698">**ColumnName**</span></span> | <span data-ttu-id="c4b05-699">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-699">Yes</span></span>         | <span data-ttu-id="c4b05-700">要映射的表列的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-700">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="c4b05-701">下表描述了当**ScalarProperty**元素用于将概念模型属性映射到存储过程参数时适用的特性：</span><span class="sxs-lookup"><span data-stu-id="c4b05-701">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="c4b05-702">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-702">Attribute Name</span></span>    | <span data-ttu-id="c4b05-703">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-703">Is Required</span></span> | <span data-ttu-id="c4b05-704">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-704">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-705">**Name**</span><span class="sxs-lookup"><span data-stu-id="c4b05-705">**Name**</span></span>          | <span data-ttu-id="c4b05-706">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-706">Yes</span></span>         | <span data-ttu-id="c4b05-707">要映射的概念模型属性的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-707">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="c4b05-708">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-708">**ParameterName**</span></span> | <span data-ttu-id="c4b05-709">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-709">Yes</span></span>         | <span data-ttu-id="c4b05-710">正在映射的参数的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-710">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="c4b05-711">**版本**</span><span class="sxs-lookup"><span data-stu-id="c4b05-711">**Version**</span></span>       | <span data-ttu-id="c4b05-712">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-712">No</span></span>          | <span data-ttu-id="c4b05-713">**当前**或**原始**值，具体取决于是否应将当前值或属性的原始值用于并发检查。</span><span class="sxs-lookup"><span data-stu-id="c4b05-713">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="c4b05-714">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-714">Example</span></span>

<span data-ttu-id="c4b05-715">下面的示例演示了两种方法中使用的**ScalarProperty**元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-715">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="c4b05-716">将**person**实体类型的属性映射到**person**表的列。</span><span class="sxs-lookup"><span data-stu-id="c4b05-716">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="c4b05-717">将**Person**实体类型的属性映射到**UpdatePerson**存储过程的参数。</span><span class="sxs-lookup"><span data-stu-id="c4b05-717">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="c4b05-718">存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-718">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="c4b05-719">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-719">Example</span></span>

<span data-ttu-id="c4b05-720">下一个示例显示了**ScalarProperty**元素，该元素用于将概念模型关联的插入和删除函数映射到数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-720">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="c4b05-721">存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-721">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="c4b05-722">UpdateFunction 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="c4b05-722">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="c4b05-723">映射规范语言（MSL）中的**UpdateFunction**元素将概念模型中的实体类型的更新函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="c4b05-723">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="c4b05-724">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-724">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="c4b05-725">有关详细信息，请参阅 Function 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="c4b05-725">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="c4b05-726">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="c4b05-726">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="c4b05-727">**UpdateFunction**元素可以是 ModificationFunctionMapping 元素的子元素并应用于 EntityTypeMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-727">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="c4b05-728">**UpdateFunction**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="c4b05-728">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="c4b05-729">AssociationEnd （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-729">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="c4b05-730">ComplexProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-730">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="c4b05-731">ResultBinding （零个或一个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-731">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="c4b05-732">ScarlarProperty （零个或多个）</span><span class="sxs-lookup"><span data-stu-id="c4b05-732">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c4b05-733">适用的特性</span><span class="sxs-lookup"><span data-stu-id="c4b05-733">Applicable Attributes</span></span>

<span data-ttu-id="c4b05-734">下表介绍可应用于**UpdateFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="c4b05-734">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="c4b05-735">特性名</span><span class="sxs-lookup"><span data-stu-id="c4b05-735">Attribute Name</span></span>            | <span data-ttu-id="c4b05-736">是否必需</span><span class="sxs-lookup"><span data-stu-id="c4b05-736">Is Required</span></span> | <span data-ttu-id="c4b05-737">值</span><span class="sxs-lookup"><span data-stu-id="c4b05-737">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c4b05-738">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="c4b05-738">**FunctionName**</span></span>          | <span data-ttu-id="c4b05-739">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-739">Yes</span></span>         | <span data-ttu-id="c4b05-740">更新函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-740">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="c4b05-741">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-741">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="c4b05-742">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="c4b05-742">**RowsAffectedParameter**</span></span> | <span data-ttu-id="c4b05-743">是</span><span class="sxs-lookup"><span data-stu-id="c4b05-743">No</span></span>          | <span data-ttu-id="c4b05-744">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="c4b05-744">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="c4b05-745">示例</span><span class="sxs-lookup"><span data-stu-id="c4b05-745">Example</span></span>

<span data-ttu-id="c4b05-746">下面的示例基于 School 模型，并显示用于将**Person**实体类型的更新函数映射到**UpdatePerson**存储过程的**UpdateFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="c4b05-746">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="c4b05-747">**UpdatePerson**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="c4b05-747">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
