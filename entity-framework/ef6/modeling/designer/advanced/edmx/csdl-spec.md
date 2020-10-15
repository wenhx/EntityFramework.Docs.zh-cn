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
# <a name="csdl-specification"></a><span data-ttu-id="36f91-103">CSDL 规范</span><span class="sxs-lookup"><span data-stu-id="36f91-103">CSDL Specification</span></span>
<span data-ttu-id="36f91-104">概念架构定义语言 (CSDL) 是一种基于 XML 的语言，它描述构成数据驱动应用程序的概念模型的实体、关系和函数。</span><span class="sxs-lookup"><span data-stu-id="36f91-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="36f91-105">此概念模型可由实体框架或 WCF 数据服务使用。</span><span class="sxs-lookup"><span data-stu-id="36f91-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="36f91-106">实体框架使用 CSDL 描述的元数据将概念模型中定义的实体和关系映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="36f91-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="36f91-107">有关详细信息，请参阅 [SSDL 规范](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) 和 [MSL 规范](xref:ef6/modeling/designer/advanced/edmx/msl-spec)。</span><span class="sxs-lookup"><span data-stu-id="36f91-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="36f91-108">CSDL 是实体数据模型的实体框架实现。</span><span class="sxs-lookup"><span data-stu-id="36f91-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="36f91-109">在实体框架应用程序中，概念模型的元数据将从使用) CSDL 编写的 csdl 文件 (加载到 EdmItemCollection 的实例中，并且可通过使用 System.web 类中的方法进行访问。</span><span class="sxs-lookup"><span data-stu-id="36f91-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="36f91-110">实体框架使用概念模型元数据将针对概念模型的查询转换为特定于数据源的命令。</span><span class="sxs-lookup"><span data-stu-id="36f91-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="36f91-111">EF 设计器会在设计时将概念模型信息存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="36f91-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="36f91-112">在生成时，EF 设计器使用 .edmx 文件中的信息来创建运行时实体框架所需的 csdl 文件。</span><span class="sxs-lookup"><span data-stu-id="36f91-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="36f91-113">CSDL 的版本按 XML 命名空间进行区分。</span><span class="sxs-lookup"><span data-stu-id="36f91-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="36f91-114">CSDL 版本</span><span class="sxs-lookup"><span data-stu-id="36f91-114">CSDL Version</span></span> | <span data-ttu-id="36f91-115">XML 命名空间</span><span class="sxs-lookup"><span data-stu-id="36f91-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="36f91-116">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="36f91-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="36f91-117">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="36f91-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="36f91-118">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="36f91-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="36f91-119">Association 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-119">Association Element (CSDL)</span></span>

<span data-ttu-id="36f91-120">**Association**元素定义了两个实体类型之间的关系。</span><span class="sxs-lookup"><span data-stu-id="36f91-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="36f91-121">关联必须指定关系中涉及的实体类型和关系的每一端可能的实体类型数量（也称为重数）。</span><span class="sxs-lookup"><span data-stu-id="36f91-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="36f91-122">关联端的多重性的 (值可以是 1) 、零或一个 (0 .. 1) 或多个 (\*) 。</span><span class="sxs-lookup"><span data-stu-id="36f91-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="36f91-123">此信息在两个 End 子元素中指定。</span><span class="sxs-lookup"><span data-stu-id="36f91-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="36f91-124">通过导航属性或外键（如果在实体类型上公开了外键）可以访问关联一端的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="36f91-125">在应用程序中，关联的实例表示实体类型的实例之间的特定关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="36f91-126">关联实例按逻辑分组在关联集中。</span><span class="sxs-lookup"><span data-stu-id="36f91-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="36f91-127">**Association**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-128">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-129">End（正好两个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="36f91-130">ReferentialConstraint（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="36f91-131">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-132">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-132">Applicable Attributes</span></span>

<span data-ttu-id="36f91-133">下表描述了可应用于 **Association** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="36f91-134">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-134">Attribute Name</span></span> | <span data-ttu-id="36f91-135">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-135">Is Required</span></span> | <span data-ttu-id="36f91-136">值</span><span class="sxs-lookup"><span data-stu-id="36f91-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="36f91-137">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-137">**Name**</span></span>       | <span data-ttu-id="36f91-138">是</span><span class="sxs-lookup"><span data-stu-id="36f91-138">Yes</span></span>         | <span data-ttu-id="36f91-139">关联的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-140">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **Association** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="36f91-141">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-142">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-143">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-143">Example</span></span>

<span data-ttu-id="36f91-144">下面的示例演示一个**association**元素，该元素定义在**Customer**和**Order**实体类型上没有公开外键时的**CustomerOrders**关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="36f91-145">关联的每个**端点**的**多重性**值指示可以将多个**订单**与一个**客户**关联，但只能有一个**客户**与一个**订单**相关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="36f91-146">此外， **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。</span><span class="sxs-lookup"><span data-stu-id="36f91-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="36f91-147">下面的示例演示一个**association**元素，该元素定义在**Customer**和**Order**实体类型上公开外键时的**CustomerOrders**关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="36f91-148">公开外键后，实体之间的关系将通过 **ReferentialConstraint** 元素进行管理。</span><span class="sxs-lookup"><span data-stu-id="36f91-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="36f91-149">将此关联映射到数据源并不需要 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="36f91-150">AssociationSet 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="36f91-151">概念架构定义语言 (CSDL) 中的 **AssociationSet** 元素是同一类型的关联实例的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="36f91-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="36f91-152">关联集为对关联实例进行分组提供了定义，以便能够将它们映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="36f91-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="36f91-153">**AssociationSet**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-154">Documentation（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="36f91-155">End（需要正好两个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="36f91-156">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="36f91-157">**Association**特性指定关联集包含的关联类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="36f91-158">构成关联集末尾的实体集是通过恰好两个子 **End** 元素指定的。</span><span class="sxs-lookup"><span data-stu-id="36f91-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-159">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-159">Applicable Attributes</span></span>

<span data-ttu-id="36f91-160">下表介绍可应用于 **AssociationSet** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="36f91-161">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-161">Attribute Name</span></span>  | <span data-ttu-id="36f91-162">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-162">Is Required</span></span> | <span data-ttu-id="36f91-163">值</span><span class="sxs-lookup"><span data-stu-id="36f91-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-164">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-164">**Name**</span></span>        | <span data-ttu-id="36f91-165">是</span><span class="sxs-lookup"><span data-stu-id="36f91-165">Yes</span></span>         | <span data-ttu-id="36f91-166">实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-166">The name of the entity set.</span></span> <span data-ttu-id="36f91-167">**Name**特性的值不能与**Association**特性的值相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="36f91-168">**关联**</span><span class="sxs-lookup"><span data-stu-id="36f91-168">**Association**</span></span> | <span data-ttu-id="36f91-169">是</span><span class="sxs-lookup"><span data-stu-id="36f91-169">Yes</span></span>         | <span data-ttu-id="36f91-170">关联集包含其实例的关联的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="36f91-171">关联必须与关联集位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="36f91-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-172">任意数量的批注属性 (自定义 XML 特性) 可以应用于 **AssociationSet** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="36f91-173">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-174">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-175">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-175">Example</span></span>

<span data-ttu-id="36f91-176">下面的示例演示具有两个**AssociationSet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="36f91-177">CollectionType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="36f91-178">概念架构定义语言 (CSDL) 中的 **CollectionType** 元素指定函数参数或函数返回类型是集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="36f91-179">**CollectionType**元素可以是参数元素的子元素，也可以是 ReturnType (函数) 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="36f91-180">可以使用 **type** 特性或以下子元素之一指定集合类型：</span><span class="sxs-lookup"><span data-stu-id="36f91-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="36f91-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="36f91-181">**CollectionType**</span></span>
-   <span data-ttu-id="36f91-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="36f91-182">ReferenceType</span></span>
-   <span data-ttu-id="36f91-183">RowType</span><span class="sxs-lookup"><span data-stu-id="36f91-183">RowType</span></span>
-   <span data-ttu-id="36f91-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="36f91-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-185">如果集合类型是使用 **type** 特性和子元素指定的，则模型不会进行验证。</span><span class="sxs-lookup"><span data-stu-id="36f91-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-186">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-186">Applicable Attributes</span></span>

<span data-ttu-id="36f91-187">下表介绍可应用于 **CollectionType** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="36f91-188">请注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **精度**、 **小数位数**、 **Unicode**和 **排序规则** 特性仅适用于 **edmsimpletype**的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="36f91-189">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-189">Attribute Name</span></span>                                                          | <span data-ttu-id="36f91-190">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-190">Is Required</span></span> | <span data-ttu-id="36f91-191">值</span><span class="sxs-lookup"><span data-stu-id="36f91-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-192">**类型**</span><span class="sxs-lookup"><span data-stu-id="36f91-192">**Type**</span></span>                                                                | <span data-ttu-id="36f91-193">否</span><span class="sxs-lookup"><span data-stu-id="36f91-193">No</span></span>          | <span data-ttu-id="36f91-194">集合的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="36f91-195">**可以为 Null**</span><span class="sxs-lookup"><span data-stu-id="36f91-195">**Nullable**</span></span>                                                            | <span data-ttu-id="36f91-196">否</span><span class="sxs-lookup"><span data-stu-id="36f91-196">No</span></span>          | <span data-ttu-id="36f91-197">**True**（默认值）或 **False**，取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="36f91-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="36f91-198">在 CSDL v1 中 >，复杂类型属性必须具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="36f91-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="36f91-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="36f91-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="36f91-200">否</span><span class="sxs-lookup"><span data-stu-id="36f91-200">No</span></span>          | <span data-ttu-id="36f91-201">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="36f91-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="36f91-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="36f91-203">否</span><span class="sxs-lookup"><span data-stu-id="36f91-203">No</span></span>          | <span data-ttu-id="36f91-204">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="36f91-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="36f91-206">否</span><span class="sxs-lookup"><span data-stu-id="36f91-206">No</span></span>          | <span data-ttu-id="36f91-207">**True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="36f91-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="36f91-208">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-208">**Precision**</span></span>                                                           | <span data-ttu-id="36f91-209">否</span><span class="sxs-lookup"><span data-stu-id="36f91-209">No</span></span>          | <span data-ttu-id="36f91-210">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="36f91-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="36f91-211">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-211">**Scale**</span></span>                                                               | <span data-ttu-id="36f91-212">否</span><span class="sxs-lookup"><span data-stu-id="36f91-212">No</span></span>          | <span data-ttu-id="36f91-213">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="36f91-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="36f91-214">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-214">**SRID**</span></span>                                                                | <span data-ttu-id="36f91-215">否</span><span class="sxs-lookup"><span data-stu-id="36f91-215">No</span></span>          | <span data-ttu-id="36f91-216">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="36f91-217">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="36f91-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="36f91-218">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="36f91-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="36f91-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="36f91-219">**Unicode**</span></span>                                                             | <span data-ttu-id="36f91-220">否</span><span class="sxs-lookup"><span data-stu-id="36f91-220">No</span></span>          | <span data-ttu-id="36f91-221">**True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="36f91-222">**排序规则**</span><span class="sxs-lookup"><span data-stu-id="36f91-222">**Collation**</span></span>                                                           | <span data-ttu-id="36f91-223">否</span><span class="sxs-lookup"><span data-stu-id="36f91-223">No</span></span>          | <span data-ttu-id="36f91-224">指定要在数据源中使用的排序的字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="36f91-225">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **CollectionType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="36f91-226">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-227">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-228">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-228">Example</span></span>

<span data-ttu-id="36f91-229">下面的示例演示一个模型定义函数，该函数使用 **CollectionType** 元素来指定该函数返回 **Person** 类型的集合，该集合 (使用 **ElementType** 特性) 指定。</span><span class="sxs-lookup"><span data-stu-id="36f91-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="36f91-230">下面的示例演示一个模型定义函数，该函数使用 **CollectionType** 元素来指定该函数返回 (在 **RowType** 元素) 中指定的行的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="36f91-231">下面的示例演示一个模型定义的函数，该函数使用 **CollectionType** 元素来指定该函数接受作为参数的 **部门** 实体类型的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="36f91-232">ComplexType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="36f91-233">**ComplexType**元素定义由**EdmSimpleType**属性或其他复杂类型组成的数据结构。</span><span class="sxs-lookup"><span data-stu-id="36f91-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="36f91-234">复杂类型可以是实体类型的属性或其他复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="36f91-235">复杂类型类似于复杂类型定义数据中的实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="36f91-236">但是，在复杂类型与实体类型之间仍存在着一些重要区别：</span><span class="sxs-lookup"><span data-stu-id="36f91-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="36f91-237">复杂类型没有标识（或键），因此不能独立存在。</span><span class="sxs-lookup"><span data-stu-id="36f91-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="36f91-238">复杂类型只能作为实体类型或其他复杂类型的属性而存在。</span><span class="sxs-lookup"><span data-stu-id="36f91-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="36f91-239">复杂类型不能参与关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="36f91-240">关联的任一端都不能是复杂类型，因此不能为复杂类型定义导航属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="36f91-241">复杂类型属性不能具有 null 值，但可以将复杂类型的每个标量属性设置为 null。</span><span class="sxs-lookup"><span data-stu-id="36f91-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="36f91-242">**ComplexType**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-243">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-244">Property（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="36f91-245">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="36f91-246">下表介绍可应用于 **ComplexType** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="36f91-247">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="36f91-248">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-248">Is Required</span></span> | <span data-ttu-id="36f91-249">值</span><span class="sxs-lookup"><span data-stu-id="36f91-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-250">名称</span><span class="sxs-lookup"><span data-stu-id="36f91-250">Name</span></span>                                                                                                           | <span data-ttu-id="36f91-251">是</span><span class="sxs-lookup"><span data-stu-id="36f91-251">Yes</span></span>         | <span data-ttu-id="36f91-252">复杂类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-252">The name of the complex type.</span></span> <span data-ttu-id="36f91-253">复杂类型的名称不能与模型作用域中的其他复杂类型、实体类型或关联的名称相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="36f91-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="36f91-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="36f91-255">否</span><span class="sxs-lookup"><span data-stu-id="36f91-255">No</span></span>          | <span data-ttu-id="36f91-256">作为所定义的复杂类型的基类型的另一个复杂类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="36f91-257">> 此属性在 CSDL v1 中不适用。</span><span class="sxs-lookup"><span data-stu-id="36f91-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="36f91-258">在该版本中不支持复杂类型的继承。</span><span class="sxs-lookup"><span data-stu-id="36f91-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="36f91-259">摘要</span><span class="sxs-lookup"><span data-stu-id="36f91-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="36f91-260">否</span><span class="sxs-lookup"><span data-stu-id="36f91-260">No</span></span>          | <span data-ttu-id="36f91-261">**True** 或 **False** (默认值) 取决于复杂类型是否为抽象类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="36f91-262">> 此属性在 CSDL v1 中不适用。</span><span class="sxs-lookup"><span data-stu-id="36f91-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="36f91-263">该版本中的复杂类型不能是抽象类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="36f91-264">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **ComplexType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="36f91-265">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-266">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-267">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-267">Example</span></span>

