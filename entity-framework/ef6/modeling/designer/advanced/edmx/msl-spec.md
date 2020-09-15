---
title: MSL 规范-EF6
description: 实体框架6中的 MSL 规范
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 36527b9827a7576f73c9ef476462d9392b601984
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070107"
---
# <a name="msl-specification"></a><span data-ttu-id="a9943-103">MSL 规范</span><span class="sxs-lookup"><span data-stu-id="a9943-103">MSL Specification</span></span>
<span data-ttu-id="a9943-104">映射规范语言 (MSL) 是一种基于 XML 的语言，用于描述实体框架应用程序的概念模型和存储模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="a9943-105">在实体框架应用程序中，映射元数据从 msl 文件加载 (在生成时以 MSL) 编写。</span><span class="sxs-lookup"><span data-stu-id="a9943-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="a9943-106">实体框架在运行时使用映射元数据将针对概念模型的查询转换为特定于存储的命令。</span><span class="sxs-lookup"><span data-stu-id="a9943-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="a9943-107">Entity Framework Designer (EF 设计器) 在设计时将映射信息存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="a9943-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="a9943-108">在生成时，Entity Designer 使用 .edmx 文件中的信息创建实体框架在运行时所需的 msl 文件</span><span class="sxs-lookup"><span data-stu-id="a9943-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="a9943-109">MSL 中引用的所有概念模型类型或存储模型类型的名称必须由其各自的命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="a9943-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="a9943-110">有关概念模型命名空间名称的信息，请参阅 [CSDL 规范](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。</span><span class="sxs-lookup"><span data-stu-id="a9943-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="a9943-111">有关存储模型命名空间名称的信息，请参阅 [SSDL 规范](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)。</span><span class="sxs-lookup"><span data-stu-id="a9943-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="a9943-112">MSL 版本由 XML 命名空间进行区分。</span><span class="sxs-lookup"><span data-stu-id="a9943-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="a9943-113">MSL 版本</span><span class="sxs-lookup"><span data-stu-id="a9943-113">MSL Version</span></span> | <span data-ttu-id="a9943-114">XML 命名空间</span><span class="sxs-lookup"><span data-stu-id="a9943-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="a9943-115">MSL v1</span><span class="sxs-lookup"><span data-stu-id="a9943-115">MSL v1</span></span>      | <span data-ttu-id="a9943-116">urn：架构-microsoft-com： windows： storage：映射： CS</span><span class="sxs-lookup"><span data-stu-id="a9943-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="a9943-117">MSL v2</span><span class="sxs-lookup"><span data-stu-id="a9943-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="a9943-118">MSL v3</span><span class="sxs-lookup"><span data-stu-id="a9943-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="a9943-119">Alias 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-119">Alias Element (MSL)</span></span>

<span data-ttu-id="a9943-120">映射规范语言 (MSL) 中的 **Alias** 元素是一个映射元素的子元素，用于定义概念模型和存储模型命名空间的别名。</span><span class="sxs-lookup"><span data-stu-id="a9943-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="a9943-121">MSL 中引用的所有概念模型类型或存储模型类型的名称必须由其各自的命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="a9943-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="a9943-122">有关概念模型命名空间名称的信息，请参阅 (CSDL) 的架构元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="a9943-123">有关存储模型命名空间名称的信息，请参阅 (SSDL) 的架构元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="a9943-124">**Alias**元素不能有子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-125">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-125">Applicable Attributes</span></span>

<span data-ttu-id="a9943-126">下表描述了可应用于 **Alias** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="a9943-127">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-127">Attribute Name</span></span> | <span data-ttu-id="a9943-128">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-128">Is Required</span></span> | <span data-ttu-id="a9943-129">值</span><span class="sxs-lookup"><span data-stu-id="a9943-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="a9943-130">**键**</span><span class="sxs-lookup"><span data-stu-id="a9943-130">**Key**</span></span>        | <span data-ttu-id="a9943-131">是</span><span class="sxs-lookup"><span data-stu-id="a9943-131">Yes</span></span>         | <span data-ttu-id="a9943-132">**值**特性指定的命名空间的别名。</span><span class="sxs-lookup"><span data-stu-id="a9943-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="a9943-133">**值**</span><span class="sxs-lookup"><span data-stu-id="a9943-133">**Value**</span></span>      | <span data-ttu-id="a9943-134">是</span><span class="sxs-lookup"><span data-stu-id="a9943-134">Yes</span></span>         | <span data-ttu-id="a9943-135">**键**元素的值为其别名的命名空间。</span><span class="sxs-lookup"><span data-stu-id="a9943-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="a9943-136">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-136">Example</span></span>

<span data-ttu-id="a9943-137">下面的示例演示一个 **alias** 元素，该元素定义 `c` 概念模型中定义的类型的别名。</span><span class="sxs-lookup"><span data-stu-id="a9943-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

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

## <a name="associationend-element-msl"></a><span data-ttu-id="a9943-138">AssociationEnd 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="a9943-139">当概念模型中的实体类型的修改函数映射到基础数据库中的存储过程时，将使用映射规范语言 (MSL) 中的 **AssociationEnd** 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="a9943-140">如果修改存储过程所使用的参数的值保存在关联属性中，则 **AssociationEnd** 元素会将属性值映射到该参数。</span><span class="sxs-lookup"><span data-stu-id="a9943-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="a9943-141">有关更多信息，请参见下面的示例。</span><span class="sxs-lookup"><span data-stu-id="a9943-141">For more information, see the example below.</span></span>

<span data-ttu-id="a9943-142">有关将实体类型的修改函数映射到存储过程的详细信息，请参阅 ModificationFunctionMapping 元素 (MSL) 和演练：将实体映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="a9943-143">**AssociationEnd**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-145">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-145">Applicable Attributes</span></span>