<span data-ttu-id="36f91-268">下面的示例显示了一个复杂类型 **Address**，其中包含 **EdmSimpleType** 属性 **StreetAddress**、 **City**、 **StateOrProvince**、 **国家/地区**和 **邮政编码**。</span><span class="sxs-lookup"><span data-stu-id="36f91-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="36f91-269">若要将上述复杂类型 **地址** 定义 () 为某个实体类型的属性，必须在实体类型定义中声明该属性类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="36f91-270">下面的示例将 **Address** 属性显示为) 的实体类型 (**发布服务器** 上的复杂类型：</span><span class="sxs-lookup"><span data-stu-id="36f91-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="36f91-271">DefiningExpression 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="36f91-272">概念架构定义语言 (CSDL) 中的 **DefiningExpression** 元素包含一个实体 SQL 定义概念模型中的函数的表达式。</span><span class="sxs-lookup"><span data-stu-id="36f91-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="36f91-273">出于验证目的， **DefiningExpression** 元素可以包含任意内容。</span><span class="sxs-lookup"><span data-stu-id="36f91-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="36f91-274">但是，如果 **DefiningExpression** 元素不包含有效的实体 SQL，则实体框架在运行时将引发异常。</span><span class="sxs-lookup"><span data-stu-id="36f91-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-275">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-275">Applicable Attributes</span></span>

<span data-ttu-id="36f91-276">可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **DefiningExpression** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="36f91-277">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-278">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-279">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-279">Example</span></span>

<span data-ttu-id="36f91-280">下面的示例使用 **DefiningExpression** 元素来定义一个函数，该函数返回发布书籍之后的年数。</span><span class="sxs-lookup"><span data-stu-id="36f91-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="36f91-281">**DefiningExpression**元素的内容是以实体 SQL 编写的。</span><span class="sxs-lookup"><span data-stu-id="36f91-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="36f91-282">Dependent 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="36f91-283">概念架构定义语言中 (CSDL) 的 **依赖** 元素是 ReferentialConstraint 元素的子元素，用于定义引用约束的依赖端。</span><span class="sxs-lookup"><span data-stu-id="36f91-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="36f91-284">**ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。</span><span class="sxs-lookup"><span data-stu-id="36f91-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="36f91-285">与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="36f91-286">引用的实体类型称为约束的 *主体端* 。</span><span class="sxs-lookup"><span data-stu-id="36f91-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="36f91-287">引用主体端的实体类型称为约束的 *依赖端* 。</span><span class="sxs-lookup"><span data-stu-id="36f91-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="36f91-288">**PropertyRef** 元素用于指定哪些键引用主体端。</span><span class="sxs-lookup"><span data-stu-id="36f91-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="36f91-289">**依赖**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-290">PropertyRef（一个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="36f91-291">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-292">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-292">Applicable Attributes</span></span>

<span data-ttu-id="36f91-293">下表描述了可应用于 **依赖** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="36f91-294">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-294">Attribute Name</span></span> | <span data-ttu-id="36f91-295">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-295">Is Required</span></span> | <span data-ttu-id="36f91-296">值</span><span class="sxs-lookup"><span data-stu-id="36f91-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="36f91-297">**角色**</span><span class="sxs-lookup"><span data-stu-id="36f91-297">**Role**</span></span>       | <span data-ttu-id="36f91-298">是</span><span class="sxs-lookup"><span data-stu-id="36f91-298">Yes</span></span>         | <span data-ttu-id="36f91-299">关联的依赖端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-300">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **依赖** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="36f91-301">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-302">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-303">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-303">Example</span></span>

<span data-ttu-id="36f91-304">下面的示例演示了在**PublishedBy**关联的定义中使用的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="36f91-305">**Book**实体类型的**PublisherId**属性构成了引用约束的依赖端。</span><span class="sxs-lookup"><span data-stu-id="36f91-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="36f91-306">Documentation 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="36f91-307">概念架构定义语言 (CSDL) 中的 **文档** 元素可用于提供有关在父元素中定义的对象的信息。</span><span class="sxs-lookup"><span data-stu-id="36f91-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="36f91-308">在 .edmx 文件中，当 **文档** 元素是元素的子元素（该元素显示为 EF 设计器设计图面上的一个对象） (如实体、关联或属性) ）时， **文档** 元素的内容将显示在该对象的 Visual Studio " **属性** " 窗口中。</span><span class="sxs-lookup"><span data-stu-id="36f91-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="36f91-309">**文档**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-310">**摘要**：父元素的简要说明。</span><span class="sxs-lookup"><span data-stu-id="36f91-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="36f91-311">（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-311">(zero or one element)</span></span>
-   <span data-ttu-id="36f91-312">**LongDescription**：父元素的详细说明。</span><span class="sxs-lookup"><span data-stu-id="36f91-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="36f91-313">（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-313">(zero or one element)</span></span>
-   <span data-ttu-id="36f91-314">批注元素.</span><span class="sxs-lookup"><span data-stu-id="36f91-314">Annotation elements.</span></span> <span data-ttu-id="36f91-315">（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-316">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-316">Applicable Attributes</span></span>

<span data-ttu-id="36f91-317">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **文档** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="36f91-318">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-319">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-320">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-320">Example</span></span>

<span data-ttu-id="36f91-321">下面的示例将 **文档** 元素显示为 EntityType 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="36f91-322">如果下面的代码片段在 .edmx 文件的 CSDL 内容中，则当您单击该实体类型时， **Summary** 和 **LongDescription** 元素的内容将显示在 Visual Studio 的 " **属性** " 窗口中 `Customer` 。</span><span class="sxs-lookup"><span data-stu-id="36f91-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="36f91-323">End 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-323">End Element (CSDL)</span></span>

<span data-ttu-id="36f91-324">概念架构定义语言中 (CSDL) 的 **结束** 元素可以是 Association 元素或 AssociationSet 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="36f91-325">在每种情况下， **End** 元素的角色不同，适用的属性也不同。</span><span class="sxs-lookup"><span data-stu-id="36f91-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="36f91-326">End 元素作为 Association 元素的子元素</span><span class="sxs-lookup"><span data-stu-id="36f91-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="36f91-327">**End**元素 (作为**association**元素的子元素，) 标识关联一端的实体类型以及该关联端可以存在的实体类型实例的数量。</span><span class="sxs-lookup"><span data-stu-id="36f91-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="36f91-328">关联端定义为关联的一部分；关联必须正好有两个关联端。</span><span class="sxs-lookup"><span data-stu-id="36f91-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="36f91-329">通过导航属性或外键（如果在实体类型上公开了外键）可以访问关联一端的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="36f91-330">**End**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-331">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-332">OnDelete（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="36f91-333">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="36f91-334">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-334">Applicable Attributes</span></span>

<span data-ttu-id="36f91-335">下表描述了当 **结束** 元素是 **Association** 元素的子元素时，可应用于该元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="36f91-336">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-336">Attribute Name</span></span>   | <span data-ttu-id="36f91-337">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-337">Is Required</span></span> | <span data-ttu-id="36f91-338">值</span><span class="sxs-lookup"><span data-stu-id="36f91-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-339">类型</span><span class="sxs-lookup"><span data-stu-id="36f91-339">**Type**</span></span>         | <span data-ttu-id="36f91-340">是</span><span class="sxs-lookup"><span data-stu-id="36f91-340">Yes</span></span>         | <span data-ttu-id="36f91-341">关联一端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="36f91-342">**角色**</span><span class="sxs-lookup"><span data-stu-id="36f91-342">**Role**</span></span>         | <span data-ttu-id="36f91-343">否</span><span class="sxs-lookup"><span data-stu-id="36f91-343">No</span></span>          | <span data-ttu-id="36f91-344">关联端的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-344">A name for the association end.</span></span> <span data-ttu-id="36f91-345">如果不提供名称，将使用关联端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="36f91-346">**多重性**</span><span class="sxs-lookup"><span data-stu-id="36f91-346">**Multiplicity**</span></span> | <span data-ttu-id="36f91-347">是</span><span class="sxs-lookup"><span data-stu-id="36f91-347">Yes</span></span>         | <span data-ttu-id="36f91-348">**1**、 **0 或 1**或， **\*** 具体取决于可在关联末尾的实体类型实例的数量。</span><span class="sxs-lookup"><span data-stu-id="36f91-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="36f91-349">**1** 指示关联端刚好存在一个实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="36f91-350">**0 .0** 表示在关联端存在零个或一个实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="36f91-351">**\*** 指示在关联端存在零个、一个或多个实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-352">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **结束** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="36f91-353">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-354">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="36f91-355">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-355">Example</span></span>

<span data-ttu-id="36f91-356">下面的示例演示了一个定义**CustomerOrders**关联的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="36f91-357">关联的每个**端点**的**多重性**值指示可以将多个**订单**与一个**客户**关联，但只能有一个**客户**与一个**订单**相关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="36f91-358">此外， **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。</span><span class="sxs-lookup"><span data-stu-id="36f91-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="36f91-359">End 元素作为 AssociationSet 元素的子元素</span><span class="sxs-lookup"><span data-stu-id="36f91-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="36f91-360">**End**元素指定关联集的一端。</span><span class="sxs-lookup"><span data-stu-id="36f91-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="36f91-361">**AssociationSet**元素必须包含两个**结束**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="36f91-362">**End**元素中包含的信息用于将关联集映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="36f91-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="36f91-363">**End**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-364">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-365">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-366">Annotation 元素必须出现在所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="36f91-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="36f91-367">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="36f91-368">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-368">Applicable Attributes</span></span>

<span data-ttu-id="36f91-369">下表描述了当 **结束** 元素为 **AssociationSet** 元素的子元素时，可应用于该元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="36f91-370">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-370">Attribute Name</span></span> | <span data-ttu-id="36f91-371">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-371">Is Required</span></span> | <span data-ttu-id="36f91-372">值</span><span class="sxs-lookup"><span data-stu-id="36f91-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="36f91-373">**EntitySet**</span></span>  | <span data-ttu-id="36f91-374">是</span><span class="sxs-lookup"><span data-stu-id="36f91-374">Yes</span></span>         | <span data-ttu-id="36f91-375">定义父**AssociationSet**元素一端的**EntitySet**元素的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="36f91-376">必须在与父**AssociationSet**元素相同的实体容器中定义**EntitySet**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="36f91-377">**角色**</span><span class="sxs-lookup"><span data-stu-id="36f91-377">**Role**</span></span>       | <span data-ttu-id="36f91-378">否</span><span class="sxs-lookup"><span data-stu-id="36f91-378">No</span></span>          | <span data-ttu-id="36f91-379">关联集端的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-379">The name of the association set end.</span></span> <span data-ttu-id="36f91-380">如果未使用 **Role** 属性，则关联集端的名称将为实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="36f91-381">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **结束** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="36f91-382">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-383">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="36f91-384">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-384">Example</span></span>

<span data-ttu-id="36f91-385">下面的示例演示具有两个**AssociationSet**元素的**EntityContainer**元素，每个元素都有两个**End**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="36f91-386">EntityContainer 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="36f91-387">概念架构定义语言 (CSDL) 中的 **EntityContainer** 元素是实体集、关联集和函数导入的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="36f91-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="36f91-388">概念模型实体容器通过 EntityContainerMapping 元素映射到存储模型实体容器。</span><span class="sxs-lookup"><span data-stu-id="36f91-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="36f91-389">存储模型实体容器描述数据库的结构：实体集描述表、关联集描述外键约束、函数导入描述数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="36f91-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="36f91-390">**EntityContainer**元素可以有零个或一个文档元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="36f91-391">如果存在 **文档** 元素，则它必须位于所有 **EntitySet**、 **AssociationSet**和 **FunctionImport** 元素之前。</span><span class="sxs-lookup"><span data-stu-id="36f91-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="36f91-392">**EntityContainer**元素可以具有零个或多个下列子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="36f91-393">EntitySet</span></span>
-   <span data-ttu-id="36f91-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="36f91-394">AssociationSet</span></span>
-   <span data-ttu-id="36f91-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="36f91-395">FunctionImport</span></span>
-   <span data-ttu-id="36f91-396">批注元素</span><span class="sxs-lookup"><span data-stu-id="36f91-396">Annotation elements</span></span>

<span data-ttu-id="36f91-397">可以扩展 **EntityContainer** 元素，使其包含同一命名空间中的另一个 **entitycontainer** 的内容。</span><span class="sxs-lookup"><span data-stu-id="36f91-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="36f91-398">若要包含另一个 **entitycontainer**的内容，请在引用 **entitycontainer** 元素中，将 " **扩展** " 属性的值设置为要包括的 **EntityContainer** 元素的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="36f91-399">包含的 **entitycontainer** 元素的所有子元素将被视为引用 **entitycontainer** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-400">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-400">Applicable Attributes</span></span>

<span data-ttu-id="36f91-401">下表描述了可应用于 **Using** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="36f91-402">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-402">Attribute Name</span></span> | <span data-ttu-id="36f91-403">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-403">Is Required</span></span> | <span data-ttu-id="36f91-404">值</span><span class="sxs-lookup"><span data-stu-id="36f91-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="36f91-405">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-405">**Name**</span></span>       | <span data-ttu-id="36f91-406">是</span><span class="sxs-lookup"><span data-stu-id="36f91-406">Yes</span></span>         | <span data-ttu-id="36f91-407">实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="36f91-408">**延续**</span><span class="sxs-lookup"><span data-stu-id="36f91-408">**Extends**</span></span>    | <span data-ttu-id="36f91-409">否</span><span class="sxs-lookup"><span data-stu-id="36f91-409">No</span></span>          | <span data-ttu-id="36f91-410">同一命名空间中另一实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-411">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **EntityContainer** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="36f91-412">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-413">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-414">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-414">Example</span></span>

<span data-ttu-id="36f91-415">下面的示例演示一个 **EntityContainer** 元素，该元素定义三个实体集和两个关联集。</span><span class="sxs-lookup"><span data-stu-id="36f91-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="36f91-416">EntitySet 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="36f91-417">概念架构定义语言中的 **EntitySet** 元素是某个实体类型的实例和从该实体类型派生的任何类型的实例的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="36f91-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="36f91-418">实体类型与实体集之间的关系类似于关系数据库中行与表之间的关系。</span><span class="sxs-lookup"><span data-stu-id="36f91-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="36f91-419">实体类型与行类似，它定义一组相关数据；而实体集与表类似，它包含该定义的实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="36f91-420">实体集为对实体类型实例分组提供了一个构造，以便能够将它们映射到数据源中的相关数据结构。</span><span class="sxs-lookup"><span data-stu-id="36f91-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="36f91-421">可以为特定实体类型定义多个实体集。</span><span class="sxs-lookup"><span data-stu-id="36f91-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-422">EF 设计器不支持每个类型包含多个实体集的概念模型。</span><span class="sxs-lookup"><span data-stu-id="36f91-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="36f91-423">**EntitySet**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-424">Documentation 元素（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="36f91-425">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-426">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-426">Applicable Attributes</span></span>

<span data-ttu-id="36f91-427">下表描述了可应用于 **EntitySet** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="36f91-428">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-428">Attribute Name</span></span> | <span data-ttu-id="36f91-429">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-429">Is Required</span></span> | <span data-ttu-id="36f91-430">值</span><span class="sxs-lookup"><span data-stu-id="36f91-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-431">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-431">**Name**</span></span>       | <span data-ttu-id="36f91-432">是</span><span class="sxs-lookup"><span data-stu-id="36f91-432">Yes</span></span>         | <span data-ttu-id="36f91-433">实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="36f91-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="36f91-434">**EntityType**</span></span> | <span data-ttu-id="36f91-435">是</span><span class="sxs-lookup"><span data-stu-id="36f91-435">Yes</span></span>         | <span data-ttu-id="36f91-436">实体集包含其实例的实体类型的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-437">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **EntitySet** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="36f91-438">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-439">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-440">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-440">Example</span></span>

<span data-ttu-id="36f91-441">下面的示例演示具有三个**EntitySet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="36f91-442">每个类型可以定义多个实体集 (MEST)。</span><span class="sxs-lookup"><span data-stu-id="36f91-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="36f91-443">下面的示例定义了一个实体容器，其中包含 **书籍** 实体类型的两个实体集：</span><span class="sxs-lookup"><span data-stu-id="36f91-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="36f91-444">EntityType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="36f91-445">**EntityType**元素表示概念模型中的顶级概念（例如客户或订单）的结构。</span><span class="sxs-lookup"><span data-stu-id="36f91-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="36f91-446">实体类型是应用程序中实体类型实例的模板。</span><span class="sxs-lookup"><span data-stu-id="36f91-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="36f91-447">每个模板都包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="36f91-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="36f91-448">唯一名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-448">A unique name.</span></span> <span data-ttu-id="36f91-449">（必选。）</span><span class="sxs-lookup"><span data-stu-id="36f91-449">(Required.)</span></span>
-   <span data-ttu-id="36f91-450">由一个或多个属性定义的实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="36f91-451">（必选。）</span><span class="sxs-lookup"><span data-stu-id="36f91-451">(Required.)</span></span>
-   <span data-ttu-id="36f91-452">用于包含数据的属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-452">Properties for containing data.</span></span> <span data-ttu-id="36f91-453">（可选。）</span><span class="sxs-lookup"><span data-stu-id="36f91-453">(Optional.)</span></span>
-   <span data-ttu-id="36f91-454">导航属性，用于从关联的一端导航至另一端。</span><span class="sxs-lookup"><span data-stu-id="36f91-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="36f91-455">（可选。）</span><span class="sxs-lookup"><span data-stu-id="36f91-455">(Optional.)</span></span>

<span data-ttu-id="36f91-456">在应用程序中，实体类型的实例表示一个特定对象（例如特定客户或订单）。</span><span class="sxs-lookup"><span data-stu-id="36f91-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="36f91-457">实体类型的每个实例在实体集中都必须具有唯一的实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="36f91-458">只有两个实体类型实例的类型相同且其实体键的值也相同时，才认为它们是相等的。</span><span class="sxs-lookup"><span data-stu-id="36f91-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="36f91-459">**EntityType**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-460">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-461">Key（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="36f91-462">Property（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="36f91-463">NavigationProperty（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="36f91-464">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-465">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-465">Applicable Attributes</span></span>

<span data-ttu-id="36f91-466">下表介绍可应用于 **EntityType** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="36f91-467">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="36f91-468">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-468">Is Required</span></span> | <span data-ttu-id="36f91-469">值</span><span class="sxs-lookup"><span data-stu-id="36f91-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-470">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="36f91-471">是</span><span class="sxs-lookup"><span data-stu-id="36f91-471">Yes</span></span>         | <span data-ttu-id="36f91-472">实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="36f91-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="36f91-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="36f91-474">否</span><span class="sxs-lookup"><span data-stu-id="36f91-474">No</span></span>          | <span data-ttu-id="36f91-475">作为正在定义的实体类型的基类型的另一个实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="36f91-476">**抽象**</span><span class="sxs-lookup"><span data-stu-id="36f91-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="36f91-477">否</span><span class="sxs-lookup"><span data-stu-id="36f91-477">No</span></span>          | <span data-ttu-id="36f91-478">**True** 或 **False**，具体取决于实体类型是否为抽象类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="36f91-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="36f91-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="36f91-480">否</span><span class="sxs-lookup"><span data-stu-id="36f91-480">No</span></span>          | <span data-ttu-id="36f91-481">**True** 或 **False** ，具体取决于实体类型是否为开放式实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="36f91-482">> **OpenType** 特性仅适用于在与 ADO.NET Data Services 一起使用的概念模型中定义的实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="36f91-483">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **EntityType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="36f91-484">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-485">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-486">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-486">Example</span></span>

<span data-ttu-id="36f91-487">下面的示例演示一个具有三个**属性**元素和两个**NavigationProperty**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="36f91-488">EnumType 元素 (CSDL) </span><span class="sxs-lookup"><span data-stu-id="36f91-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="36f91-489">**EnumType**元素表示一个枚举类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="36f91-490">**EnumType**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-491">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-492">成员 (零个或多个元素) </span><span class="sxs-lookup"><span data-stu-id="36f91-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="36f91-493">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-494">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-494">Applicable Attributes</span></span>

<span data-ttu-id="36f91-495">下表介绍可应用于 **EnumType** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="36f91-496">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-496">Attribute Name</span></span>     | <span data-ttu-id="36f91-497">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-497">Is Required</span></span> | <span data-ttu-id="36f91-498">值</span><span class="sxs-lookup"><span data-stu-id="36f91-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-499">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-499">**Name**</span></span>           | <span data-ttu-id="36f91-500">是</span><span class="sxs-lookup"><span data-stu-id="36f91-500">Yes</span></span>         | <span data-ttu-id="36f91-501">实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="36f91-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="36f91-502">**IsFlags**</span></span>        | <span data-ttu-id="36f91-503">否</span><span class="sxs-lookup"><span data-stu-id="36f91-503">No</span></span>          | <span data-ttu-id="36f91-504">**True** 或 **False**，具体取决于枚举类型是否可用作一组标志。</span><span class="sxs-lookup"><span data-stu-id="36f91-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="36f91-505">默认值为**False。**</span><span class="sxs-lookup"><span data-stu-id="36f91-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="36f91-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="36f91-506">**UnderlyingType**</span></span> | <span data-ttu-id="36f91-507">否</span><span class="sxs-lookup"><span data-stu-id="36f91-507">No</span></span>          | <span data-ttu-id="36f91-508">**Edm**、 **edm**、 **edm**、 **edm** 或 **edm** ，定义类型的值的范围的。</span><span class="sxs-lookup"><span data-stu-id="36f91-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="36f91-509">枚举元素的默认基础类型为 **Edm**。</span><span class="sxs-lookup"><span data-stu-id="36f91-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-510">可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **EnumType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="36f91-511">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-512">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-513">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-513">Example</span></span>

<span data-ttu-id="36f91-514">下面的示例演示具有三个**成员**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="36f91-515">Function 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-515">Function Element (CSDL)</span></span>

<span data-ttu-id="36f91-516">概念架构定义语言中 (CSDL) 的 **函数** 元素用于在概念模型中定义或声明函数。</span><span class="sxs-lookup"><span data-stu-id="36f91-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="36f91-517">函数通过使用 DefiningExpression 元素来定义。</span><span class="sxs-lookup"><span data-stu-id="36f91-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="36f91-518">**函数**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-519">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-520">Parameter（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="36f91-521">DefiningExpression（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="36f91-522">ReturnType (函数)  (零个或一个元素) </span><span class="sxs-lookup"><span data-stu-id="36f91-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="36f91-523">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="36f91-524">函数的返回类型必须与 **returntype** (函数) 元素或 **returntype** 属性一起指定 (参见下面) ，但不能同时指定两者。</span><span class="sxs-lookup"><span data-stu-id="36f91-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="36f91-525">可能的返回类型为任何 EdmSimpleType、实体类型、复杂类型、行类型或引用类型（或这些类型之一的集合）。</span><span class="sxs-lookup"><span data-stu-id="36f91-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-526">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-526">Applicable Attributes</span></span>

<span data-ttu-id="36f91-527">下表描述了可应用于 **Function** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="36f91-528">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-528">Attribute Name</span></span> | <span data-ttu-id="36f91-529">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-529">Is Required</span></span> | <span data-ttu-id="36f91-530">值</span><span class="sxs-lookup"><span data-stu-id="36f91-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="36f91-531">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-531">**Name**</span></span>       | <span data-ttu-id="36f91-532">是</span><span class="sxs-lookup"><span data-stu-id="36f91-532">Yes</span></span>         | <span data-ttu-id="36f91-533">函数名。</span><span class="sxs-lookup"><span data-stu-id="36f91-533">The name of the function.</span></span>          |
| <span data-ttu-id="36f91-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="36f91-534">**ReturnType**</span></span> | <span data-ttu-id="36f91-535">否</span><span class="sxs-lookup"><span data-stu-id="36f91-535">No</span></span>          | <span data-ttu-id="36f91-536">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-537">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Function** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="36f91-538">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-539">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-540">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-540">Example</span></span>

<span data-ttu-id="36f91-541">下面的示例使用 **function** 元素来定义一个函数，该函数返回一个指导员后的年数。</span><span class="sxs-lookup"><span data-stu-id="36f91-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="36f91-542">FunctionImport 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="36f91-543">概念架构定义语言 (CSDL) 中的 **FunctionImport** 元素表示在数据源中定义但可通过概念模型用于对象的函数。</span><span class="sxs-lookup"><span data-stu-id="36f91-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="36f91-544">例如，存储模型中的 Function 元素可用于表示数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="36f91-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="36f91-545">概念模型中的 **FunctionImport** 元素表示实体框架应用程序中的相应函数并通过使用 FunctionImportMapping 元素映射到存储模型函数。</span><span class="sxs-lookup"><span data-stu-id="36f91-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="36f91-546">在应用程序中调用函数时，会在数据库中执行相应的存储过程。</span><span class="sxs-lookup"><span data-stu-id="36f91-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="36f91-547">**FunctionImport**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-548">Documentation（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="36f91-549">Parameter（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="36f91-550">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="36f91-551">ReturnType (FunctionImport)  (允许零个或多个元素) </span><span class="sxs-lookup"><span data-stu-id="36f91-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="36f91-552">应为函数接受的每个参数定义一个 **参数** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="36f91-553">函数的返回类型必须与 **returntype** (FunctionImport) 元素或 **returntype** 属性一起指定 (参见下面) ，但不能同时指定两者。</span><span class="sxs-lookup"><span data-stu-id="36f91-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="36f91-554">返回类型值必须是 EdmSimpleType、EntityType 或 ComplexType 的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-555">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-555">Applicable Attributes</span></span>

<span data-ttu-id="36f91-556">下表介绍可应用于 **FunctionImport** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="36f91-557">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-557">Attribute Name</span></span>   | <span data-ttu-id="36f91-558">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-558">Is Required</span></span> | <span data-ttu-id="36f91-559">值</span><span class="sxs-lookup"><span data-stu-id="36f91-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-560">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-560">**Name**</span></span>         | <span data-ttu-id="36f91-561">是</span><span class="sxs-lookup"><span data-stu-id="36f91-561">Yes</span></span>         | <span data-ttu-id="36f91-562">导入的函数的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="36f91-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="36f91-563">**ReturnType**</span></span>   | <span data-ttu-id="36f91-564">否</span><span class="sxs-lookup"><span data-stu-id="36f91-564">No</span></span>          | <span data-ttu-id="36f91-565">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-565">The type that the function returns.</span></span> <span data-ttu-id="36f91-566">如果函数未返回值，则不要使用此属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="36f91-567">否则，该值必须是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="36f91-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="36f91-568">**EntitySet**</span></span>    | <span data-ttu-id="36f91-569">否</span><span class="sxs-lookup"><span data-stu-id="36f91-569">No</span></span>          | <span data-ttu-id="36f91-570">如果函数返回实体类型的集合，则 **EntitySet** 的值必须是该集合所属的实体集。</span><span class="sxs-lookup"><span data-stu-id="36f91-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="36f91-571">否则，不得使用 **EntitySet** 属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="36f91-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="36f91-572">**IsComposable**</span></span> | <span data-ttu-id="36f91-573">否</span><span class="sxs-lookup"><span data-stu-id="36f91-573">No</span></span>          | <span data-ttu-id="36f91-574">如果将此值设置为 true，则函数可组合 (表值函数) 并可在 LINQ 查询中使用。</span><span class="sxs-lookup"><span data-stu-id="36f91-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="36f91-575">默认值为 **false**。</span><span class="sxs-lookup"><span data-stu-id="36f91-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="36f91-576">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **FunctionImport** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="36f91-577">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-578">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-579">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-579">Example</span></span>