<span data-ttu-id="a9943-146">下表描述了适用于 **AssociationEnd** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="a9943-147">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-147">Attribute Name</span></span>     | <span data-ttu-id="a9943-148">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-148">Is Required</span></span> | <span data-ttu-id="a9943-149">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-150">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="a9943-150">**AssociationSet**</span></span> | <span data-ttu-id="a9943-151">是</span><span class="sxs-lookup"><span data-stu-id="a9943-151">Yes</span></span>         | <span data-ttu-id="a9943-152">要映射的关联的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="a9943-153">**From**</span><span class="sxs-lookup"><span data-stu-id="a9943-153">**From**</span></span>           | <span data-ttu-id="a9943-154">是</span><span class="sxs-lookup"><span data-stu-id="a9943-154">Yes</span></span>         | <span data-ttu-id="a9943-155">导航属性的 **FromRole** 属性的值，该属性对应于要映射的关联。</span><span class="sxs-lookup"><span data-stu-id="a9943-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="a9943-156">有关详细信息，请参阅 NavigationProperty 元素 (CSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="a9943-157">**收件人**</span><span class="sxs-lookup"><span data-stu-id="a9943-157">**To**</span></span>             | <span data-ttu-id="a9943-158">是</span><span class="sxs-lookup"><span data-stu-id="a9943-158">Yes</span></span>         | <span data-ttu-id="a9943-159">导航属性的 **ToRole** 属性的值，该属性对应于要映射的关联。</span><span class="sxs-lookup"><span data-stu-id="a9943-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="a9943-160">有关详细信息，请参阅 NavigationProperty 元素 (CSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="a9943-161">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-161">Example</span></span>

<span data-ttu-id="a9943-162">请考虑使用以下概念模型实体类型：</span><span class="sxs-lookup"><span data-stu-id="a9943-162">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="a9943-163">另请考虑使用以下存储过程：</span><span class="sxs-lookup"><span data-stu-id="a9943-163">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="a9943-164">若要将实体的更新函数映射 `Course` 到此存储过程，必须为 **DepartmentID** 参数提供一个值。</span><span class="sxs-lookup"><span data-stu-id="a9943-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="a9943-165">`DepartmentID` 的值与实体类型中的某个属性不对应；该值包含在独立的关联中，此关联的映射如下所示：</span><span class="sxs-lookup"><span data-stu-id="a9943-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="a9943-166">下面的代码演示了**AssociationEnd**元素，该元素用于将**FK \_ 课程 \_ 部门**关联的**DepartmentID**属性映射到**UpdateCourse**存储过程，该存储过程 (将**课程**实体类型的更新函数映射到该存储过程) ：</span><span class="sxs-lookup"><span data-stu-id="a9943-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="a9943-167">AssociationSetMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-168">映射规范语言 (MSL) 中的 **AssociationSetMapping** 元素定义概念模型中的关联与基础数据库中的表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="a9943-169">概念模型中的关联是一些属性表示基础数据库中的主键列和外键列的类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="a9943-170">**AssociationSetMapping**元素使用两个 EndProperty 元素来定义关联类型属性与数据库中的列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="a9943-171">您可以使用 Condition 元素对这些映射施加条件。</span><span class="sxs-lookup"><span data-stu-id="a9943-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="a9943-172">使用 ModificationFunctionMapping 元素将关联的插入、更新和删除函数映射到数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="a9943-173">通过在 QueryView 元素中使用实体 SQL 字符串，定义 association 和表列之间的只读映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-174">如果为概念模型中的关联定义了引用约束，则不需要将关联映射到 **AssociationSetMapping** 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="a9943-175">如果具有引用约束的关联存在 **AssociationSetMapping** 元素，则将忽略在 **AssociationSetMapping** 元素中定义的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="a9943-176">有关详细信息，请参阅 ReferentialConstraint 元素 (CSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="a9943-177">**AssociationSetMapping**元素可以具有以下子元素</span><span class="sxs-lookup"><span data-stu-id="a9943-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="a9943-178">QueryView（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="a9943-179">EndProperty（零个或两个）</span><span class="sxs-lookup"><span data-stu-id="a9943-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="a9943-180">Condition（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="a9943-181">ModificationFunctionMapping（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-182">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-182">Applicable Attributes</span></span>

<span data-ttu-id="a9943-183">下表介绍可应用于 **AssociationSetMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="a9943-184">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-184">Attribute Name</span></span>     | <span data-ttu-id="a9943-185">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-185">Is Required</span></span> | <span data-ttu-id="a9943-186">值</span><span class="sxs-lookup"><span data-stu-id="a9943-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-187">**名称**</span><span class="sxs-lookup"><span data-stu-id="a9943-187">**Name**</span></span>           | <span data-ttu-id="a9943-188">是</span><span class="sxs-lookup"><span data-stu-id="a9943-188">Yes</span></span>         | <span data-ttu-id="a9943-189">要映射的概念模型关联集的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="a9943-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="a9943-190">**TypeName**</span></span>       | <span data-ttu-id="a9943-191">否</span><span class="sxs-lookup"><span data-stu-id="a9943-191">No</span></span>          | <span data-ttu-id="a9943-192">要映射的概念模型关联类型的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="a9943-193">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="a9943-193">**StoreEntitySet**</span></span> | <span data-ttu-id="a9943-194">否</span><span class="sxs-lookup"><span data-stu-id="a9943-194">No</span></span>          | <span data-ttu-id="a9943-195">要映射的表的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="a9943-196">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-196">Example</span></span>

<span data-ttu-id="a9943-197">下面的示例演示了一个 **AssociationSetMapping** 元素，在该元素中，概念模型中的 **FK \_ 课程 \_ 部** 关联集映射到数据库中的 **课程** 表。</span><span class="sxs-lookup"><span data-stu-id="a9943-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="a9943-198">在子 **EndProperty** 元素中指定关联类型属性与表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="a9943-199">ComplexProperty 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="a9943-200">映射规范语言 (MSL) 中的 **ComplexProperty** 元素定义了概念模型实体类型上的复杂类型属性与基础数据库中的表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="a9943-201">属性-列映射在子 ScalarProperty 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="a9943-202">**ComplexType**属性元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-203">ScalarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="a9943-204">**ComplexProperty** (零个或多个) </span><span class="sxs-lookup"><span data-stu-id="a9943-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="a9943-205">ComplextTypeMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="a9943-206">Condition（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-207">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-207">Applicable Attributes</span></span>

<span data-ttu-id="a9943-208">下表描述了适用于 **ComplexProperty** 元素的属性：</span><span class="sxs-lookup"><span data-stu-id="a9943-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="a9943-209">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-209">Attribute Name</span></span> | <span data-ttu-id="a9943-210">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-210">Is Required</span></span> | <span data-ttu-id="a9943-211">值</span><span class="sxs-lookup"><span data-stu-id="a9943-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-212">**名称**</span><span class="sxs-lookup"><span data-stu-id="a9943-212">**Name**</span></span>       | <span data-ttu-id="a9943-213">是</span><span class="sxs-lookup"><span data-stu-id="a9943-213">Yes</span></span>         | <span data-ttu-id="a9943-214">概念模型中要映射的实体类型的复杂属性的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="a9943-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="a9943-215">**TypeName**</span></span>   | <span data-ttu-id="a9943-216">否</span><span class="sxs-lookup"><span data-stu-id="a9943-216">No</span></span>          | <span data-ttu-id="a9943-217">概念模型属性类型的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="a9943-218">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-218">Example</span></span>

<span data-ttu-id="a9943-219">下面的示例基于 School 模型。</span><span class="sxs-lookup"><span data-stu-id="a9943-219">The following example is based on the School model.</span></span> <span data-ttu-id="a9943-220">下面的复杂类型已添加到概念模型中：</span><span class="sxs-lookup"><span data-stu-id="a9943-220">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="a9943-221">**Person**实体类型的**LastName**和**FirstName**属性已替换为一个复杂属性**名称**：</span><span class="sxs-lookup"><span data-stu-id="a9943-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="a9943-222">以下 MSL 显示了用于将**Name**属性映射到基础数据库中的列的**ComplexProperty**元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="a9943-223">ComplexTypeMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-224">映射规范语言 (MSL) 中的 **ComplexTypeMapping** 元素是 ResultMapping 元素的子元素，用于定义概念模型中的函数导入与基础数据库中的存储过程之间的映射（如果满足以下条件）：</span><span class="sxs-lookup"><span data-stu-id="a9943-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="a9943-225">函数导入返回一个概念复杂类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="a9943-226">存储过程返回的列的名称与复杂类型的属性名称不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="a9943-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="a9943-227">默认情况下，存储过程返回的列与复杂类型返回的列之间的映射基于列名称和属性名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="a9943-228">如果列名称与属性名称不完全匹配，则必须使用 **ComplexTypeMapping** 元素来定义映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="a9943-229">有关默认映射的示例，请参阅 FunctionImportMapping 元素 (MSL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="a9943-230">**ComplexTypeMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-231">ScalarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-232">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-232">Applicable Attributes</span></span>

<span data-ttu-id="a9943-233">下表描述了适用于 **ComplexTypeMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="a9943-234">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-234">Attribute Name</span></span> | <span data-ttu-id="a9943-235">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-235">Is Required</span></span> | <span data-ttu-id="a9943-236">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="a9943-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="a9943-237">**TypeName**</span></span>   | <span data-ttu-id="a9943-238">是</span><span class="sxs-lookup"><span data-stu-id="a9943-238">Yes</span></span>         | <span data-ttu-id="a9943-239">要映射的复杂类型的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-240">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-240">Example</span></span>

<span data-ttu-id="a9943-241">请思考以下存储过程：</span><span class="sxs-lookup"><span data-stu-id="a9943-241">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="a9943-242">另请考虑使用以下概念模型复杂类型：</span><span class="sxs-lookup"><span data-stu-id="a9943-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="a9943-243">为了创建返回以前复杂类型的实例的函数导入，必须在 **ComplexTypeMapping** 元素中定义存储过程返回的列与实体类型返回的列之间的映射：</span><span class="sxs-lookup"><span data-stu-id="a9943-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="a9943-244">Condition 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-244">Condition Element (MSL)</span></span>

<span data-ttu-id="a9943-245">映射规范语言 (MSL 中的 **Condition** 元素) 对概念模型和基础数据库之间的映射施加条件。</span><span class="sxs-lookup"><span data-stu-id="a9943-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="a9943-246">如果满足子 **条件** 元素中指定的所有条件，则在 XML 节点内定义的映射将有效。</span><span class="sxs-lookup"><span data-stu-id="a9943-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="a9943-247">否则，该映射无效。</span><span class="sxs-lookup"><span data-stu-id="a9943-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="a9943-248">例如，如果 MappingFragment 元素包含一个或多个**Condition**子元素，则在满足子**条件**元素的所有条件时，在**MappingFragment**节点内定义的映射将有效。</span><span class="sxs-lookup"><span data-stu-id="a9943-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="a9943-249">每个条件都可以应用于\*\* (名称\*\*\*\*特性指定**的概念模型实体属性的名称) 或**columnname\*\* (列在数据库中的列名称，由**columnname**特性) 指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="a9943-250">设置 **Name** 属性时，将根据实体属性值检查条件。</span><span class="sxs-lookup"><span data-stu-id="a9943-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="a9943-251">设置 **ColumnName** 属性后，将根据列值检查条件。</span><span class="sxs-lookup"><span data-stu-id="a9943-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="a9943-252">只能在**Condition**元素中指定**Name**或**ColumnName**特性中的一个。</span><span class="sxs-lookup"><span data-stu-id="a9943-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-253">如果在 FunctionImportMapping 元素中使用 **Condition** 元素，则仅 **Name** 属性不适用。</span><span class="sxs-lookup"><span data-stu-id="a9943-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="a9943-254">**Condition**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="a9943-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="a9943-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="a9943-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-256">ComplexProperty</span></span>
-   <span data-ttu-id="a9943-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="a9943-257">EntitySetMapping</span></span>
-   <span data-ttu-id="a9943-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="a9943-258">MappingFragment</span></span>
-   <span data-ttu-id="a9943-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="a9943-259">EntityTypeMapping</span></span>

<span data-ttu-id="a9943-260">**Condition**元素不能有子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-261">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-261">Applicable Attributes</span></span>

<span data-ttu-id="a9943-262">下表描述了适用于 **Condition** 元素的属性：</span><span class="sxs-lookup"><span data-stu-id="a9943-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="a9943-263">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-263">Attribute Name</span></span> | <span data-ttu-id="a9943-264">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-264">Is Required</span></span> | <span data-ttu-id="a9943-265">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-266">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="a9943-266">**ColumnName**</span></span> | <span data-ttu-id="a9943-267">否</span><span class="sxs-lookup"><span data-stu-id="a9943-267">No</span></span>          | <span data-ttu-id="a9943-268">表列的名称，其值用于计算条件。</span><span class="sxs-lookup"><span data-stu-id="a9943-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="a9943-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="a9943-269">**IsNull**</span></span>     | <span data-ttu-id="a9943-270">否</span><span class="sxs-lookup"><span data-stu-id="a9943-270">No</span></span>          | <span data-ttu-id="a9943-271">**True** 或 **False**。</span><span class="sxs-lookup"><span data-stu-id="a9943-271">**True** or **False**.</span></span> <span data-ttu-id="a9943-272">如果值为 **True** ，列值为 **null**，或者如果值为 **False** 且列值不为 **null**，则条件为 true。</span><span class="sxs-lookup"><span data-stu-id="a9943-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="a9943-273">否则，条件为 False。</span><span class="sxs-lookup"><span data-stu-id="a9943-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="a9943-274">不能同时使用 **IsNull** 和 **Value** 属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="a9943-275">**值**</span><span class="sxs-lookup"><span data-stu-id="a9943-275">**Value**</span></span>      | <span data-ttu-id="a9943-276">否</span><span class="sxs-lookup"><span data-stu-id="a9943-276">No</span></span>          | <span data-ttu-id="a9943-277">要与列值进行比较的值。</span><span class="sxs-lookup"><span data-stu-id="a9943-277">The value with which the column value is compared.</span></span> <span data-ttu-id="a9943-278">如果值不同，则该条件为 True。</span><span class="sxs-lookup"><span data-stu-id="a9943-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="a9943-279">否则，条件为 False。</span><span class="sxs-lookup"><span data-stu-id="a9943-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="a9943-280">不能同时使用 **IsNull** 和 **Value** 属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="a9943-281">**Name**</span><span class="sxs-lookup"><span data-stu-id="a9943-281">**Name**</span></span>       | <span data-ttu-id="a9943-282">否</span><span class="sxs-lookup"><span data-stu-id="a9943-282">No</span></span>          | <span data-ttu-id="a9943-283">概念模型实体属性的名称，其值用于计算条件。</span><span class="sxs-lookup"><span data-stu-id="a9943-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="a9943-284">如果在 FunctionImportMapping 元素中使用 **Condition** 元素，则此属性不适用。</span><span class="sxs-lookup"><span data-stu-id="a9943-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="a9943-285">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-285">Example</span></span>

<span data-ttu-id="a9943-286">下面的示例将 **条件** 元素显示为 **MappingFragment** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="a9943-287">当 **"雇佣**日期" 不为 Null 并且**EnrollmentDate**为 null 时，将**在 SchoolModel**类型与**Person**表的**PersonID**和**雇用**日期列之间映射数据。</span><span class="sxs-lookup"><span data-stu-id="a9943-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="a9943-288">如果**EnrollmentDate**不为 Null 且**雇佣**日期为 null，则会在**SchoolModel**类型与**Person**表的**PersonID**和**注册**列之间映射数据。</span><span class="sxs-lookup"><span data-stu-id="a9943-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="a9943-289">DeleteFunction 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="a9943-290">映射规范语言 (MSL) 中的 **DeleteFunction** 元素将概念模型中的实体类型或关联的删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="a9943-291">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="a9943-292">有关详细信息，请参阅 Function 元素 (SSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-293">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="a9943-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="a9943-294">应用于 EntityTypeMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="a9943-295">当应用于 EntityTypeMapping 元素时， **DeleteFunction** 元素会将概念模型中的实体类型的删除函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="a9943-296">**DeleteFunction**元素在应用于**EntityTypeMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="a9943-297">AssociationEnd（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="a9943-298">ComplexProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="a9943-299">ScarlarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="a9943-300">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-300">Applicable Attributes</span></span>

<span data-ttu-id="a9943-301">下表描述了可应用于**EntityTypeMapping**元素的**DeleteFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="a9943-302">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-302">Attribute Name</span></span>            | <span data-ttu-id="a9943-303">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-303">Is Required</span></span> | <span data-ttu-id="a9943-304">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-305">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="a9943-305">**FunctionName**</span></span>          | <span data-ttu-id="a9943-306">是</span><span class="sxs-lookup"><span data-stu-id="a9943-306">Yes</span></span>         | <span data-ttu-id="a9943-307">删除函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="a9943-308">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="a9943-309">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="a9943-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="a9943-310">否</span><span class="sxs-lookup"><span data-stu-id="a9943-310">No</span></span>          | <span data-ttu-id="a9943-311">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="a9943-312">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-312">Example</span></span>

<span data-ttu-id="a9943-313">下面的示例基于 School 模型，并显示将**Person**实体类型的 delete 函数映射到**DeletePerson**存储过程的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="a9943-314">**DeletePerson**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="a9943-315">应用于 AssociationSetMapping 的 DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="a9943-316">当应用于 AssociationSetMapping 元素时， **DeleteFunction** 元素会将概念模型中的关联的删除函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="a9943-317">**DeleteFunction**元素在应用于**AssociationSetMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="a9943-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="a9943-319">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-319">Applicable Attributes</span></span>

<span data-ttu-id="a9943-320">下表描述了可应用于**AssociationSetMapping**元素的**DeleteFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="a9943-321">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-321">Attribute Name</span></span>            | <span data-ttu-id="a9943-322">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-322">Is Required</span></span> | <span data-ttu-id="a9943-323">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-324">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="a9943-324">**FunctionName**</span></span>          | <span data-ttu-id="a9943-325">是</span><span class="sxs-lookup"><span data-stu-id="a9943-325">Yes</span></span>         | <span data-ttu-id="a9943-326">删除函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="a9943-327">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="a9943-328">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="a9943-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="a9943-329">否</span><span class="sxs-lookup"><span data-stu-id="a9943-329">No</span></span>          | <span data-ttu-id="a9943-330">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="a9943-331">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-331">Example</span></span>

<span data-ttu-id="a9943-332">下面的示例基于 School 模型，并显示了用于将**courseinstructor.courseid**关联的删除函数映射到**DeleteCourseInstructor**存储过程的**DeleteFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="a9943-333">**DeleteCourseInstructor**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="a9943-334">EndProperty 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="a9943-335">映射规范语言 (MSL) 中的 **EndProperty** 元素定义概念模型关联和基础数据库的结束或修改函数之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="a9943-336">属性-列映射在子 ScalarProperty 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="a9943-337">当使用 **EndProperty** 元素定义概念模型关联末尾的映射时，它是 AssociationSetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="a9943-338">当使用 **EndProperty** 元素定义概念模型关联的修改函数的映射时，它是 InsertFunction 元素或 DeleteFunction 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="a9943-339">**EndProperty**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-340">ScalarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-341">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-341">Applicable Attributes</span></span>

<span data-ttu-id="a9943-342">下表描述了适用于 **EndProperty** 元素的属性：</span><span class="sxs-lookup"><span data-stu-id="a9943-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="a9943-343">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-343">Attribute Name</span></span> | <span data-ttu-id="a9943-344">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-344">Is Required</span></span> | <span data-ttu-id="a9943-345">值</span><span class="sxs-lookup"><span data-stu-id="a9943-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="a9943-346">名称</span><span class="sxs-lookup"><span data-stu-id="a9943-346">Name</span></span>           | <span data-ttu-id="a9943-347">是</span><span class="sxs-lookup"><span data-stu-id="a9943-347">Yes</span></span>         | <span data-ttu-id="a9943-348">要映射的关联端的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-349">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-349">Example</span></span>

<span data-ttu-id="a9943-350">下面的示例显示一个 **AssociationSetMapping** 元素，在该元素中，概念模型中的 **FK \_ 课程 \_ 部** 关联映射到数据库中的 **课程** 表。</span><span class="sxs-lookup"><span data-stu-id="a9943-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="a9943-351">在子 **EndProperty** 元素中指定关联类型属性与表列之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="a9943-352">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-352">Example</span></span>

<span data-ttu-id="a9943-353">下面的示例显示了 **EndProperty** 元素，该元素将关联 (**courseinstructor.courseid**) 的插入和删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="a9943-354">映射到的函数在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-354">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="a9943-355">EntityContainerMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-356">映射规范语言 (MSL) 中的 **EntityContainerMapping** 元素将概念模型中的实体容器映射到存储模型中的实体容器。</span><span class="sxs-lookup"><span data-stu-id="a9943-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="a9943-357">**EntityContainerMapping**元素是 Mapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="a9943-358">**EntityContainerMapping**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="a9943-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a9943-359">EntitySetMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="a9943-360">AssociationSetMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="a9943-361">FunctionImportMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-362">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-362">Applicable Attributes</span></span>

<span data-ttu-id="a9943-363">下表介绍可应用于 **EntityContainerMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="a9943-364">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-364">Attribute Name</span></span>            | <span data-ttu-id="a9943-365">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-365">Is Required</span></span> | <span data-ttu-id="a9943-366">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-367">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="a9943-367">**StorageModelContainer**</span></span> | <span data-ttu-id="a9943-368">是</span><span class="sxs-lookup"><span data-stu-id="a9943-368">Yes</span></span>         | <span data-ttu-id="a9943-369">要映射到的存储模型实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="a9943-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="a9943-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="a9943-371">是</span><span class="sxs-lookup"><span data-stu-id="a9943-371">Yes</span></span>         | <span data-ttu-id="a9943-372">要映射的概念模型实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="a9943-373">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="a9943-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="a9943-374">否</span><span class="sxs-lookup"><span data-stu-id="a9943-374">No</span></span>          | <span data-ttu-id="a9943-375">**True** 或 **False**。</span><span class="sxs-lookup"><span data-stu-id="a9943-375">**True** or **False**.</span></span> <span data-ttu-id="a9943-376">如果 **为 False**，则不生成任何更新视图。</span><span class="sxs-lookup"><span data-stu-id="a9943-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="a9943-377">如果你具有只读映射，则此属性应设置为 **False** ，因为数据可能无法成功往返。</span><span class="sxs-lookup"><span data-stu-id="a9943-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="a9943-378">默认值为 **True**。</span><span class="sxs-lookup"><span data-stu-id="a9943-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-379">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-379">Example</span></span>

<span data-ttu-id="a9943-380">下面的示例显示了 **EntityContainerMapping** 元素，该元素将概念模型实体容器) 的 **SchoolModelEntities** 容器 (映射到存储模型实体) 容器 (的 **SchoolModelStoreContainer** 容器：</span><span class="sxs-lookup"><span data-stu-id="a9943-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="a9943-381">EntitySetMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-382">映射规范语言 (MSL) 中的 **EntitySetMapping** 元素将概念模型实体集中的所有类型都映射到存储模型中的实体集。</span><span class="sxs-lookup"><span data-stu-id="a9943-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="a9943-383">概念模型中的实体集为同一类型（和派生类型）实体的实例的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="a9943-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="a9943-384">存储模型中的实体集表示基础数据库中的一个表或视图。</span><span class="sxs-lookup"><span data-stu-id="a9943-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="a9943-385">概念模型实体集由**EntitySetMapping**元素的**Name**特性的值指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="a9943-386">映射到的表或视图由每个子 MappingFragment 元素或**EntitySetMapping**元素本身中的**StoreEntitySet**属性指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="a9943-387">**EntitySetMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-388">EntityTypeMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="a9943-389">QueryView（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="a9943-390">MappingFragment（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-391">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-391">Applicable Attributes</span></span>

<span data-ttu-id="a9943-392">下表介绍可应用于 **EntitySetMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="a9943-393">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-393">Attribute Name</span></span>           | <span data-ttu-id="a9943-394">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-394">Is Required</span></span> | <span data-ttu-id="a9943-395">值</span><span class="sxs-lookup"><span data-stu-id="a9943-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-396">**名称**</span><span class="sxs-lookup"><span data-stu-id="a9943-396">**Name**</span></span>                 | <span data-ttu-id="a9943-397">是</span><span class="sxs-lookup"><span data-stu-id="a9943-397">Yes</span></span>         | <span data-ttu-id="a9943-398">要映射的概念模型实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="a9943-399">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="a9943-399">**TypeName** **1**</span></span>       | <span data-ttu-id="a9943-400">否</span><span class="sxs-lookup"><span data-stu-id="a9943-400">No</span></span>          | <span data-ttu-id="a9943-401">要映射的概念模型实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="a9943-402">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="a9943-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="a9943-403">否</span><span class="sxs-lookup"><span data-stu-id="a9943-403">No</span></span>          | <span data-ttu-id="a9943-404">要映射到的存储模型实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="a9943-405">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="a9943-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="a9943-406">否</span><span class="sxs-lookup"><span data-stu-id="a9943-406">No</span></span>          | <span data-ttu-id="a9943-407">**True** 或 **False** ，具体取决于是否只返回不同的行。</span><span class="sxs-lookup"><span data-stu-id="a9943-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="a9943-408">如果此特性设置为 **True**，则 EntityContainerMapping 元素的 **GenerateUpdateViews** 特性必须设置为 **False**。</span><span class="sxs-lookup"><span data-stu-id="a9943-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="a9943-409">**1** 可以使用 **TypeName** 和 **StoreEntitySet** 特性替代 EntityTypeMapping 和 MappingFragment 子元素，以便将单个实体类型映射到单个表。</span><span class="sxs-lookup"><span data-stu-id="a9943-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="a9943-410">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-410">Example</span></span>

<span data-ttu-id="a9943-411">下面的示例显示了 **EntitySetMapping** 元素，该元素将三个 (类型（在概念模型的 **课程** 实体集中) 为一个基类型和两个派生类型）映射到基础数据库中的三个不同表。</span><span class="sxs-lookup"><span data-stu-id="a9943-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="a9943-412">这些表由每个**MappingFragment**元素中的**StoreEntitySet**属性指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="a9943-413">EntityTypeMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-414">映射规范语言 (MSL) 中的 **EntityTypeMapping** 元素定义概念模型中的实体类型与基础数据库中的表或视图之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="a9943-415">有关概念模型实体类型与基础数据库表或视图的信息，请参见 EntityType 元素 (CSDL) 和 EntitySet 元素 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="a9943-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="a9943-416">要映射的概念模型实体类型由**EntityTypeMapping**元素的**TypeName**特性指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="a9943-417">要映射的表或视图由子 MappingFragment 元素的 **StoreEntitySet** 属性指定。</span><span class="sxs-lookup"><span data-stu-id="a9943-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="a9943-418">ModificationFunctionMapping 子元素可以用于将实体类型的插入、更新、或删除函数映射到数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="a9943-419">**EntityTypeMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-420">MappingFragment（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="a9943-421">ModificationFunctionMapping（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="a9943-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-422">ScalarProperty</span></span>
-   <span data-ttu-id="a9943-423">条件</span><span class="sxs-lookup"><span data-stu-id="a9943-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-424">**MappingFragment** 和 **ModificationFunctionMapping** 元素不能同时是 **EntityTypeMapping** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="a9943-425">当 **ScalarProperty** 和 **Condition** 元素在 FunctionImportMapping 元素中使用时，它们只能是 **EntityTypeMapping** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-426">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-426">Applicable Attributes</span></span>

<span data-ttu-id="a9943-427">下表介绍可应用于 **EntityTypeMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="a9943-428">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-428">Attribute Name</span></span> | <span data-ttu-id="a9943-429">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-429">Is Required</span></span> | <span data-ttu-id="a9943-430">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="a9943-431">**TypeName**</span></span>   | <span data-ttu-id="a9943-432">是</span><span class="sxs-lookup"><span data-stu-id="a9943-432">Yes</span></span>         | <span data-ttu-id="a9943-433">要映射的概念模型实体类型的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="a9943-434">如果类型为抽象类型或派生类型，则值必须为 `IsOfType(Namespace-qualified_type_name)`。</span><span class="sxs-lookup"><span data-stu-id="a9943-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-435">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-435">Example</span></span>

<span data-ttu-id="a9943-436">下面的示例演示一个具有两个子 **EntityTypeMapping** 元素的 EntitySetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="a9943-437">在第一个 **EntityTypeMapping** 元素中， **SchoolModel** 实体类型映射到 **person** 表。</span><span class="sxs-lookup"><span data-stu-id="a9943-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="a9943-438">在第二个 **EntityTypeMapping** 元素中， **SchoolModel** 类型的更新功能映射到数据库中的存储过程 **UpdatePerson**。</span><span class="sxs-lookup"><span data-stu-id="a9943-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="a9943-439">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-439">Example</span></span>

<span data-ttu-id="a9943-440">下一示例演示其中根类型为抽象类型的类型层次结构的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="a9943-441">请注意， `IsOfType` **TypeName** 特性的语法的用法。</span><span class="sxs-lookup"><span data-stu-id="a9943-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="a9943-442">FunctionImportMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-443">映射规范语言 (MSL) 中的 **FunctionImportMapping** 元素定义概念模型中的函数导入与基础数据库中的存储过程或函数之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="a9943-444">函数导入必须在概念模型中进行声明，存储的过程必须在存储模型中进行声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="a9943-445">有关详细信息，请参阅 FunctionImport 元素 (CSDL) 和 Function 元素 (SSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-446">默认情况下，如果函数导入返回概念模型实体类型或复杂类型，则基础存储过程返回的列名称必须与概念模型类型中的属性名称完全匹配。</span><span class="sxs-lookup"><span data-stu-id="a9943-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="a9943-447">如果列名称与属性名称不完全匹配，则必须在 ResultMapping 元素中定义映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="a9943-448">**FunctionImportMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-449">ResultMapping (零个或多个) </span><span class="sxs-lookup"><span data-stu-id="a9943-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-450">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-450">Applicable Attributes</span></span>

<span data-ttu-id="a9943-451">下表描述了适用于 **FunctionImportMapping** 元素的属性：</span><span class="sxs-lookup"><span data-stu-id="a9943-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="a9943-452">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-452">Attribute Name</span></span>         | <span data-ttu-id="a9943-453">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-453">Is Required</span></span> | <span data-ttu-id="a9943-454">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="a9943-455">**FunctionImportName**</span></span> | <span data-ttu-id="a9943-456">是</span><span class="sxs-lookup"><span data-stu-id="a9943-456">Yes</span></span>         | <span data-ttu-id="a9943-457">概念模型中要映射的函数导入的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="a9943-458">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="a9943-458">**FunctionName**</span></span>       | <span data-ttu-id="a9943-459">是</span><span class="sxs-lookup"><span data-stu-id="a9943-459">Yes</span></span>         | <span data-ttu-id="a9943-460">存储模型中要映射的函数的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-461">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-461">Example</span></span>

<span data-ttu-id="a9943-462">下面的示例基于 School 模型。</span><span class="sxs-lookup"><span data-stu-id="a9943-462">The following example is based on the School model.</span></span> <span data-ttu-id="a9943-463">请考虑在存储模型中使用以下函数：</span><span class="sxs-lookup"><span data-stu-id="a9943-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="a9943-464">另请考虑在概念模型中使用此函数导入：</span><span class="sxs-lookup"><span data-stu-id="a9943-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="a9943-465">下面的示例显示一个 **FunctionImportMapping** 元素，该元素用于将上述函数和函数导入映射到彼此：</span><span class="sxs-lookup"><span data-stu-id="a9943-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="a9943-466">InsertFunction 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="a9943-467">映射规范语言 (MSL) 中的 **InsertFunction** 元素将概念模型中的实体类型或关联的插入函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="a9943-468">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="a9943-469">有关详细信息，请参阅 Function 元素 (SSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-470">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="a9943-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="a9943-471">**InsertFunction**元素可以是 ModificationFunctionMapping 元素的子元素，并应用于 EntityTypeMapping 元素或 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="a9943-472">应用于 EntityTypeMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="a9943-473">当应用于 EntityTypeMapping 元素时， **InsertFunction** 元素会将概念模型中的实体类型的插入函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="a9943-474">**InsertFunction**元素在应用于**EntityTypeMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="a9943-475">AssociationEnd（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="a9943-476">ComplexProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="a9943-477">ResultBinding（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="a9943-478">ScarlarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="a9943-479">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-479">Applicable Attributes</span></span>

<span data-ttu-id="a9943-480">下表描述了应用于**EntityTypeMapping**元素时可应用于**InsertFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="a9943-481">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-481">Attribute Name</span></span>            | <span data-ttu-id="a9943-482">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-482">Is Required</span></span> | <span data-ttu-id="a9943-483">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-484">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="a9943-484">**FunctionName**</span></span>          | <span data-ttu-id="a9943-485">是</span><span class="sxs-lookup"><span data-stu-id="a9943-485">Yes</span></span>         | <span data-ttu-id="a9943-486">插入函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="a9943-487">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="a9943-488">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="a9943-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="a9943-489">否</span><span class="sxs-lookup"><span data-stu-id="a9943-489">No</span></span>          | <span data-ttu-id="a9943-490">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="a9943-491">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-491">Example</span></span>

<span data-ttu-id="a9943-492">下面的示例基于 School 模型，并显示了用于将 Person 实体类型的插入函数映射到**InsertPerson**存储过程的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="a9943-493">**InsertPerson**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="a9943-494">应用于 AssociationSetMapping 的 InsertFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="a9943-495">当应用于 AssociationSetMapping 元素时， **InsertFunction** 元素会将概念模型中的关联的插入函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="a9943-496">**InsertFunction**元素在应用于**AssociationSetMapping**元素时可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="a9943-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="a9943-498">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-498">Applicable Attributes</span></span>

<span data-ttu-id="a9943-499">下表描述了可应用于**AssociationSetMapping**元素的**InsertFunction**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="a9943-500">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-500">Attribute Name</span></span>            | <span data-ttu-id="a9943-501">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-501">Is Required</span></span> | <span data-ttu-id="a9943-502">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-503">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="a9943-503">**FunctionName**</span></span>          | <span data-ttu-id="a9943-504">是</span><span class="sxs-lookup"><span data-stu-id="a9943-504">Yes</span></span>         | <span data-ttu-id="a9943-505">插入函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="a9943-506">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="a9943-507">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="a9943-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="a9943-508">否</span><span class="sxs-lookup"><span data-stu-id="a9943-508">No</span></span>          | <span data-ttu-id="a9943-509">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="a9943-510">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-510">Example</span></span>

<span data-ttu-id="a9943-511">下面的示例基于 School 模型，并显示了用于将**courseinstructor.courseid**关联的插入函数映射到**InsertCourseInstructor**存储过程的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="a9943-512">**InsertCourseInstructor**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="a9943-513">Mapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="a9943-514">映射规范语言 (MSL) 中的 **mapping** 元素包含用于将概念模型中定义的对象映射到数据库 (的信息，如存储模型) 中所述。</span><span class="sxs-lookup"><span data-stu-id="a9943-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="a9943-515">有关详细信息，请参阅 CSDL 规范和 SSDL 规范。</span><span class="sxs-lookup"><span data-stu-id="a9943-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="a9943-516">**Mapping**元素是映射规范的根元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="a9943-517">映射规范的 XML 命名空间为 https://schemas.microsoft.com/ado/2009/11/mapping/cs 。</span><span class="sxs-lookup"><span data-stu-id="a9943-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="a9943-518">映射元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="a9943-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="a9943-519">Alias（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="a9943-520">EntityContainerMapping（恰好一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="a9943-521">MSL 中引用的概念模型类型和存储模型类型的名称必须由其相应的命名空间名称限定。</span><span class="sxs-lookup"><span data-stu-id="a9943-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="a9943-522">有关概念模型命名空间名称的信息，请参阅 (CSDL) 的架构元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="a9943-523">有关存储模型命名空间名称的信息，请参阅 (SSDL) 的架构元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="a9943-524">可以使用 Alias 元素定义 MSL 中使用的命名空间的别名。</span><span class="sxs-lookup"><span data-stu-id="a9943-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-525">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-525">Applicable Attributes</span></span>

<span data-ttu-id="a9943-526">下表描述了可应用到 **Mapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="a9943-527">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-527">Attribute Name</span></span> | <span data-ttu-id="a9943-528">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-528">Is Required</span></span> | <span data-ttu-id="a9943-529">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="a9943-530">**空间**</span><span class="sxs-lookup"><span data-stu-id="a9943-530">**Space**</span></span>      | <span data-ttu-id="a9943-531">是</span><span class="sxs-lookup"><span data-stu-id="a9943-531">Yes</span></span>         | <span data-ttu-id="a9943-532">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="a9943-532">**C-S**.</span></span> <span data-ttu-id="a9943-533">这是固定值，因此不能更改。</span><span class="sxs-lookup"><span data-stu-id="a9943-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-534">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-534">Example</span></span>

<span data-ttu-id="a9943-535">下面的示例演示一个基于 School 模型的一部分的 **映射** 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="a9943-536">有关 School 模型的详细信息，请参阅 "快速入门" (实体框架) ：</span><span class="sxs-lookup"><span data-stu-id="a9943-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="a9943-537">MappingFragment 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="a9943-538">映射规范语言 (MSL) 中的 **MappingFragment** 元素定义概念模型实体类型的属性与数据库中的表或视图之间的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="a9943-539">有关概念模型实体类型与基础数据库表或视图的信息，请参见 EntityType 元素 (CSDL) 和 EntitySet 元素 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="a9943-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="a9943-540">**MappingFragment**可以是 EntityTypeMapping 元素或 EntitySetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="a9943-541">**MappingFragment**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-542">ComplexType（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="a9943-543">ScalarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="a9943-544">Condition（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-545">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-545">Applicable Attributes</span></span>

<span data-ttu-id="a9943-546">下表介绍可应用于 **MappingFragment** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="a9943-547">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-547">Attribute Name</span></span>          | <span data-ttu-id="a9943-548">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-548">Is Required</span></span> | <span data-ttu-id="a9943-549">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-550">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="a9943-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="a9943-551">是</span><span class="sxs-lookup"><span data-stu-id="a9943-551">Yes</span></span>         | <span data-ttu-id="a9943-552">要映射的表或视图的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="a9943-553">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="a9943-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="a9943-554">否</span><span class="sxs-lookup"><span data-stu-id="a9943-554">No</span></span>          | <span data-ttu-id="a9943-555">**True** 或 **False** ，具体取决于是否只返回不同的行。</span><span class="sxs-lookup"><span data-stu-id="a9943-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="a9943-556">如果此特性设置为 **True**，则 EntityContainerMapping 元素的 **GenerateUpdateViews** 特性必须设置为 **False**。</span><span class="sxs-lookup"><span data-stu-id="a9943-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-557">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-557">Example</span></span>

<span data-ttu-id="a9943-558">下面的示例演示一个 **MappingFragment** 元素作为 **EntityTypeMapping** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="a9943-559">在此示例中，概念模型中的 **课程** 类型的属性将映射到数据库中 **课程** 表的列。</span><span class="sxs-lookup"><span data-stu-id="a9943-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="a9943-560">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-560">Example</span></span>

<span data-ttu-id="a9943-561">下面的示例演示一个 **MappingFragment** 元素作为 **EntitySetMapping** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="a9943-562">如上面的示例所示，概念模型中的 **课程** 类型的属性将映射到数据库中 **课程** 表的列。</span><span class="sxs-lookup"><span data-stu-id="a9943-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="a9943-563">ModificationFunctionMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-564">映射规范语言 (MSL) 中的 **ModificationFunctionMapping** 元素将概念模型实体类型的插入、更新和删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="a9943-565">**ModificationFunctionMapping**元素还可以将概念模型中的多对多关联的插入和删除函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="a9943-566">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="a9943-567">有关详细信息，请参阅 Function 元素 (SSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-568">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="a9943-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="a9943-569">如果将继承层次结构中的一个实体的修改函数映射到存储过程，则必须将该层次结构中所有类型的修改函数都映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="a9943-570">**ModificationFunctionMapping**元素可以是 EntityTypeMapping 元素或 AssociationSetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="a9943-571">**ModificationFunctionMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-572">DeleteFunction（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="a9943-573">InsertFunction（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="a9943-574">UpdateFunction（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="a9943-575">没有适用于 **ModificationFunctionMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="a9943-576">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-576">Example</span></span>

<span data-ttu-id="a9943-577">下面的示例演示了 School 模型中 " **人员** " 实体集的实体集映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="a9943-578">除了 **person** 实体类型的列映射外，还会显示 " **人员** " 类型的插入、更新和删除函数的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="a9943-579">映射到的函数在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-579">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="a9943-580">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-580">Example</span></span>

<span data-ttu-id="a9943-581">下面的示例演示了 School 模型中 **courseinstructor.courseid** 关联集的关联集映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="a9943-582">除了 **courseinstructor.courseid** 关联的列映射外，还会显示 **courseinstructor.courseid** 关联的插入和删除函数的映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="a9943-583">映射到的函数在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-583">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="a9943-584">QueryView 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="a9943-585">映射规范语言 (MSL) 中的 **QueryView** 元素定义概念模型中的实体类型或关联与基础数据库中的表之间的只读映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="a9943-586">映射是使用针对存储模型计算的实体 SQL 查询定义的，你可以根据概念模型中的实体或关联来表示结果集。</span><span class="sxs-lookup"><span data-stu-id="a9943-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="a9943-587">因为查询视图是只读的，所以不能使用标准更新命令来更新查询视图所定义的类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="a9943-588">可以使用修改函数来更新这些类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="a9943-589">有关详细信息，请参阅如何：将修改函数映射到存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-590">在 **QueryView** 元素中，不支持实体 SQL 包含 **GroupBy**、组聚合或导航属性的表达式。</span><span class="sxs-lookup"><span data-stu-id="a9943-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="a9943-591">**QueryView**元素可以是 EntitySetMapping 元素或 AssociationSetMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="a9943-592">在前一种情况中，查询视图定义概念模型中实体的只读映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="a9943-593">在后一种情况中，查询视图定义概念模型中关联的只读映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-594">如果 **AssociationSetMapping** 元素用于与引用约束的关联，则忽略 **AssociationSetMapping** 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="a9943-595">有关详细信息，请参阅 ReferentialConstraint 元素 (CSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="a9943-596">**QueryView**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-597">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-597">Applicable Attributes</span></span>

<span data-ttu-id="a9943-598">下表介绍可应用于 **QueryView** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="a9943-599">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-599">Attribute Name</span></span> | <span data-ttu-id="a9943-600">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-600">Is Required</span></span> | <span data-ttu-id="a9943-601">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="a9943-602">**TypeName**</span></span>   | <span data-ttu-id="a9943-603">否</span><span class="sxs-lookup"><span data-stu-id="a9943-603">No</span></span>          | <span data-ttu-id="a9943-604">要由查询视图映射的概念模型类型的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-605">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-605">Example</span></span>

<span data-ttu-id="a9943-606">下面的示例将 **QueryView** 元素显示为 **EntitySetMapping** 元素的子元素，并为 School 模型中的 **部门** 实体类型定义查询视图映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="a9943-607">由于该查询仅返回存储模型中 " **部门** " 类型的成员子集，因此，School 模型中的 **部门** 类型已根据以下映射进行了修改：</span><span class="sxs-lookup"><span data-stu-id="a9943-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="a9943-608">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-608">Example</span></span>

<span data-ttu-id="a9943-609">下一个示例显示了 **QueryView** 元素作为 **AssociationSetMapping** 元素的子元素，并为 School 模型中的关联定义了一个只读映射 `FK_Course_Department` 。</span><span class="sxs-lookup"><span data-stu-id="a9943-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="a9943-610">注释</span><span class="sxs-lookup"><span data-stu-id="a9943-610">Comments</span></span>

<span data-ttu-id="a9943-611">可以定义查询视图来实现以下方案：</span><span class="sxs-lookup"><span data-stu-id="a9943-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="a9943-612">在概念模型中定义一个实体，该实体不包含存储模型中的实体的所有属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="a9943-613">这包括没有默认值并且不支持 **null** 值的属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="a9943-614">将存储模型中计算的列映射到概念模型中实体类型的属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="a9943-615">定义一个映射，其中用于对概念模型中的实体进行分区的条件不基于相等性。</span><span class="sxs-lookup"><span data-stu-id="a9943-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="a9943-616">使用 **Condition** 元素指定条件映射时，所提供的条件必须等于指定的值。</span><span class="sxs-lookup"><span data-stu-id="a9943-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="a9943-617">有关详细信息，请参阅 Condition 元素 (MSL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="a9943-618">将存储模型中的同一列映射到概念模型中的多个类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="a9943-619">将多个类型映射到同一个表。</span><span class="sxs-lookup"><span data-stu-id="a9943-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="a9943-620">在概念模型中定义不基于关系架构中的外键的关联。</span><span class="sxs-lookup"><span data-stu-id="a9943-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="a9943-621">使用自定义业务逻辑设置概念模型中的属性值。</span><span class="sxs-lookup"><span data-stu-id="a9943-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="a9943-622">例如，您可以将数据源中的字符串值 "T" 映射到概念模型中的 **true**值（布尔值）。</span><span class="sxs-lookup"><span data-stu-id="a9943-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="a9943-623">为查询结果定义条件筛选器。</span><span class="sxs-lookup"><span data-stu-id="a9943-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="a9943-624">强制对概念模型中的数据施加比存储模型中更少的限制。</span><span class="sxs-lookup"><span data-stu-id="a9943-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="a9943-625">例如，您可以将概念模型中的属性设置为可以为 null，即使它映射到的列不支持 **null**值。</span><span class="sxs-lookup"><span data-stu-id="a9943-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="a9943-626">为实体定义查询视图时需要考虑以下注意事项：</span><span class="sxs-lookup"><span data-stu-id="a9943-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="a9943-627">查询视图是只读的。</span><span class="sxs-lookup"><span data-stu-id="a9943-627">Query views are read-only.</span></span> <span data-ttu-id="a9943-628">只能使用修改函数来更新实体。</span><span class="sxs-lookup"><span data-stu-id="a9943-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="a9943-629">通过查询视图定义实体类型时，必须也通过查询视图来定义所有相关实体。</span><span class="sxs-lookup"><span data-stu-id="a9943-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="a9943-630">当您将多对多关联映射到存储模型中表示关系架构中的链接表的实体时，必须在此链接表的**AssociationSetMapping**元素中定义**QueryView**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="a9943-631">必须为类型层次结构中的所有类型定义查询视图。</span><span class="sxs-lookup"><span data-stu-id="a9943-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="a9943-632">可以使用下列方式来实现：</span><span class="sxs-lookup"><span data-stu-id="a9943-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="a9943-633">使用单个 **QueryView** 元素指定单个实体 SQL 查询，该查询返回层次结构中所有实体类型的联合。</span><span class="sxs-lookup"><span data-stu-id="a9943-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="a9943-634">使用单个 **QueryView** 元素，该元素指定单个实体 SQL 查询，该查询使用 CASE 运算符根据特定条件返回层次结构中的特定实体类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="a9943-635">对于层次结构中的特定类型，使用额外的 **QueryView** 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="a9943-636">在这种情况下，请使用**QueryView**元素的**TypeName**特性来指定每个视图的实体类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="a9943-637">定义查询视图时，不能在**EntitySetMapping**元素上指定**StorageSetName**属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="a9943-638">定义查询视图时， **EntitySetMapping**元素不能同时包含 **属性** 映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="a9943-639">ResultBinding 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="a9943-640">映射规范语言 (MSL) 中的 **ResultBinding** 元素将存储过程返回的列值映射到概念模型中的实体属性，同时将实体类型修改函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="a9943-641">例如，当 insert 存储过程返回标识列的值时， **ResultBinding** 元素会将返回的值映射到概念模型中的实体类型属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="a9943-642">**ResultBinding**元素可以是 InsertFunction 元素或 UpdateFunction 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="a9943-643">**ResultBinding**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-644">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-644">Applicable Attributes</span></span>

<span data-ttu-id="a9943-645">下表描述了适用于 **ResultBinding** 元素的属性：</span><span class="sxs-lookup"><span data-stu-id="a9943-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="a9943-646">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-646">Attribute Name</span></span> | <span data-ttu-id="a9943-647">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-647">Is Required</span></span> | <span data-ttu-id="a9943-648">值</span><span class="sxs-lookup"><span data-stu-id="a9943-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-649">**名称**</span><span class="sxs-lookup"><span data-stu-id="a9943-649">**Name**</span></span>       | <span data-ttu-id="a9943-650">是</span><span class="sxs-lookup"><span data-stu-id="a9943-650">Yes</span></span>         | <span data-ttu-id="a9943-651">概念模型中要映射的实体属性的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="a9943-652">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="a9943-652">**ColumnName**</span></span> | <span data-ttu-id="a9943-653">是</span><span class="sxs-lookup"><span data-stu-id="a9943-653">Yes</span></span>         | <span data-ttu-id="a9943-654">要映射的列的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="a9943-655">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-655">Example</span></span>

<span data-ttu-id="a9943-656">下面的示例基于 School 模型，并显示用于将**Person**实体类型的插入函数映射到**InsertPerson**存储过程的**InsertFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="a9943-657"> (**InsertPerson** 存储过程如下所示，并在存储模型中声明。 ) **ResultBinding** 元素用于将存储过程 (**NewPersonID**) 返回的列值映射到 (**PersonID**) 的实体类型属性。</span><span class="sxs-lookup"><span data-stu-id="a9943-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="a9943-658">以下 Transact-sql 介绍了 **InsertPerson** 存储过程：</span><span class="sxs-lookup"><span data-stu-id="a9943-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="a9943-659">ResultMapping 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="a9943-660">映射规范语言 (MSL) 中的 **ResultMapping** 元素定义概念模型中的函数导入与基础数据库中的存储过程之间的映射（如果满足以下条件）：</span><span class="sxs-lookup"><span data-stu-id="a9943-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="a9943-661">函数导入返回一个概念模型实体类型或复杂类型。</span><span class="sxs-lookup"><span data-stu-id="a9943-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="a9943-662">存储过程返回的列的名称与实体类型或复杂类型中的属性名称不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="a9943-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="a9943-663">默认情况下，存储过程返回的列与实体类型或复杂类型中的属性之间的映射基于列名称和属性名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="a9943-664">如果列名称与属性名称不完全匹配，则必须使用 **ResultMapping** 元素来定义映射。</span><span class="sxs-lookup"><span data-stu-id="a9943-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="a9943-665">有关默认映射的示例，请参阅 FunctionImportMapping 元素 (MSL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="a9943-666">**ResultMapping**元素是 FunctionImportMapping 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="a9943-667">**ResultMapping**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-668">EntityTypeMapping（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="a9943-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="a9943-669">ComplexTypeMapping</span></span>

<span data-ttu-id="a9943-670">没有适用于 **ResultMapping** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="a9943-671">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-671">Example</span></span>

<span data-ttu-id="a9943-672">请思考以下存储过程：</span><span class="sxs-lookup"><span data-stu-id="a9943-672">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="a9943-673">另请考虑使用以下概念模型实体类型：</span><span class="sxs-lookup"><span data-stu-id="a9943-673">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="a9943-674">若要创建返回以前实体类型的实例的函数导入，必须在 **ResultMapping** 元素中定义存储过程返回的列与实体类型返回的列之间的映射：</span><span class="sxs-lookup"><span data-stu-id="a9943-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="a9943-675">ScalarProperty 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="a9943-676">映射规范语言 (MSL) 中的 **ScalarProperty** 元素将概念模型实体类型、复杂类型或关联的属性映射到基础数据库中的表列或存储过程参数。</span><span class="sxs-lookup"><span data-stu-id="a9943-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="a9943-677">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="a9943-678">有关详细信息，请参阅 Function 元素 (SSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="a9943-679">**ScalarProperty**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="a9943-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="a9943-680">MappingFragment</span></span>
-   <span data-ttu-id="a9943-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-681">InsertFunction</span></span>
-   <span data-ttu-id="a9943-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-682">UpdateFunction</span></span>
-   <span data-ttu-id="a9943-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="a9943-683">DeleteFunction</span></span>
-   <span data-ttu-id="a9943-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-684">EndProperty</span></span>
-   <span data-ttu-id="a9943-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="a9943-685">ComplexProperty</span></span>
-   <span data-ttu-id="a9943-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="a9943-686">ResultMapping</span></span>

<span data-ttu-id="a9943-687">作为 **MappingFragment**、 **ComplexProperty**或 **EndProperty** 元素的子元素， **ScalarProperty** 元素会将概念模型中的属性映射到数据库中的某一列。</span><span class="sxs-lookup"><span data-stu-id="a9943-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="a9943-688">作为 **InsertFunction**、 **UpdateFunction**或 **DeleteFunction** 元素的子元素， **ScalarProperty** 元素会将概念模型中的属性映射到存储过程参数。</span><span class="sxs-lookup"><span data-stu-id="a9943-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="a9943-689">**ScalarProperty**元素不能具有任何子元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-690">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-690">Applicable Attributes</span></span>

<span data-ttu-id="a9943-691">应用于 **ScalarProperty** 元素的特性因元素的角色而异。</span><span class="sxs-lookup"><span data-stu-id="a9943-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="a9943-692">下表介绍了在 **ScalarProperty** 元素用于将概念模型属性映射到数据库中的列时适用的特性：</span><span class="sxs-lookup"><span data-stu-id="a9943-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="a9943-693">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-693">Attribute Name</span></span> | <span data-ttu-id="a9943-694">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-694">Is Required</span></span> | <span data-ttu-id="a9943-695">值</span><span class="sxs-lookup"><span data-stu-id="a9943-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="a9943-696">**名称**</span><span class="sxs-lookup"><span data-stu-id="a9943-696">**Name**</span></span>       | <span data-ttu-id="a9943-697">是</span><span class="sxs-lookup"><span data-stu-id="a9943-697">Yes</span></span>         | <span data-ttu-id="a9943-698">要映射的概念模型属性的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="a9943-699">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="a9943-699">**ColumnName**</span></span> | <span data-ttu-id="a9943-700">是</span><span class="sxs-lookup"><span data-stu-id="a9943-700">Yes</span></span>         | <span data-ttu-id="a9943-701">要映射的表列的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="a9943-702">下表描述了当 **ScalarProperty** 元素用于将概念模型属性映射到存储过程参数时适用的特性：</span><span class="sxs-lookup"><span data-stu-id="a9943-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="a9943-703">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-703">Attribute Name</span></span>    | <span data-ttu-id="a9943-704">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-704">Is Required</span></span> | <span data-ttu-id="a9943-705">值</span><span class="sxs-lookup"><span data-stu-id="a9943-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-706">**名称**</span><span class="sxs-lookup"><span data-stu-id="a9943-706">**Name**</span></span>          | <span data-ttu-id="a9943-707">是</span><span class="sxs-lookup"><span data-stu-id="a9943-707">Yes</span></span>         | <span data-ttu-id="a9943-708">要映射的概念模型属性的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="a9943-709">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="a9943-709">**ParameterName**</span></span> | <span data-ttu-id="a9943-710">是</span><span class="sxs-lookup"><span data-stu-id="a9943-710">Yes</span></span>         | <span data-ttu-id="a9943-711">正在映射的参数的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="a9943-712">**Version**</span><span class="sxs-lookup"><span data-stu-id="a9943-712">**Version**</span></span>       | <span data-ttu-id="a9943-713">否</span><span class="sxs-lookup"><span data-stu-id="a9943-713">No</span></span>          | <span data-ttu-id="a9943-714">**当前** 或 **原始** 值，具体取决于是否应将当前值或属性的原始值用于并发检查。</span><span class="sxs-lookup"><span data-stu-id="a9943-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="a9943-715">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-715">Example</span></span>

<span data-ttu-id="a9943-716">下面的示例演示了两种方法中使用的 **ScalarProperty** 元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="a9943-717">将 **person** 实体类型的属性映射到 **person**表的列。</span><span class="sxs-lookup"><span data-stu-id="a9943-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="a9943-718">将 **Person** 实体类型的属性映射到 **UpdatePerson** 存储过程的参数。</span><span class="sxs-lookup"><span data-stu-id="a9943-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="a9943-719">存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-719">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="a9943-720">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-720">Example</span></span>

<span data-ttu-id="a9943-721">下一个示例显示了 **ScalarProperty** 元素，该元素用于将概念模型关联的插入和删除函数映射到数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="a9943-722">存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-722">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="a9943-723">UpdateFunction 元素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="a9943-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="a9943-724">映射规范语言 (MSL) 中的 **UpdateFunction** 元素将概念模型中的实体类型的更新函数映射到基础数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="a9943-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="a9943-725">将修改函数映射到其中的存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="a9943-726">有关详细信息，请参阅 Function 元素 (SSDL) 。</span><span class="sxs-lookup"><span data-stu-id="a9943-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="a9943-727">如果没有将实体类型的所有三个插入、更新或删除操作映射到存储过程，则在运行时执行并引发 UpdateException 时，未映射的操作将失败。</span><span class="sxs-lookup"><span data-stu-id="a9943-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="a9943-728">**UpdateFunction**元素可以是 ModificationFunctionMapping 元素的子元素并应用于 EntityTypeMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="a9943-729">**UpdateFunction**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="a9943-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="a9943-730">AssociationEnd（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="a9943-731">ComplexProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="a9943-732">ResultBinding（零个或一个）</span><span class="sxs-lookup"><span data-stu-id="a9943-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="a9943-733">ScarlarProperty（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="a9943-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="a9943-734">适用的属性</span><span class="sxs-lookup"><span data-stu-id="a9943-734">Applicable Attributes</span></span>

<span data-ttu-id="a9943-735">下表介绍可应用于 **UpdateFunction** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="a9943-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="a9943-736">属性名称</span><span class="sxs-lookup"><span data-stu-id="a9943-736">Attribute Name</span></span>            | <span data-ttu-id="a9943-737">是否必需</span><span class="sxs-lookup"><span data-stu-id="a9943-737">Is Required</span></span> | <span data-ttu-id="a9943-738">“值”</span><span class="sxs-lookup"><span data-stu-id="a9943-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a9943-739">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="a9943-739">**FunctionName**</span></span>          | <span data-ttu-id="a9943-740">是</span><span class="sxs-lookup"><span data-stu-id="a9943-740">Yes</span></span>         | <span data-ttu-id="a9943-741">更新函数要映射到的存储过程的命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="a9943-742">存储过程必须在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="a9943-743">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="a9943-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="a9943-744">否</span><span class="sxs-lookup"><span data-stu-id="a9943-744">No</span></span>          | <span data-ttu-id="a9943-745">返回受影响行数的输出参数的名称。</span><span class="sxs-lookup"><span data-stu-id="a9943-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="a9943-746">示例</span><span class="sxs-lookup"><span data-stu-id="a9943-746">Example</span></span>

<span data-ttu-id="a9943-747">下面的示例基于 School 模型，并显示用于将**Person**实体类型的更新函数映射到**UpdatePerson**存储过程的**UpdateFunction**元素。</span><span class="sxs-lookup"><span data-stu-id="a9943-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="a9943-748">**UpdatePerson**存储过程在存储模型中声明。</span><span class="sxs-lookup"><span data-stu-id="a9943-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