<span data-ttu-id="36f91-580">下面的示例演示一个 **FunctionImport** 元素，该元素接受一个参数并返回实体类型的集合：</span><span class="sxs-lookup"><span data-stu-id="36f91-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="36f91-581">Key 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-581">Key Element (CSDL)</span></span>

<span data-ttu-id="36f91-582">**Key**元素是 EntityType 元素的子元素，用于*定义实体类型 (确定*标识) 的实体类型的属性或一组属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="36f91-583">构成实体键的属性是在设计时选择的。</span><span class="sxs-lookup"><span data-stu-id="36f91-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="36f91-584">实体键属性的值必须在运行时唯一标识实体集中的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="36f91-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="36f91-585">在选择构成实体键的属性时应确保实例在实体集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="36f91-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="36f91-586">**Key**元素通过引用实体类型的一个或多个属性来定义实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="36f91-587">**Key**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="36f91-588">PropertyRef（一个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="36f91-589">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-590">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-590">Applicable Attributes</span></span>

<span data-ttu-id="36f91-591">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **该键** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="36f91-592">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-593">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-594">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-594">Example</span></span>

<span data-ttu-id="36f91-595">下面的示例定义了一个名为 **Book**的实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="36f91-596">实体键通过引用实体类型的 **ISBN** 属性来定义。</span><span class="sxs-lookup"><span data-stu-id="36f91-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="36f91-597">**Isbn**属性是实体键的一个不错选择，因为国际标准图书号码 (ISBN) 唯一标识书。</span><span class="sxs-lookup"><span data-stu-id="36f91-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="36f91-598">下面的示例演示了一个实体类型 (**作者**) ，其中包含一个由两个属性、 **名称** 和 **地址**组成的实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="36f91-599">使用实体键的 **名称** 和 **地址** 是合理的选择，因为同一名称的两个作者不会居住在同一地址。</span><span class="sxs-lookup"><span data-stu-id="36f91-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="36f91-600">但是，针对实体键的这种选择并不能绝对确保实体键在实体集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="36f91-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="36f91-601">在这种情况下，建议添加可用于唯一标识作者的属性，例如 **AuthorId**：。</span><span class="sxs-lookup"><span data-stu-id="36f91-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="36f91-602"> (CSDL) 成员元素</span><span class="sxs-lookup"><span data-stu-id="36f91-602">Member Element (CSDL)</span></span>

<span data-ttu-id="36f91-603">**Member**元素是 EnumType 元素的子元素，用于定义枚举类型的成员。</span><span class="sxs-lookup"><span data-stu-id="36f91-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-604">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-604">Applicable Attributes</span></span>

<span data-ttu-id="36f91-605">下表介绍可应用于 **FunctionImport** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="36f91-606">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-606">Attribute Name</span></span> | <span data-ttu-id="36f91-607">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-607">Is Required</span></span> | <span data-ttu-id="36f91-608">值</span><span class="sxs-lookup"><span data-stu-id="36f91-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-609">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-609">**Name**</span></span>       | <span data-ttu-id="36f91-610">是</span><span class="sxs-lookup"><span data-stu-id="36f91-610">Yes</span></span>         | <span data-ttu-id="36f91-611">成员名。</span><span class="sxs-lookup"><span data-stu-id="36f91-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="36f91-612">**值**</span><span class="sxs-lookup"><span data-stu-id="36f91-612">**Value**</span></span>      | <span data-ttu-id="36f91-613">否</span><span class="sxs-lookup"><span data-stu-id="36f91-613">No</span></span>          | <span data-ttu-id="36f91-614">成员的值。</span><span class="sxs-lookup"><span data-stu-id="36f91-614">The value of the member.</span></span> <span data-ttu-id="36f91-615">默认情况下，第一个成员的值为0，并且每个连续枚举数的值将增加1。</span><span class="sxs-lookup"><span data-stu-id="36f91-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="36f91-616">可能存在多个具有相同值的成员。</span><span class="sxs-lookup"><span data-stu-id="36f91-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-617">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **FunctionImport** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="36f91-618">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-619">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-620">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-620">Example</span></span>

<span data-ttu-id="36f91-621">下面的示例演示具有三个**成员**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="36f91-622">NavigationProperty 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="36f91-623">**NavigationProperty**元素定义导航属性，该属性提供对关联的另一端的引用。</span><span class="sxs-lookup"><span data-stu-id="36f91-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="36f91-624">与使用 Property 元素定义的属性不同，导航属性不定义数据的形状和特征。</span><span class="sxs-lookup"><span data-stu-id="36f91-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="36f91-625">它们提供了一种在两个实体类型之间导航关联的方法。</span><span class="sxs-lookup"><span data-stu-id="36f91-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="36f91-626">注意，对于关联两端的两种实体类型，导航属性都是可选的。</span><span class="sxs-lookup"><span data-stu-id="36f91-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="36f91-627">如果您对于位于关联一端的实体类型定义一个导航属性，则不需要对于位于关联另一端的实体类型定义导航属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="36f91-628">导航属性返回的数据类型是由其远程关联端的重数确定的。</span><span class="sxs-lookup"><span data-stu-id="36f91-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="36f91-629">例如，假设有一个导航属性 **OrdersNavProp**，该属性存在于 **客户** 实体类型上，并在 **customer** 和 **Order**之间导航一对多关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="36f91-630">因为导航属性的远程关联端的重数)  (多 \* ，所以其数据类型是) **顺序** 的集合 (。</span><span class="sxs-lookup"><span data-stu-id="36f91-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="36f91-631">同样，如果 "**订单**" 实体类型上存在导航属性**CustomerNavProp**，则其数据类型将为**Customer** ，因为远程端的重数为 1)  (1。</span><span class="sxs-lookup"><span data-stu-id="36f91-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="36f91-632">**NavigationProperty**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-633">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-634">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-635">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-635">Applicable Attributes</span></span>

<span data-ttu-id="36f91-636">下表介绍可应用于 **NavigationProperty** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="36f91-637">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-637">Attribute Name</span></span>   | <span data-ttu-id="36f91-638">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-638">Is Required</span></span> | <span data-ttu-id="36f91-639">值</span><span class="sxs-lookup"><span data-stu-id="36f91-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-640">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-640">**Name**</span></span>         | <span data-ttu-id="36f91-641">是</span><span class="sxs-lookup"><span data-stu-id="36f91-641">Yes</span></span>         | <span data-ttu-id="36f91-642">导航属性的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="36f91-643">**Relationship**</span><span class="sxs-lookup"><span data-stu-id="36f91-643">**Relationship**</span></span> | <span data-ttu-id="36f91-644">是</span><span class="sxs-lookup"><span data-stu-id="36f91-644">Yes</span></span>         | <span data-ttu-id="36f91-645">处于模型的作用域中的关联的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="36f91-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="36f91-646">**ToRole**</span></span>       | <span data-ttu-id="36f91-647">是</span><span class="sxs-lookup"><span data-stu-id="36f91-647">Yes</span></span>         | <span data-ttu-id="36f91-648">导航在此结束的关联端。</span><span class="sxs-lookup"><span data-stu-id="36f91-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="36f91-649">**ToRole**属性的值必须与在 AssociationEnd 元素) 中定义的某个关联端 (定义的某个**角色**属性的值相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="36f91-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="36f91-650">**FromRole**</span></span>     | <span data-ttu-id="36f91-651">是</span><span class="sxs-lookup"><span data-stu-id="36f91-651">Yes</span></span>         | <span data-ttu-id="36f91-652">导航从其开始的关联端。</span><span class="sxs-lookup"><span data-stu-id="36f91-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="36f91-653">**FromRole**属性的值必须与在 AssociationEnd 元素) 中定义的某个关联端 (定义的某个**角色**属性的值相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-654"> (自定义 XML 特性) 任意数量的批注特性可应用于 **NavigationProperty** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="36f91-655">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-656">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-657">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-657">Example</span></span>

<span data-ttu-id="36f91-658">下面的示例定义了一个实体类型 (**书籍**) ，其中包含两个导航属性 (**PublishedBy** 和 **WrittenBy**) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="36f91-659">OnDelete 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="36f91-660">概念架构定义语言 (CSDL) 中的 **OnDelete** 元素定义与关联关联的行为。</span><span class="sxs-lookup"><span data-stu-id="36f91-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="36f91-661">如果在关联的一端将 **Action** 特性设置为 **Cascade** ，则在删除第一条上的实体类型时，将删除关联的另一端上的相关实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="36f91-662">如果两个实体类型之间的关联是主键到主键关系，则当删除关联的另一端上的主体对象时，将删除加载的依赖对象，而不考虑 **OnDelete** 规范。</span><span class="sxs-lookup"><span data-stu-id="36f91-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="36f91-663">**OnDelete**元素只影响应用程序的运行时行为;它不会影响数据源中的行为。</span><span class="sxs-lookup"><span data-stu-id="36f91-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="36f91-664">在数据源中定义的行为应与在应用程序中定义的行为相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="36f91-665">**OnDelete**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-666">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-667">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-668">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-668">Applicable Attributes</span></span>

<span data-ttu-id="36f91-669">下表介绍可应用于 **OnDelete** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="36f91-670">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-670">Attribute Name</span></span> | <span data-ttu-id="36f91-671">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-671">Is Required</span></span> | <span data-ttu-id="36f91-672">值</span><span class="sxs-lookup"><span data-stu-id="36f91-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-673">**操作**</span><span class="sxs-lookup"><span data-stu-id="36f91-673">**Action**</span></span>     | <span data-ttu-id="36f91-674">是</span><span class="sxs-lookup"><span data-stu-id="36f91-674">Yes</span></span>         | <span data-ttu-id="36f91-675">**Cascade** 或 **None**。</span><span class="sxs-lookup"><span data-stu-id="36f91-675">**Cascade** or **None**.</span></span> <span data-ttu-id="36f91-676">如果在删除主体实体类型时将删除 **级联**的依赖实体类型，则为。</span><span class="sxs-lookup"><span data-stu-id="36f91-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="36f91-677">如果为 " **无**"，则在删除主体实体类型时不会删除从属实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-678">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **Association** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="36f91-679">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-680">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-681">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-681">Example</span></span>

<span data-ttu-id="36f91-682">下面的示例演示了一个定义**CustomerOrders**关联的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="36f91-683">**OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。</span><span class="sxs-lookup"><span data-stu-id="36f91-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="36f91-684">Parameter 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="36f91-685">概念架构定义语言中 (CSDL) 的 **参数** 元素可以是 FunctionImport 元素或 Function 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="36f91-686">FunctionImport 元素应用程序</span><span class="sxs-lookup"><span data-stu-id="36f91-686">FunctionImport Element Application</span></span>

<span data-ttu-id="36f91-687">**参数**元素 (作为**FunctionImport**元素的子元素) 用于定义在 CSDL 中声明的函数导入的输入和输出参数。</span><span class="sxs-lookup"><span data-stu-id="36f91-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="36f91-688">**Parameter**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-689">Documentation（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="36f91-690">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="36f91-691">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-691">Applicable Attributes</span></span>

<span data-ttu-id="36f91-692">下表介绍可应用于 **参数** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="36f91-693">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-693">Attribute Name</span></span> | <span data-ttu-id="36f91-694">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-694">Is Required</span></span> | <span data-ttu-id="36f91-695">值</span><span class="sxs-lookup"><span data-stu-id="36f91-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-696">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-696">**Name**</span></span>       | <span data-ttu-id="36f91-697">是</span><span class="sxs-lookup"><span data-stu-id="36f91-697">Yes</span></span>         | <span data-ttu-id="36f91-698">参数的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="36f91-699">类型</span><span class="sxs-lookup"><span data-stu-id="36f91-699">**Type**</span></span>       | <span data-ttu-id="36f91-700">是</span><span class="sxs-lookup"><span data-stu-id="36f91-700">Yes</span></span>         | <span data-ttu-id="36f91-701">参数类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-701">The parameter type.</span></span> <span data-ttu-id="36f91-702">值必须为 **EDMSimpleType** 或模型范围内的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="36f91-703">**模式**</span><span class="sxs-lookup"><span data-stu-id="36f91-703">**Mode**</span></span>       | <span data-ttu-id="36f91-704">否</span><span class="sxs-lookup"><span data-stu-id="36f91-704">No</span></span>          | <span data-ttu-id="36f91-705">**In**、 **Out**或 **InOut** ，具体取决于参数是输入、输出还是输入/输出参数。</span><span class="sxs-lookup"><span data-stu-id="36f91-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="36f91-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-706">**MaxLength**</span></span>  | <span data-ttu-id="36f91-707">否</span><span class="sxs-lookup"><span data-stu-id="36f91-707">No</span></span>          | <span data-ttu-id="36f91-708">允许的参数最长长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="36f91-709">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-709">**Precision**</span></span>  | <span data-ttu-id="36f91-710">否</span><span class="sxs-lookup"><span data-stu-id="36f91-710">No</span></span>          | <span data-ttu-id="36f91-711">参数的精度。</span><span class="sxs-lookup"><span data-stu-id="36f91-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="36f91-712">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-712">**Scale**</span></span>      | <span data-ttu-id="36f91-713">否</span><span class="sxs-lookup"><span data-stu-id="36f91-713">No</span></span>          | <span data-ttu-id="36f91-714">参数的确定位数。</span><span class="sxs-lookup"><span data-stu-id="36f91-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="36f91-715">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-715">**SRID**</span></span>       | <span data-ttu-id="36f91-716">否</span><span class="sxs-lookup"><span data-stu-id="36f91-716">No</span></span>          | <span data-ttu-id="36f91-717">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="36f91-718">仅对空间类型的参数有效。</span><span class="sxs-lookup"><span data-stu-id="36f91-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="36f91-719">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="36f91-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-720">可以将任意数量的批注属性 (自定义 XML 特性) 可应用于 **参数** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="36f91-721">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-722">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="36f91-723">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-723">Example</span></span>

<span data-ttu-id="36f91-724">下面的示例演示一个具有一个**参数**子元素的**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="36f91-725">函数接受一个输入参数并返回实体类型的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="36f91-726">Function 元素应用程序</span><span class="sxs-lookup"><span data-stu-id="36f91-726">Function Element Application</span></span>

<span data-ttu-id="36f91-727">**参数**元素 (作为**Function**元素的子元素) 为在概念模型中定义或声明的函数定义参数。</span><span class="sxs-lookup"><span data-stu-id="36f91-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="36f91-728">**Parameter**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-729">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="36f91-730">CollectionType（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="36f91-731">ReferenceType（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="36f91-732">RowType（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-733">只有一个 **CollectionType**、 **ReferenceType**或 **RowType** 元素可以是 **属性** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="36f91-734">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-735">Annotation 元素必须出现在所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="36f91-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="36f91-736">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="36f91-737">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-737">Applicable Attributes</span></span>

<span data-ttu-id="36f91-738">下表介绍可应用于 **参数** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="36f91-739">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-739">Attribute Name</span></span>   | <span data-ttu-id="36f91-740">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-740">Is Required</span></span> | <span data-ttu-id="36f91-741">值</span><span class="sxs-lookup"><span data-stu-id="36f91-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-742">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-742">**Name**</span></span>         | <span data-ttu-id="36f91-743">是</span><span class="sxs-lookup"><span data-stu-id="36f91-743">Yes</span></span>         | <span data-ttu-id="36f91-744">参数的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="36f91-745">**类型**</span><span class="sxs-lookup"><span data-stu-id="36f91-745">**Type**</span></span>         | <span data-ttu-id="36f91-746">否</span><span class="sxs-lookup"><span data-stu-id="36f91-746">No</span></span>          | <span data-ttu-id="36f91-747">参数类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-747">The parameter type.</span></span> <span data-ttu-id="36f91-748">参数可以是以下任意类型（或这些类型的集合）：</span><span class="sxs-lookup"><span data-stu-id="36f91-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="36f91-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="36f91-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="36f91-750">Entity Type — 实体类型</span><span class="sxs-lookup"><span data-stu-id="36f91-750">entity type</span></span> <br/> <span data-ttu-id="36f91-751">Complex Type — 复杂类型</span><span class="sxs-lookup"><span data-stu-id="36f91-751">complex type</span></span> <br/> <span data-ttu-id="36f91-752">行类型</span><span class="sxs-lookup"><span data-stu-id="36f91-752">row type</span></span> <br/> <span data-ttu-id="36f91-753">Reference Type — 引用类型</span><span class="sxs-lookup"><span data-stu-id="36f91-753">reference type</span></span>                             |
| <span data-ttu-id="36f91-754">**可以为 Null**</span><span class="sxs-lookup"><span data-stu-id="36f91-754">**Nullable**</span></span>     | <span data-ttu-id="36f91-755">否</span><span class="sxs-lookup"><span data-stu-id="36f91-755">No</span></span>          | <span data-ttu-id="36f91-756">**True** (默认值) 或 **False** ，具体取决于属性是否可以具有 **null** 值。</span><span class="sxs-lookup"><span data-stu-id="36f91-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="36f91-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="36f91-757">**DefaultValue**</span></span> | <span data-ttu-id="36f91-758">否</span><span class="sxs-lookup"><span data-stu-id="36f91-758">No</span></span>          | <span data-ttu-id="36f91-759">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="36f91-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="36f91-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-760">**MaxLength**</span></span>    | <span data-ttu-id="36f91-761">否</span><span class="sxs-lookup"><span data-stu-id="36f91-761">No</span></span>          | <span data-ttu-id="36f91-762">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="36f91-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-763">**FixedLength**</span></span>  | <span data-ttu-id="36f91-764">否</span><span class="sxs-lookup"><span data-stu-id="36f91-764">No</span></span>          | <span data-ttu-id="36f91-765">**True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="36f91-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="36f91-766">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-766">**Precision**</span></span>    | <span data-ttu-id="36f91-767">否</span><span class="sxs-lookup"><span data-stu-id="36f91-767">No</span></span>          | <span data-ttu-id="36f91-768">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="36f91-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="36f91-769">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-769">**Scale**</span></span>        | <span data-ttu-id="36f91-770">否</span><span class="sxs-lookup"><span data-stu-id="36f91-770">No</span></span>          | <span data-ttu-id="36f91-771">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="36f91-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="36f91-772">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-772">**SRID**</span></span>         | <span data-ttu-id="36f91-773">否</span><span class="sxs-lookup"><span data-stu-id="36f91-773">No</span></span>          | <span data-ttu-id="36f91-774">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="36f91-775">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="36f91-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="36f91-776">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="36f91-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="36f91-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="36f91-777">**Unicode**</span></span>      | <span data-ttu-id="36f91-778">否</span><span class="sxs-lookup"><span data-stu-id="36f91-778">No</span></span>          | <span data-ttu-id="36f91-779">**True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="36f91-780">**排序规则**</span><span class="sxs-lookup"><span data-stu-id="36f91-780">**Collation**</span></span>    | <span data-ttu-id="36f91-781">否</span><span class="sxs-lookup"><span data-stu-id="36f91-781">No</span></span>          | <span data-ttu-id="36f91-782">指定要在数据源中使用的排序的字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="36f91-783">可以将任意数量的批注属性 (自定义 XML 特性) 可应用于 **参数** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="36f91-784">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-785">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="36f91-786">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-786">Example</span></span>

<span data-ttu-id="36f91-787">下面的示例演示一个使用一个**参数**子元素定义函数参数的**函数**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="36f91-788">Principal 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="36f91-789">概念架构定义语言中 (CSDL) 的 **主体** 元素是 ReferentialConstraint 元素的子元素，用于定义引用约束的主体端。</span><span class="sxs-lookup"><span data-stu-id="36f91-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="36f91-790">**ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。</span><span class="sxs-lookup"><span data-stu-id="36f91-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="36f91-791">与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="36f91-792">引用的实体类型称为约束的 *主体端* 。</span><span class="sxs-lookup"><span data-stu-id="36f91-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="36f91-793">引用主体端的实体类型称为约束的 *依赖端* 。</span><span class="sxs-lookup"><span data-stu-id="36f91-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="36f91-794">**PropertyRef** 元素用于指定依赖端引用的键。</span><span class="sxs-lookup"><span data-stu-id="36f91-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="36f91-795">**主体**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-796">PropertyRef（一个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="36f91-797">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-798">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-798">Applicable Attributes</span></span>

<span data-ttu-id="36f91-799">下表描述了可应用于 **主体** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="36f91-800">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-800">Attribute Name</span></span> | <span data-ttu-id="36f91-801">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-801">Is Required</span></span> | <span data-ttu-id="36f91-802">值</span><span class="sxs-lookup"><span data-stu-id="36f91-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="36f91-803">**角色**</span><span class="sxs-lookup"><span data-stu-id="36f91-803">**Role**</span></span>       | <span data-ttu-id="36f91-804">是</span><span class="sxs-lookup"><span data-stu-id="36f91-804">Yes</span></span>         | <span data-ttu-id="36f91-805">关联的主体端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-806">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **主体** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="36f91-807">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-808">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-809">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-809">Example</span></span>

<span data-ttu-id="36f91-810">下面的示例显示了 **ReferentialConstraint** 元素，它是 **PublishedBy** 关联定义的一部分。</span><span class="sxs-lookup"><span data-stu-id="36f91-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="36f91-811">**发行者**实体类型的**Id**属性构成了引用约束的主体端。</span><span class="sxs-lookup"><span data-stu-id="36f91-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="36f91-812">Property 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-812">Property Element (CSDL)</span></span>

<span data-ttu-id="36f91-813">概念架构定义语言中 (CSDL) 的 **属性** 元素可以是 EntityType 元素、ComplexType 元素或 RowType 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="36f91-814">EntityType 和 ComplexType 元素应用程序</span><span class="sxs-lookup"><span data-stu-id="36f91-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="36f91-815">**属性** 元素 (为 **EntityType** 或 **ComplexType** 元素的子元素) 定义实体类型实例或复杂类型实例将包含的数据的形状和特征。</span><span class="sxs-lookup"><span data-stu-id="36f91-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="36f91-816">概念模型中的属性类似于为类定义的属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="36f91-817">正如类的属性定义类的形状和携带有关对象的信息一样，概念模型中的属性也定义实体类型的形状和携带有关实体类型实例的信息。</span><span class="sxs-lookup"><span data-stu-id="36f91-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="36f91-818">**属性**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-819">Documentation 元素（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="36f91-820">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="36f91-821">以下方面可应用于 **属性** 元素：可以 **为 null**、 **默认**值、 **MaxLength**、 **FixedLength**、 **精度**、 **小数位数**、 **Unicode**、 **排序规则**、 **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="36f91-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="36f91-822">方面是一些 XML 特性，它们提供有关如何在数据存储区中存储属性值的信息。</span><span class="sxs-lookup"><span data-stu-id="36f91-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-823">Facet 只能应用于 **EDMSimpleType**类型的属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="36f91-824">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-824">Applicable Attributes</span></span>

<span data-ttu-id="36f91-825">下表介绍可应用于 **属性** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="36f91-826">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-826">Attribute Name</span></span>                                                         | <span data-ttu-id="36f91-827">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-827">Is Required</span></span> | <span data-ttu-id="36f91-828">值</span><span class="sxs-lookup"><span data-stu-id="36f91-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-829">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-829">**Name**</span></span>                                                               | <span data-ttu-id="36f91-830">是</span><span class="sxs-lookup"><span data-stu-id="36f91-830">Yes</span></span>         | <span data-ttu-id="36f91-831">属性的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="36f91-832">类型</span><span class="sxs-lookup"><span data-stu-id="36f91-832">**Type**</span></span>                                                               | <span data-ttu-id="36f91-833">是</span><span class="sxs-lookup"><span data-stu-id="36f91-833">Yes</span></span>         | <span data-ttu-id="36f91-834">属性值的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-834">The type of the property value.</span></span> <span data-ttu-id="36f91-835">属性值类型必须为 **EDMSimpleType** 或模型范围内的复杂类型（由完全限定名称指示）。</span><span class="sxs-lookup"><span data-stu-id="36f91-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="36f91-836">**可以为 Null**</span><span class="sxs-lookup"><span data-stu-id="36f91-836">**Nullable**</span></span>                                                           | <span data-ttu-id="36f91-837">否</span><span class="sxs-lookup"><span data-stu-id="36f91-837">No</span></span>          | <span data-ttu-id="36f91-838">**True**（默认值）或 <strong>False</strong>，取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="36f91-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="36f91-839">在 CSDL v1 中，复杂类型属性的 > 必须具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="36f91-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="36f91-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="36f91-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="36f91-841">否</span><span class="sxs-lookup"><span data-stu-id="36f91-841">No</span></span>          | <span data-ttu-id="36f91-842">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="36f91-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="36f91-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="36f91-844">否</span><span class="sxs-lookup"><span data-stu-id="36f91-844">No</span></span>          | <span data-ttu-id="36f91-845">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="36f91-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="36f91-847">否</span><span class="sxs-lookup"><span data-stu-id="36f91-847">No</span></span>          | <span data-ttu-id="36f91-848">**True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="36f91-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="36f91-849">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-849">**Precision**</span></span>                                                          | <span data-ttu-id="36f91-850">否</span><span class="sxs-lookup"><span data-stu-id="36f91-850">No</span></span>          | <span data-ttu-id="36f91-851">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="36f91-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="36f91-852">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-852">**Scale**</span></span>                                                              | <span data-ttu-id="36f91-853">否</span><span class="sxs-lookup"><span data-stu-id="36f91-853">No</span></span>          | <span data-ttu-id="36f91-854">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="36f91-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="36f91-855">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-855">**SRID**</span></span>                                                               | <span data-ttu-id="36f91-856">否</span><span class="sxs-lookup"><span data-stu-id="36f91-856">No</span></span>          | <span data-ttu-id="36f91-857">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="36f91-858">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="36f91-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="36f91-859">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="36f91-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="36f91-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="36f91-860">**Unicode**</span></span>                                                            | <span data-ttu-id="36f91-861">否</span><span class="sxs-lookup"><span data-stu-id="36f91-861">No</span></span>          | <span data-ttu-id="36f91-862">**True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="36f91-863">**排序规则**</span><span class="sxs-lookup"><span data-stu-id="36f91-863">**Collation**</span></span>                                                          | <span data-ttu-id="36f91-864">否</span><span class="sxs-lookup"><span data-stu-id="36f91-864">No</span></span>          | <span data-ttu-id="36f91-865">指定要在数据源中使用的排序的字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="36f91-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="36f91-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="36f91-867">否</span><span class="sxs-lookup"><span data-stu-id="36f91-867">No</span></span>          | <span data-ttu-id="36f91-868">**无**（默认值）或**固定**。</span><span class="sxs-lookup"><span data-stu-id="36f91-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="36f91-869">如果此值设置为**固定**，会在乐观并发检查中使用属性值。</span><span class="sxs-lookup"><span data-stu-id="36f91-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="36f91-870">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Property** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="36f91-871">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-872">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="36f91-873">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-873">Example</span></span>

<span data-ttu-id="36f91-874">下面的示例演示一个具有三个**属性**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="36f91-875">下面的示例演示一个包含五个**属性**元素的**ComplexType**元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="36f91-876">RowType 元素应用</span><span class="sxs-lookup"><span data-stu-id="36f91-876">RowType Element Application</span></span>

<span data-ttu-id="36f91-877">**属性** 元素 (作为 **RowType** 元素的子元素) 定义可传递到模型定义函数或从模型定义函数返回的数据的形状和特征。</span><span class="sxs-lookup"><span data-stu-id="36f91-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="36f91-878">**Property**元素可以具有以下子元素之一：</span><span class="sxs-lookup"><span data-stu-id="36f91-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="36f91-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="36f91-879">CollectionType</span></span>
-   <span data-ttu-id="36f91-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="36f91-880">ReferenceType</span></span>
-   <span data-ttu-id="36f91-881">RowType</span><span class="sxs-lookup"><span data-stu-id="36f91-881">RowType</span></span>

<span data-ttu-id="36f91-882">**Property**元素可以有任意数量的子批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-883">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="36f91-884">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-884">Applicable Attributes</span></span>

<span data-ttu-id="36f91-885">下表介绍可应用于 **属性** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="36f91-886">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-886">Attribute Name</span></span>                                                     | <span data-ttu-id="36f91-887">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-887">Is Required</span></span> | <span data-ttu-id="36f91-888">值</span><span class="sxs-lookup"><span data-stu-id="36f91-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-889">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-889">**Name**</span></span>                                                           | <span data-ttu-id="36f91-890">是</span><span class="sxs-lookup"><span data-stu-id="36f91-890">Yes</span></span>         | <span data-ttu-id="36f91-891">属性的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="36f91-892">类型</span><span class="sxs-lookup"><span data-stu-id="36f91-892">**Type**</span></span>                                                           | <span data-ttu-id="36f91-893">是</span><span class="sxs-lookup"><span data-stu-id="36f91-893">Yes</span></span>         | <span data-ttu-id="36f91-894">属性值的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="36f91-895">**可以为 Null**</span><span class="sxs-lookup"><span data-stu-id="36f91-895">**Nullable**</span></span>                                                       | <span data-ttu-id="36f91-896">否</span><span class="sxs-lookup"><span data-stu-id="36f91-896">No</span></span>          | <span data-ttu-id="36f91-897">**True**（默认值）或 **False**，取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="36f91-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="36f91-898">CSDL v1 中的 >，复杂类型属性必须具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="36f91-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="36f91-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="36f91-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="36f91-900">否</span><span class="sxs-lookup"><span data-stu-id="36f91-900">No</span></span>          | <span data-ttu-id="36f91-901">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="36f91-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="36f91-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="36f91-903">否</span><span class="sxs-lookup"><span data-stu-id="36f91-903">No</span></span>          | <span data-ttu-id="36f91-904">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="36f91-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="36f91-906">否</span><span class="sxs-lookup"><span data-stu-id="36f91-906">No</span></span>          | <span data-ttu-id="36f91-907">**True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="36f91-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="36f91-908">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-908">**Precision**</span></span>                                                      | <span data-ttu-id="36f91-909">否</span><span class="sxs-lookup"><span data-stu-id="36f91-909">No</span></span>          | <span data-ttu-id="36f91-910">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="36f91-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="36f91-911">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-911">**Scale**</span></span>                                                          | <span data-ttu-id="36f91-912">否</span><span class="sxs-lookup"><span data-stu-id="36f91-912">No</span></span>          | <span data-ttu-id="36f91-913">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="36f91-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="36f91-914">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-914">**SRID**</span></span>                                                           | <span data-ttu-id="36f91-915">否</span><span class="sxs-lookup"><span data-stu-id="36f91-915">No</span></span>          | <span data-ttu-id="36f91-916">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="36f91-917">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="36f91-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="36f91-918">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="36f91-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="36f91-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="36f91-919">**Unicode**</span></span>                                                        | <span data-ttu-id="36f91-920">否</span><span class="sxs-lookup"><span data-stu-id="36f91-920">No</span></span>          | <span data-ttu-id="36f91-921">**True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="36f91-922">**排序规则**</span><span class="sxs-lookup"><span data-stu-id="36f91-922">**Collation**</span></span>                                                      | <span data-ttu-id="36f91-923">否</span><span class="sxs-lookup"><span data-stu-id="36f91-923">No</span></span>          | <span data-ttu-id="36f91-924">指定要在数据源中使用的排序的字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="36f91-925">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Property** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="36f91-926">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-927">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="36f91-928">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-928">Example</span></span>

<span data-ttu-id="36f91-929">下面的示例演示了用于定义模型定义函数的返回类型的形状的 **属性** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="36f91-930">PropertyRef 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="36f91-931">概念架构定义语言 (CSDL) 中的 **PropertyRef** 元素引用实体类型的属性，以指示该属性将执行以下角色之一：</span><span class="sxs-lookup"><span data-stu-id="36f91-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="36f91-932">实体键的一部分（实体类型的用于确定标识的一个或一组属性）。</span><span class="sxs-lookup"><span data-stu-id="36f91-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="36f91-933">一个或多个 **PropertyRef** 元素可用于定义实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="36f91-934">引用约束的依赖端或主体端。</span><span class="sxs-lookup"><span data-stu-id="36f91-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="36f91-935">**PropertyRef**元素只能有 (零个或多个) 作为子元素的批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-936">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-937">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-937">Applicable Attributes</span></span>

<span data-ttu-id="36f91-938">下表介绍可应用于 **PropertyRef** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="36f91-939">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-939">Attribute Name</span></span> | <span data-ttu-id="36f91-940">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-940">Is Required</span></span> | <span data-ttu-id="36f91-941">值</span><span class="sxs-lookup"><span data-stu-id="36f91-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="36f91-942">**名称**</span><span class="sxs-lookup"><span data-stu-id="36f91-942">**Name**</span></span>       | <span data-ttu-id="36f91-943">是</span><span class="sxs-lookup"><span data-stu-id="36f91-943">Yes</span></span>         | <span data-ttu-id="36f91-944">所引用属性的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-945">可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **PropertyRef** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="36f91-946">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-947">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-948">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-948">Example</span></span>

<span data-ttu-id="36f91-949">下面的示例定义了 (**Book**) 的实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="36f91-950">实体键通过引用实体类型的 **ISBN** 属性来定义。</span><span class="sxs-lookup"><span data-stu-id="36f91-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="36f91-951">在下一个示例中，使用两个 **PropertyRef** 元素来指示 (**Id** 和 **PublisherId** 的两个属性) 是引用约束的主体端和依赖端。</span><span class="sxs-lookup"><span data-stu-id="36f91-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="36f91-952">ReferenceType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="36f91-953">概念架构定义语言 (CSDL) 中的 **ReferenceType** 元素指定对实体类型的引用。</span><span class="sxs-lookup"><span data-stu-id="36f91-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="36f91-954">**ReferenceType**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="36f91-955">ReturnType (函数) </span><span class="sxs-lookup"><span data-stu-id="36f91-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="36f91-956">参数</span><span class="sxs-lookup"><span data-stu-id="36f91-956">Parameter</span></span>
-   <span data-ttu-id="36f91-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="36f91-957">CollectionType</span></span>

<span data-ttu-id="36f91-958">在定义函数的参数或返回类型时，将使用 **ReferenceType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="36f91-959">**ReferenceType**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-960">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-961">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-962">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-962">Applicable Attributes</span></span>

<span data-ttu-id="36f91-963">下表介绍可应用于 **ReferenceType** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="36f91-964">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-964">Attribute Name</span></span> | <span data-ttu-id="36f91-965">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-965">Is Required</span></span> | <span data-ttu-id="36f91-966">值</span><span class="sxs-lookup"><span data-stu-id="36f91-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="36f91-967">类型</span><span class="sxs-lookup"><span data-stu-id="36f91-967">**Type**</span></span>       | <span data-ttu-id="36f91-968">是</span><span class="sxs-lookup"><span data-stu-id="36f91-968">Yes</span></span>         | <span data-ttu-id="36f91-969">所引用的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-970">可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **ReferenceType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="36f91-971">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-972">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-973">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-973">Example</span></span>

<span data-ttu-id="36f91-974">下面的示例显示了**ReferenceType**元素，该元素在接受对**Person**实体类型的引用的模型定义函数中用作**Parameter**元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="36f91-975">下面的示例显示了**ReferenceType**元素，该元素用于在模型定义的函数中返回对**Person**实体类型的引用的**ReturnType** (函数) 元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="36f91-976">ReferentialConstraint 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="36f91-977"> (CSDL) 概念架构定义语言中的 **ReferentialConstraint** 元素定义的功能与关系数据库中的引用完整性约束类似。</span><span class="sxs-lookup"><span data-stu-id="36f91-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="36f91-978">与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。</span><span class="sxs-lookup"><span data-stu-id="36f91-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="36f91-979">引用的实体类型称为约束的 *主体端* 。</span><span class="sxs-lookup"><span data-stu-id="36f91-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="36f91-980">引用主体端的实体类型称为约束的 *依赖端* 。</span><span class="sxs-lookup"><span data-stu-id="36f91-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="36f91-981">如果一个实体类型上公开的外键引用另一个实体类型上的属性，则 **ReferentialConstraint** 元素将定义这两个实体类型之间的关联。</span><span class="sxs-lookup"><span data-stu-id="36f91-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="36f91-982">由于 **ReferentialConstraint** 元素提供有关两个实体类型之间的关系的信息，因此在映射规范语言 (MSL) 中不需要对应的 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="36f91-983">没有公开外键的两个实体类型之间的关联必须具有相应的 **AssociationSetMapping** 元素，才能将关联信息映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="36f91-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="36f91-984">如果在实体类型上没有公开外键，则 **ReferentialConstraint** 元素只能定义实体类型和其他实体类型之间的主键到主键约束。</span><span class="sxs-lookup"><span data-stu-id="36f91-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="36f91-985">**ReferentialConstraint**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-986">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-987">Principal（恰好一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="36f91-988">Dependent（恰好一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="36f91-989">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-990">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-990">Applicable Attributes</span></span>

<span data-ttu-id="36f91-991">**ReferentialConstraint**元素可以具有任意数量的批注属性 (自定义 XML 特性) 。</span><span class="sxs-lookup"><span data-stu-id="36f91-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="36f91-992">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-993">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-994">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-994">Example</span></span>

<span data-ttu-id="36f91-995">下面的示例演示了在**PublishedBy**关联的定义中使用的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="36f91-996">ReturnType (函数) 元素 (CSDL) </span><span class="sxs-lookup"><span data-stu-id="36f91-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="36f91-997">使用概念架构定义语言中的 **ReturnType** (函数) 元素 (CSDL) 指定函数元素中定义的函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="36f91-998">还可以使用 **ReturnType** 特性指定函数返回类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="36f91-999">返回类型可以是任何 **EdmSimpleType**、实体类型、复杂类型、行类型、引用类型或这些类型之一的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="36f91-1000">函数的返回类型可使用**ReturnType** (函数) 元素的**type**特性指定，或使用以下子元素之一指定：</span><span class="sxs-lookup"><span data-stu-id="36f91-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="36f91-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="36f91-1001">CollectionType</span></span>
-   <span data-ttu-id="36f91-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="36f91-1002">ReferenceType</span></span>
-   <span data-ttu-id="36f91-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="36f91-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-1004">如果指定的函数返回类型同时具有**ReturnType** (函数的**type**特性) 元素和其中一个子元素，则模型将不会进行验证。</span><span class="sxs-lookup"><span data-stu-id="36f91-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-1005">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1005">Applicable Attributes</span></span>

<span data-ttu-id="36f91-1006">下表介绍可应用于 **ReturnType** (函数) 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="36f91-1007">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-1007">Attribute Name</span></span> | <span data-ttu-id="36f91-1008">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-1008">Is Required</span></span> | <span data-ttu-id="36f91-1009">值</span><span class="sxs-lookup"><span data-stu-id="36f91-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="36f91-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="36f91-1010">**ReturnType**</span></span> | <span data-ttu-id="36f91-1011">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1011">No</span></span>          | <span data-ttu-id="36f91-1012">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-1013"> (自定义 XML 特性) 任意数量的批注特性可应用于 **ReturnType** (函数) 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="36f91-1014">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-1015">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-1016">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1016">Example</span></span>

<span data-ttu-id="36f91-1017">下面的示例使用 **function** 元素来定义一个函数，该函数返回书籍已打印的年数。</span><span class="sxs-lookup"><span data-stu-id="36f91-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="36f91-1018">请注意，返回类型由**ReturnType** (函数) 元素的**type**属性指定。</span><span class="sxs-lookup"><span data-stu-id="36f91-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="36f91-1019">ReturnType (FunctionImport) 元素 (CSDL) </span><span class="sxs-lookup"><span data-stu-id="36f91-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="36f91-1020">使用概念架构定义语言中的 **ReturnType** (FunctionImport) 元素 (CSDL) 指定在 FunctionImport 元素中定义的函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="36f91-1021">还可以使用 **ReturnType** 特性指定函数返回类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="36f91-1022">返回类型可以是实体类型、复杂类型或 **EdmSimpleType**的任何集合，</span><span class="sxs-lookup"><span data-stu-id="36f91-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="36f91-1023">函数的返回类型是使用**ReturnType** (FunctionImport) 元素的**type**属性指定的。</span><span class="sxs-lookup"><span data-stu-id="36f91-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-1024">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1024">Applicable Attributes</span></span>

<span data-ttu-id="36f91-1025">下表介绍可应用于 **ReturnType** (FunctionImport) 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="36f91-1026">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-1026">Attribute Name</span></span> | <span data-ttu-id="36f91-1027">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-1027">Is Required</span></span> | <span data-ttu-id="36f91-1028">值</span><span class="sxs-lookup"><span data-stu-id="36f91-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-1029">**类型**</span><span class="sxs-lookup"><span data-stu-id="36f91-1029">**Type**</span></span>       | <span data-ttu-id="36f91-1030">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1030">No</span></span>          | <span data-ttu-id="36f91-1031">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1031">The type that the function returns.</span></span> <span data-ttu-id="36f91-1032">该值必须是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="36f91-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="36f91-1033">**EntitySet**</span></span>  | <span data-ttu-id="36f91-1034">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1034">No</span></span>          | <span data-ttu-id="36f91-1035">如果函数返回实体类型的集合，则 **EntitySet** 的值必须是该集合所属的实体集。</span><span class="sxs-lookup"><span data-stu-id="36f91-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="36f91-1036">否则，不得使用 **EntitySet** 属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-1037">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **ReturnType** (FunctionImport) 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="36f91-1038">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-1039">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-1040">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1040">Example</span></span>

<span data-ttu-id="36f91-1041">下面的示例使用返回书籍和出版商的 **FunctionImport** 。</span><span class="sxs-lookup"><span data-stu-id="36f91-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="36f91-1042">请注意，函数将返回两个结果集，因此指定了两个 **ReturnType** (FunctionImport) 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="36f91-1043">RowType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="36f91-1044">概念架构定义语言 (CSDL) 中的 **RowType** 元素将未命名的结构定义为概念模型中定义的函数的参数或返回类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="36f91-1045">**RowType**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="36f91-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="36f91-1046">CollectionType</span></span>
-   <span data-ttu-id="36f91-1047">参数</span><span class="sxs-lookup"><span data-stu-id="36f91-1047">Parameter</span></span>
-   <span data-ttu-id="36f91-1048">ReturnType (函数) </span><span class="sxs-lookup"><span data-stu-id="36f91-1048">ReturnType (Function)</span></span>

<span data-ttu-id="36f91-1049">**RowType**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-1050">Property（一个或多个）</span><span class="sxs-lookup"><span data-stu-id="36f91-1050">Property (one or more)</span></span>
-   <span data-ttu-id="36f91-1051">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="36f91-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-1052">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1052">Applicable Attributes</span></span>

<span data-ttu-id="36f91-1053">可以将任意数量的批注属性 (自定义 XML 特性) 应用到 **RowType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="36f91-1054">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-1055">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-1056">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1056">Example</span></span>

<span data-ttu-id="36f91-1057">下面的示例演示一个模型定义函数，该函数使用 **CollectionType** 元素来指定该函数返回 (在 **RowType** 元素) 中指定的行的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="36f91-1058">Schema 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="36f91-1059">**Schema**元素是概念模型定义的根元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="36f91-1060">它包括构成概念模型的对象、函数和容器的定义。</span><span class="sxs-lookup"><span data-stu-id="36f91-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="36f91-1061">**Schema**元素可包含零个或多个下列子元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="36f91-1062">使用</span><span class="sxs-lookup"><span data-stu-id="36f91-1062">Using</span></span>
-   <span data-ttu-id="36f91-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="36f91-1063">EntityContainer</span></span>
-   <span data-ttu-id="36f91-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="36f91-1064">EntityType</span></span>
-   <span data-ttu-id="36f91-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="36f91-1065">EnumType</span></span>
-   <span data-ttu-id="36f91-1066">关联</span><span class="sxs-lookup"><span data-stu-id="36f91-1066">Association</span></span>
-   <span data-ttu-id="36f91-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="36f91-1067">ComplexType</span></span>
-   <span data-ttu-id="36f91-1068">函数</span><span class="sxs-lookup"><span data-stu-id="36f91-1068">Function</span></span>

<span data-ttu-id="36f91-1069">一个 **Schema** 元素可包含零个或一个批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-1070">仅在 CSDL v2 和更高版本中允许 **Function** 元素和 annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="36f91-1071">**Schema**元素使用**namespace**特性为概念模型中的实体类型、复杂类型和关联对象定义命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="36f91-1072">在命名空间内，任何两个对象都不能同名。</span><span class="sxs-lookup"><span data-stu-id="36f91-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="36f91-1073">命名空间可以跨多个 **架构** 元素和多个 csdl 文件。</span><span class="sxs-lookup"><span data-stu-id="36f91-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="36f91-1074">概念模型命名空间与 **Schema** 元素的 XML 命名空间不同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="36f91-1075">**命名空间**特性定义的概念模型命名空间 () 是实体类型、复杂类型和关联类型的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="36f91-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="36f91-1076">**Schema**元素的**xmlns**特性) 指示 (XML 命名空间是**schema**元素的子元素和属性的默认命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="36f91-1077">格式为 (的 XML 命名空间 https://schemas.microsoft.com/ado/YYYY/MM/edm 分别表示年份和月份，) 是为 CSDL 保留的。</span><span class="sxs-lookup"><span data-stu-id="36f91-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="36f91-1078">自定义元素和特性不能位于具有此格式的命名空间中。</span><span class="sxs-lookup"><span data-stu-id="36f91-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-1079">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1079">Applicable Attributes</span></span>

<span data-ttu-id="36f91-1080">下表描述了可对 **Schema** 元素应用的属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="36f91-1081">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-1081">Attribute Name</span></span> | <span data-ttu-id="36f91-1082">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-1082">Is Required</span></span> | <span data-ttu-id="36f91-1083">值</span><span class="sxs-lookup"><span data-stu-id="36f91-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-1084">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="36f91-1084">**Namespace**</span></span>  | <span data-ttu-id="36f91-1085">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1085">Yes</span></span>         | <span data-ttu-id="36f91-1086">概念模型的命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="36f91-1087">**命名空间**属性的值用于构成类型的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="36f91-1088">例如，如果名为*Customer*的**entitytype**位于 Simple 命名空间中，则**entitytype**的完全限定名称为 simpleexamplemodel.customer。</span><span class="sxs-lookup"><span data-stu-id="36f91-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="36f91-1089">以下字符串不能用作 **Namespace** 特性的值： **System**、 **暂时性**或 **Edm**。</span><span class="sxs-lookup"><span data-stu-id="36f91-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="36f91-1090">**命名空间**属性的值不能与 SSDL 架构元素中**namespace**属性的值相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="36f91-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="36f91-1091">**Alias**</span></span>      | <span data-ttu-id="36f91-1092">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1092">No</span></span>          | <span data-ttu-id="36f91-1093">用于取代命名空间名称的标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="36f91-1094">例如，如果名为*Customer*的**EntityType**位于 Simple 命名空间，并且**Alias**属性的值为*Model*，则可以使用 model 作为 EntityType 的完全限定名称 **。**</span><span class="sxs-lookup"><span data-stu-id="36f91-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="36f91-1095">可以将任意数量的批注属性 (自定义 XML 特性) 可以应用于 **Schema** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="36f91-1096">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-1097">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-1098">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1098">Example</span></span>

<span data-ttu-id="36f91-1099">下面的示例演示一个 **架构** 元素，该元素包含一个 **EntityContainer** 元素、两个 **EntityType** 元素和一个 **Association** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="36f91-1100">TypeRef 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="36f91-1101">概念架构定义语言中的 **TypeRef** 元素 (CSDL) 提供对现有命名类型的引用。</span><span class="sxs-lookup"><span data-stu-id="36f91-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="36f91-1102">**TypeRef**元素可以是 CollectionType 元素的子元素，用于指定函数具有集合作为参数或返回类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="36f91-1103">**TypeRef**元素可以具有以下子元素 (按列出的顺序) ：</span><span class="sxs-lookup"><span data-stu-id="36f91-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="36f91-1104">Documentation（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="36f91-1105">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-1106">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1106">Applicable Attributes</span></span>

<span data-ttu-id="36f91-1107">下表介绍可应用于 **TypeRef** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="36f91-1108">请注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **精度**、 **小数位数**、 **Unicode**和 **排序规则** 特性仅适用于 **edmsimpletype**。</span><span class="sxs-lookup"><span data-stu-id="36f91-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="36f91-1109">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="36f91-1110">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-1110">Is Required</span></span> | <span data-ttu-id="36f91-1111">值</span><span class="sxs-lookup"><span data-stu-id="36f91-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-1112">**类型**</span><span class="sxs-lookup"><span data-stu-id="36f91-1112">**Type**</span></span>                                                           | <span data-ttu-id="36f91-1113">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1113">No</span></span>          | <span data-ttu-id="36f91-1114">所引用的类型的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="36f91-1115">**可以为 Null**</span><span class="sxs-lookup"><span data-stu-id="36f91-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="36f91-1116">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1116">No</span></span>          | <span data-ttu-id="36f91-1117">**True**（默认值）或 **False**，取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="36f91-1118">CSDL v1 中的 >，复杂类型属性必须具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="36f91-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="36f91-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="36f91-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="36f91-1120">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1120">No</span></span>          | <span data-ttu-id="36f91-1121">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="36f91-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="36f91-1123">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1123">No</span></span>          | <span data-ttu-id="36f91-1124">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="36f91-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="36f91-1126">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1126">No</span></span>          | <span data-ttu-id="36f91-1127">**True** 或 **False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="36f91-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="36f91-1128">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-1128">**Precision**</span></span>                                                      | <span data-ttu-id="36f91-1129">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1129">No</span></span>          | <span data-ttu-id="36f91-1130">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="36f91-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="36f91-1131">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-1131">**Scale**</span></span>                                                          | <span data-ttu-id="36f91-1132">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1132">No</span></span>          | <span data-ttu-id="36f91-1133">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="36f91-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="36f91-1134">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-1134">**SRID**</span></span>                                                           | <span data-ttu-id="36f91-1135">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1135">No</span></span>          | <span data-ttu-id="36f91-1136">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="36f91-1137">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="36f91-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="36f91-1138">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="36f91-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="36f91-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="36f91-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="36f91-1140">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1140">No</span></span>          | <span data-ttu-id="36f91-1141">**True** 或 **False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="36f91-1142">**排序规则**</span><span class="sxs-lookup"><span data-stu-id="36f91-1142">**Collation**</span></span>                                                      | <span data-ttu-id="36f91-1143">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1143">No</span></span>          | <span data-ttu-id="36f91-1144">指定要在数据源中使用的排序的字符串。</span><span class="sxs-lookup"><span data-stu-id="36f91-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="36f91-1145">可以将任意数量的批注属性 (自定义 XML 特性) 应用于 **CollectionType** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="36f91-1146">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-1147">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-1148">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1148">Example</span></span>

<span data-ttu-id="36f91-1149">下面的示例演示一个模型定义函数，该函数使用 **TypeRef** 元素 (作为 **CollectionType** 元素的子元素，) 指定该函数接受 **部门** 实体类型的集合。</span><span class="sxs-lookup"><span data-stu-id="36f91-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="36f91-1150">Using 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="36f91-1151">概念性架构定义语言中的 **Using** 元素 (CSDL) 导入在其他命名空间中存在的概念模型的内容。</span><span class="sxs-lookup"><span data-stu-id="36f91-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="36f91-1152">通过设置 **Namespace** 属性的值，您可以引用在其他概念模型中定义的实体类型、复杂类型和关联类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="36f91-1153">多个 **Using** 元素可以是 **Schema** 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-1154">CSDL 中的 **using** 元素的作用与编程语言中的 **using** 语句完全相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="36f91-1155">通过使用编程语言中的 **using** 语句导入命名空间，不会影响原始命名空间中的对象。</span><span class="sxs-lookup"><span data-stu-id="36f91-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="36f91-1156">在 CSDL 中，导入的命名空间可以包含从原始命名空间中的实体类型导出的实体类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="36f91-1157">这可能影响在原始命名空间中声明的实体集。</span><span class="sxs-lookup"><span data-stu-id="36f91-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="36f91-1158">**Using**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="36f91-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="36f91-1159">Documentation（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="36f91-1160">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="36f91-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="36f91-1161">适用的属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1161">Applicable Attributes</span></span>

<span data-ttu-id="36f91-1162">下表描述了可应用于 **Using** 元素的特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="36f91-1163">属性名称</span><span class="sxs-lookup"><span data-stu-id="36f91-1163">Attribute Name</span></span> | <span data-ttu-id="36f91-1164">是否必需</span><span class="sxs-lookup"><span data-stu-id="36f91-1164">Is Required</span></span> | <span data-ttu-id="36f91-1165">值</span><span class="sxs-lookup"><span data-stu-id="36f91-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="36f91-1166">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="36f91-1166">**Namespace**</span></span>  | <span data-ttu-id="36f91-1167">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1167">Yes</span></span>         | <span data-ttu-id="36f91-1168">导入的命名空间的名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="36f91-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="36f91-1169">**Alias**</span></span>      | <span data-ttu-id="36f91-1170">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1170">Yes</span></span>         | <span data-ttu-id="36f91-1171">用于取代命名空间名称的标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="36f91-1172">尽管此特性是必需的，但并不要求使用它来取代命名空间以限定对象名称。</span><span class="sxs-lookup"><span data-stu-id="36f91-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="36f91-1173">可以将任意数量的批注属性 (自定义 XML 特性) 可应用于 **Using** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="36f91-1174">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="36f91-1175">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="36f91-1176">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1176">Example</span></span>

<span data-ttu-id="36f91-1177">下面的示例演示用于导入在其他位置定义的命名空间的 **Using** 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="36f91-1178">请注意，显示的 **架构** 元素的命名空间为 `BooksModel` 。</span><span class="sxs-lookup"><span data-stu-id="36f91-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="36f91-1179">`Address`EntityType 上的属性 `Publisher` **EntityType**是在命名空间中定义的复杂类型 `ExtendedBooksModel` (**使用 Using**元素) 导入。</span><span class="sxs-lookup"><span data-stu-id="36f91-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="36f91-1180">Annotation 特性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="36f91-1181">以概念架构定义语言 (CSDL) 表示的批注特性是概念模型中的自定义 XML 特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="36f91-1182">除了具有有效的 XML 结构之外，还必须满足批注特性的以下各项条件：</span><span class="sxs-lookup"><span data-stu-id="36f91-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="36f91-1183">批注特性不能位于为 CSDL 保留的任何 XML 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="36f91-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="36f91-1184">可以将多个批注特性应用于给定的 CSDL 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="36f91-1185">任何两个批注特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="36f91-1186">可以使用批注特性提供有关概念模型中元素的额外元数据。</span><span class="sxs-lookup"><span data-stu-id="36f91-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="36f91-1187">可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。</span><span class="sxs-lookup"><span data-stu-id="36f91-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-1188">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1188">Example</span></span>

<span data-ttu-id="36f91-1189">下面的示例演示一个具有批注特性 (**CustomAttribute**) 的**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="36f91-1190">本示例还演示了一个应用于实体类型元素的批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1190">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="36f91-1191">下面的代码检索批注特性中的元数据并将其写入控制台：</span><span class="sxs-lookup"><span data-stu-id="36f91-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="36f91-1192">上面的代码假定 `School.csdl` 文件位于项目的输出目录中，且您已将下面的 `Imports` 和 `Using` 语句添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="36f91-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="36f91-1193">Annotation 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="36f91-1194">以概念架构定义语言 (CSDL) 表示的批注元素是概念模型中的自定义 XML 元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="36f91-1195">除了具有有效的 XML 结构之外，还必须满足批注元素的以下各项条件：</span><span class="sxs-lookup"><span data-stu-id="36f91-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="36f91-1196">批注元素不能位于为 CSDL 保留的任何 XML 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="36f91-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="36f91-1197">多个批注元素可能是某个给定 CSDL 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="36f91-1198">任何两个批注元素的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="36f91-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="36f91-1199">批注元素必须出现在给定 CSDL 元素的所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="36f91-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="36f91-1200">可以使用批注元素提供有关概念模型中元素的额外元数据。</span><span class="sxs-lookup"><span data-stu-id="36f91-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="36f91-1201">从 .NET Framework 版本4开始，可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。</span><span class="sxs-lookup"><span data-stu-id="36f91-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-1202">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1202">Example</span></span>

<span data-ttu-id="36f91-1203">下面的示例演示一个 **EntityType** 元素，该元素具有一个 (**CustomElement**) 的批注元素。</span><span class="sxs-lookup"><span data-stu-id="36f91-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="36f91-1204">本示例还演示了一个应用于实体类型元素的批注特性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="36f91-1205">下面的代码检索批注元素中的元数据并将其写入控制台：</span><span class="sxs-lookup"><span data-stu-id="36f91-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="36f91-1206">上面的代码假定 School.csdl 文件位于项目的输出目录中并且您已将下面的 `Imports` 和 `Using` 语句添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="36f91-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="36f91-1207">概念模型类型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="36f91-1208">概念架构定义语言 (CSDL) 支持一组抽象基元数据类型（称为 **edmsimpletype**），用于定义概念模型中的属性。</span><span class="sxs-lookup"><span data-stu-id="36f91-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="36f91-1209">**Edmsimpletype** 是存储或承载环境中支持的基元数据类型的代理。</span><span class="sxs-lookup"><span data-stu-id="36f91-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="36f91-1210">下表列出了 CSDL 支持的基元数据类型。</span><span class="sxs-lookup"><span data-stu-id="36f91-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="36f91-1211">该表还列出了可应用于每个 **EDMSimpleType**的分面。</span><span class="sxs-lookup"><span data-stu-id="36f91-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="36f91-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="36f91-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="36f91-1213">说明</span><span class="sxs-lookup"><span data-stu-id="36f91-1213">Description</span></span>                                                | <span data-ttu-id="36f91-1214">适用的方面</span><span class="sxs-lookup"><span data-stu-id="36f91-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="36f91-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="36f91-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="36f91-1216">包含二进制数据。</span><span class="sxs-lookup"><span data-stu-id="36f91-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="36f91-1217">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="36f91-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="36f91-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="36f91-1219">包含值 **true** 或 **false**。</span><span class="sxs-lookup"><span data-stu-id="36f91-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="36f91-1220">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="36f91-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="36f91-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="36f91-1222">包含一个无符号的 8 位整数值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="36f91-1223">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="36f91-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="36f91-1225">表示日期和时间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="36f91-1226">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="36f91-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="36f91-1228">包含以相对于 GMT 的偏移量（以分钟为单位）表示的日期和时间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="36f91-1229">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="36f91-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="36f91-1231">包含一个具有固定精度和小数位数的数值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="36f91-1232">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="36f91-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="36f91-1234">包含具有15位精度的浮点数</span><span class="sxs-lookup"><span data-stu-id="36f91-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="36f91-1235">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1236">**Edm**</span><span class="sxs-lookup"><span data-stu-id="36f91-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="36f91-1237">包含一个具有 7 位精度的浮点数。</span><span class="sxs-lookup"><span data-stu-id="36f91-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="36f91-1238">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="36f91-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="36f91-1240">包含一个 16 字节的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="36f91-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="36f91-1241">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="36f91-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="36f91-1243">包含一个带符号的 16 位整数值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="36f91-1244">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="36f91-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="36f91-1246">包含一个带符号的 32 位整数值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="36f91-1247">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="36f91-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="36f91-1249">包含一个带符号的 64 位整数值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="36f91-1250">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="36f91-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="36f91-1252">包含一个带符号的 8 位整数值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="36f91-1253">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="36f91-1254">**Edm.String**</span></span>                   | <span data-ttu-id="36f91-1255">包含字符数据。</span><span class="sxs-lookup"><span data-stu-id="36f91-1255">Contains character data.</span></span>                                   | <span data-ttu-id="36f91-1256">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="36f91-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="36f91-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="36f91-1258">包含当天的时间。</span><span class="sxs-lookup"><span data-stu-id="36f91-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="36f91-1259">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="36f91-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="36f91-1260">**Edm**</span><span class="sxs-lookup"><span data-stu-id="36f91-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="36f91-1261">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="36f91-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="36f91-1263">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1264">**Edm. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="36f91-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="36f91-1265">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1266">**Edm. GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="36f91-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="36f91-1267">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1268">**Edm. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="36f91-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="36f91-1269">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1270">**Edm. GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="36f91-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="36f91-1271">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1272">**Edm. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="36f91-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="36f91-1273">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1274">**Edm. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="36f91-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="36f91-1275">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1276">**Edm**</span><span class="sxs-lookup"><span data-stu-id="36f91-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="36f91-1277">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1278">**Edm. GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="36f91-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="36f91-1279">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1280">**Edm. GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="36f91-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="36f91-1281">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1282">**Edm. GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="36f91-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="36f91-1283">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1284">**Edm. GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="36f91-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="36f91-1285">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1286">**Edm. GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="36f91-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="36f91-1287">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1288">**Edm. GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="36f91-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="36f91-1289">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="36f91-1290">**Edm. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="36f91-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="36f91-1291">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="36f91-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="36f91-1292">方面 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="36f91-1292">Facets (CSDL)</span></span>

<span data-ttu-id="36f91-1293">以概念架构定义语言 (CSDL) 表示的方面表示对于实体类型和复杂类型的属性的约束。</span><span class="sxs-lookup"><span data-stu-id="36f91-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="36f91-1294">方面作为 XML 特性出现在以下 CSDL 元素上：</span><span class="sxs-lookup"><span data-stu-id="36f91-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="36f91-1295">properties</span><span class="sxs-lookup"><span data-stu-id="36f91-1295">Property</span></span>
-   <span data-ttu-id="36f91-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="36f91-1296">TypeRef</span></span>
-   <span data-ttu-id="36f91-1297">参数</span><span class="sxs-lookup"><span data-stu-id="36f91-1297">Parameter</span></span>

<span data-ttu-id="36f91-1298">下表描述了 CSDL 中支持的方面。</span><span class="sxs-lookup"><span data-stu-id="36f91-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="36f91-1299">所有方面都是可选的。</span><span class="sxs-lookup"><span data-stu-id="36f91-1299">All facets are optional.</span></span> <span data-ttu-id="36f91-1300">从概念模型生成数据库时，实体框架将使用下面列出的一些方面。</span><span class="sxs-lookup"><span data-stu-id="36f91-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="36f91-1301">有关概念模型中的数据类型的信息，请参阅概念模型类型 (CSDL) 。</span><span class="sxs-lookup"><span data-stu-id="36f91-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="36f91-1302">方面</span><span class="sxs-lookup"><span data-stu-id="36f91-1302">Facet</span></span>               | <span data-ttu-id="36f91-1303">说明</span><span class="sxs-lookup"><span data-stu-id="36f91-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="36f91-1304">适用于</span><span class="sxs-lookup"><span data-stu-id="36f91-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="36f91-1305">用于生成数据库</span><span class="sxs-lookup"><span data-stu-id="36f91-1305">Used for the database generation</span></span> | <span data-ttu-id="36f91-1306">由运行时使用</span><span class="sxs-lookup"><span data-stu-id="36f91-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="36f91-1307">**排序规则**</span><span class="sxs-lookup"><span data-stu-id="36f91-1307">**Collation**</span></span>       | <span data-ttu-id="36f91-1308">指定在对属性值执行比较和排序操作时要使用的排序序列。</span><span class="sxs-lookup"><span data-stu-id="36f91-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="36f91-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="36f91-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="36f91-1310">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1310">Yes</span></span>                              | <span data-ttu-id="36f91-1311">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1311">No</span></span>                  |
| <span data-ttu-id="36f91-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="36f91-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="36f91-1313">表示应使用属性的值来进行开放式并发检查。</span><span class="sxs-lookup"><span data-stu-id="36f91-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="36f91-1314">所有 **EDMSimpleType** 属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="36f91-1315">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1315">No</span></span>                               | <span data-ttu-id="36f91-1316">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1316">Yes</span></span>                 |
| <span data-ttu-id="36f91-1317">**默认**</span><span class="sxs-lookup"><span data-stu-id="36f91-1317">**Default**</span></span>         | <span data-ttu-id="36f91-1318">如果在安装时未提供值，则指定属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="36f91-1319">所有 **EDMSimpleType** 属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="36f91-1320">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1320">Yes</span></span>                              | <span data-ttu-id="36f91-1321">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1321">Yes</span></span>                 |
| <span data-ttu-id="36f91-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-1322">**FixedLength**</span></span>     | <span data-ttu-id="36f91-1323">指定属性值的长度是否可变。</span><span class="sxs-lookup"><span data-stu-id="36f91-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="36f91-1324">**Edm**、 **edm**</span><span class="sxs-lookup"><span data-stu-id="36f91-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="36f91-1325">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1325">Yes</span></span>                              | <span data-ttu-id="36f91-1326">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1326">No</span></span>                  |
| <span data-ttu-id="36f91-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="36f91-1327">**MaxLength**</span></span>       | <span data-ttu-id="36f91-1328">指定属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="36f91-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="36f91-1329">**Edm**、 **edm**</span><span class="sxs-lookup"><span data-stu-id="36f91-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="36f91-1330">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1330">Yes</span></span>                              | <span data-ttu-id="36f91-1331">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1331">No</span></span>                  |
| <span data-ttu-id="36f91-1332">**可以为 Null**</span><span class="sxs-lookup"><span data-stu-id="36f91-1332">**Nullable**</span></span>        | <span data-ttu-id="36f91-1333">指定属性是否可以具有 **null** 值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="36f91-1334">所有 **EDMSimpleType** 属性</span><span class="sxs-lookup"><span data-stu-id="36f91-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="36f91-1335">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1335">Yes</span></span>                              | <span data-ttu-id="36f91-1336">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1336">Yes</span></span>                 |
| <span data-ttu-id="36f91-1337">**精度**</span><span class="sxs-lookup"><span data-stu-id="36f91-1337">**Precision**</span></span>       | <span data-ttu-id="36f91-1338">对于 **Decimal**类型的属性，指定属性值可以具有的位数。</span><span class="sxs-lookup"><span data-stu-id="36f91-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="36f91-1339">对于类型为 **Time**、 **DateTime**和 **DateTimeOffset**的属性，指定属性值的秒的小数部分的位数。</span><span class="sxs-lookup"><span data-stu-id="36f91-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="36f91-1340">**Edm. DateTime**、 **edm**、 **edm**、 **edm 和 Time**</span><span class="sxs-lookup"><span data-stu-id="36f91-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="36f91-1341">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1341">Yes</span></span>                              | <span data-ttu-id="36f91-1342">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1342">No</span></span>                  |
| <span data-ttu-id="36f91-1343">**缩放**</span><span class="sxs-lookup"><span data-stu-id="36f91-1343">**Scale**</span></span>           | <span data-ttu-id="36f91-1344">指定属性值小数点右侧的位数。</span><span class="sxs-lookup"><span data-stu-id="36f91-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="36f91-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="36f91-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="36f91-1346">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1346">Yes</span></span>                              | <span data-ttu-id="36f91-1347">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1347">No</span></span>                  |
| <span data-ttu-id="36f91-1348">SRID </span><span class="sxs-lookup"><span data-stu-id="36f91-1348">**SRID**</span></span>            | <span data-ttu-id="36f91-1349">指定空间系统引用系统 ID。</span><span class="sxs-lookup"><span data-stu-id="36f91-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="36f91-1350">有关详细信息，请参阅 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="36f91-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="36f91-1351">**Edm、GeographyPoint、GeographyLineString、GeographyPolygon、GeographyMultiPoint、、、、、edm、GeographyMultiLineString、GeographyMultiPolygon、Edm、GeographyCollection、GeometryPoint、GeometryLineString、GeometryPolygon、GeometryMultiPoint、GeometryMultiLineString、GeometryMultiPolygon、GeometryCollection、、、、**</span><span class="sxs-lookup"><span data-stu-id="36f91-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="36f91-1352">否</span><span class="sxs-lookup"><span data-stu-id="36f91-1352">No</span></span>                               | <span data-ttu-id="36f91-1353">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1353">Yes</span></span>                 |
| <span data-ttu-id="36f91-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="36f91-1354">**Unicode**</span></span>         | <span data-ttu-id="36f91-1355">指示是否将属性值存储为 Unicode。</span><span class="sxs-lookup"><span data-stu-id="36f91-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="36f91-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="36f91-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="36f91-1357">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1357">Yes</span></span>                              | <span data-ttu-id="36f91-1358">是</span><span class="sxs-lookup"><span data-stu-id="36f91-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="36f91-1359">当从概念模型生成数据库时，如果属性元素位于以下命名空间中，则生成数据库向导将识别该**属性**元素的**StoreGeneratedPattern**属性值： https://schemas.microsoft.com/ado/2009/02/edm/annotation 。</span><span class="sxs-lookup"><span data-stu-id="36f91-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="36f91-1360">特性支持的值为 **Identity** 和 **计算**。</span><span class="sxs-lookup"><span data-stu-id="36f91-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="36f91-1361">**标识**值将生成一个数据库列，其中包含在数据库中生成的标识值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="36f91-1362">**计算得出**的值将生成一个列，该列具有在数据库中计算的值。</span><span class="sxs-lookup"><span data-stu-id="36f91-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="36f91-1363">示例</span><span class="sxs-lookup"><span data-stu-id="36f91-1363">Example</span></span>

<span data-ttu-id="36f91-1364">下面的示例演示了应用于实体类型的属性的方面：</span><span class="sxs-lookup"><span data-stu-id="36f91-1364">The following example shows facets applied to the properties of an entity type:</span></span>

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
