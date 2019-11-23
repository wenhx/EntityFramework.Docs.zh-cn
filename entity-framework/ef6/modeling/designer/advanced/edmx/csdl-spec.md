---
title: CSDL 规范-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
ms.openlocfilehash: 642e5977ecbbf0c474cac1ceae19d33a135aa875
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182602"
---
# <a name="csdl-specification"></a><span data-ttu-id="8a65f-102">CSDL 规范</span><span class="sxs-lookup"><span data-stu-id="8a65f-102">CSDL Specification</span></span>
<span data-ttu-id="8a65f-103">概念架构定义语言 (CSDL) 是一种基于 XML 的语言，它描述构成数据驱动应用程序的概念模型的实体、关系和函数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-103">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="8a65f-104">此概念模型可由实体框架或 WCF 数据服务使用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-104">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="8a65f-105">实体框架使用 CSDL 描述的元数据将概念模型中定义的实体和关系映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="8a65f-105">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="8a65f-106">有关详细信息，请参阅[SSDL 规范](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)和[MSL 规范](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-106">For more information, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md) and [MSL Specification](~/ef6/modeling/designer/advanced/edmx/msl-spec.md).</span></span>

<span data-ttu-id="8a65f-107">CSDL 是实体数据模型的实体框架实现。</span><span class="sxs-lookup"><span data-stu-id="8a65f-107">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="8a65f-108">在实体框架应用程序中，概念模型元数据从 csdl 文件（用 CSDL 编写）加载到 EdmItemCollection 的实例中，可以使用中的方法进行访问。"System.object" 类。</span><span class="sxs-lookup"><span data-stu-id="8a65f-108">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="8a65f-109">实体框架使用概念模型元数据将针对概念模型的查询转换为特定于数据源的命令。</span><span class="sxs-lookup"><span data-stu-id="8a65f-109">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="8a65f-110">EF 设计器会在设计时将概念模型信息存储在 .edmx 文件中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-110">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="8a65f-111">在生成时，EF 设计器使用 .edmx 文件中的信息来创建运行时实体框架所需的 csdl 文件。</span><span class="sxs-lookup"><span data-stu-id="8a65f-111">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="8a65f-112">CSDL 的版本按 XML 命名空间进行区分。</span><span class="sxs-lookup"><span data-stu-id="8a65f-112">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="8a65f-113">CSDL 版本</span><span class="sxs-lookup"><span data-stu-id="8a65f-113">CSDL Version</span></span> | <span data-ttu-id="8a65f-114">XML 命名空间</span><span class="sxs-lookup"><span data-stu-id="8a65f-114">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="8a65f-115">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="8a65f-115">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="8a65f-116">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="8a65f-116">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="8a65f-117">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="8a65f-117">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="8a65f-118">Association 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-118">Association Element (CSDL)</span></span>

<span data-ttu-id="8a65f-119">**Association**元素定义了两个实体类型之间的关系。</span><span class="sxs-lookup"><span data-stu-id="8a65f-119">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="8a65f-120">关联必须指定关系中涉及的实体类型和关系的每一端可能的实体类型数量（也称为重数）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-120">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="8a65f-121">关联端的多重性值可以为一（1）、零或一（0 ..1）或多（\*）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-121">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="8a65f-122">此信息在两个 End 子元素中指定。</span><span class="sxs-lookup"><span data-stu-id="8a65f-122">This information is specified in two child End elements.</span></span>

<span data-ttu-id="8a65f-123">通过导航属性或外键（如果在实体类型上公开了外键）可以访问关联一端的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-123">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="8a65f-124">在应用程序中，关联的实例表示实体类型的实例之间的特定关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-124">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="8a65f-125">关联实例按逻辑分组在关联集中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-125">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="8a65f-126">**Association**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-126">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-127">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-127">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-128">End （正好2个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-128">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="8a65f-129">ReferentialConstraint （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-129">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-130">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-130">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-131">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-131">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-132">下表描述了可应用于**Association**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-132">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="8a65f-133">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-133">Attribute Name</span></span> | <span data-ttu-id="8a65f-134">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-134">Is Required</span></span> | <span data-ttu-id="8a65f-135">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-135">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="8a65f-136">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-136">**Name**</span></span>       | <span data-ttu-id="8a65f-137">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-137">Yes</span></span>         | <span data-ttu-id="8a65f-138">关联的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-138">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-139">可以将任意数量的批注特性（自定义 XML 特性）应用于**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-139">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="8a65f-140">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-140">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-141">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-141">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-142">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-142">Example</span></span>

<span data-ttu-id="8a65f-143">下面的示例演示一个**association**元素，该元素定义在**Customer**和**Order**实体类型上没有公开外键时的**CustomerOrders**关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-143">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="8a65f-144">关联的每个**端点**的**多重性**值指示可以将多个**订单**与一个**客户**关联，但只能有一个**客户**与一个**订单**相关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-144">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="8a65f-145">此外， **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-145">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="8a65f-146">下面的示例演示一个**association**元素，该元素定义在**Customer**和**Order**实体类型上公开外键时的**CustomerOrders**关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-146">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="8a65f-147">公开外键后，实体之间的关系将通过**ReferentialConstraint**元素进行管理。</span><span class="sxs-lookup"><span data-stu-id="8a65f-147">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="8a65f-148">将此关联映射到数据源并不需要 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-148">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="8a65f-149">AssociationSet 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-149">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="8a65f-150">概念架构定义语言（CSDL）中的**AssociationSet**元素是同一类型的关联实例的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="8a65f-150">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="8a65f-151">关联集为对关联实例进行分组提供了定义，以便能够将它们映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="8a65f-151">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="8a65f-152">**AssociationSet**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-152">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-153">文档（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-153">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="8a65f-154">End （只需要两个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-154">End (exactly two elements required)</span></span>
-   <span data-ttu-id="8a65f-155">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-155">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="8a65f-156">**Association**特性指定关联集包含的关联类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-156">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="8a65f-157">构成关联集末尾的实体集是通过恰好两个子**End**元素指定的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-157">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-158">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-158">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-159">下表介绍可应用于**AssociationSet**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-159">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="8a65f-160">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-160">Attribute Name</span></span>  | <span data-ttu-id="8a65f-161">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-161">Is Required</span></span> | <span data-ttu-id="8a65f-162">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-162">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-163">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-163">**Name**</span></span>        | <span data-ttu-id="8a65f-164">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-164">Yes</span></span>         | <span data-ttu-id="8a65f-165">实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-165">The name of the entity set.</span></span> <span data-ttu-id="8a65f-166">**Name**特性的值不能与**Association**特性的值相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-166">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="8a65f-167">**关联**</span><span class="sxs-lookup"><span data-stu-id="8a65f-167">**Association**</span></span> | <span data-ttu-id="8a65f-168">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-168">Yes</span></span>         | <span data-ttu-id="8a65f-169">关联集包含其实例的关联的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-169">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="8a65f-170">关联必须与关联集位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-170">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-171">可以将任意数量的批注特性（自定义 XML 特性）应用于**AssociationSet**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-171">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="8a65f-172">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-172">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-173">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-173">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-174">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-174">Example</span></span>

<span data-ttu-id="8a65f-175">下面的示例演示具有两个**AssociationSet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-175">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="8a65f-176">CollectionType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-176">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="8a65f-177">概念架构定义语言（CSDL）中的**CollectionType**元素指定函数参数或函数返回类型是集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-177">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="8a65f-178">**CollectionType**元素可以是参数元素的子元素，也可以是 ReturnType （函数）元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-178">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="8a65f-179">可以使用**type**特性或以下子元素之一指定集合类型：</span><span class="sxs-lookup"><span data-stu-id="8a65f-179">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="8a65f-180">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-180">**CollectionType**</span></span>
-   <span data-ttu-id="8a65f-181">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="8a65f-181">ReferenceType</span></span>
-   <span data-ttu-id="8a65f-182">RowType</span><span class="sxs-lookup"><span data-stu-id="8a65f-182">RowType</span></span>
-   <span data-ttu-id="8a65f-183">TypeRef</span><span class="sxs-lookup"><span data-stu-id="8a65f-183">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-184">如果集合类型是使用**type**特性和子元素指定的，则模型不会进行验证。</span><span class="sxs-lookup"><span data-stu-id="8a65f-184">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-185">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-185">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-186">下表介绍可应用于**CollectionType**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-186">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="8a65f-187">请注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**小数位数**、 **Unicode**和**排序规则**特性仅适用于**edmsimpletype**的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-187">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="8a65f-188">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-188">Attribute Name</span></span>                                                          | <span data-ttu-id="8a65f-189">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-189">Is Required</span></span> | <span data-ttu-id="8a65f-190">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-190">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-191">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-191">**Type**</span></span>                                                                | <span data-ttu-id="8a65f-192">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-192">No</span></span>          | <span data-ttu-id="8a65f-193">集合的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-193">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="8a65f-194">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="8a65f-194">**Nullable**</span></span>                                                            | <span data-ttu-id="8a65f-195">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-195">No</span></span>          | <span data-ttu-id="8a65f-196">**True** （默认值）或**False** ，具体取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-196">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="8a65f-197">> 在 CSDL v1 中，复杂类型属性必须具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="8a65f-197">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="8a65f-198">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="8a65f-198">**DefaultValue**</span></span>                                                        | <span data-ttu-id="8a65f-199">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-199">No</span></span>          | <span data-ttu-id="8a65f-200">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-200">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="8a65f-201">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-201">**MaxLength**</span></span>                                                           | <span data-ttu-id="8a65f-202">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-202">No</span></span>          | <span data-ttu-id="8a65f-203">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-203">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="8a65f-204">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-204">**FixedLength**</span></span>                                                         | <span data-ttu-id="8a65f-205">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-205">No</span></span>          | <span data-ttu-id="8a65f-206">**True**或**False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="8a65f-206">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="8a65f-207">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-207">**Precision**</span></span>                                                           | <span data-ttu-id="8a65f-208">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-208">No</span></span>          | <span data-ttu-id="8a65f-209">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-209">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="8a65f-210">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-210">**Scale**</span></span>                                                               | <span data-ttu-id="8a65f-211">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-211">No</span></span>          | <span data-ttu-id="8a65f-212">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-212">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="8a65f-213">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-213">**SRID**</span></span>                                                                | <span data-ttu-id="8a65f-214">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-214">No</span></span>          | <span data-ttu-id="8a65f-215">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-215">Spatial System Reference Identifier.</span></span> <span data-ttu-id="8a65f-216">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="8a65f-216">Valid only for properties of spatial types.</span></span><span data-ttu-id="8a65f-217">   有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="8a65f-217">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="8a65f-218">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-218">**Unicode**</span></span>                                                             | <span data-ttu-id="8a65f-219">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-219">No</span></span>          | <span data-ttu-id="8a65f-220">**True**或**False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-220">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="8a65f-221">**归类**</span><span class="sxs-lookup"><span data-stu-id="8a65f-221">**Collation**</span></span>                                                           | <span data-ttu-id="8a65f-222">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-222">No</span></span>          | <span data-ttu-id="8a65f-223">指定要在数据源中使用的排序序列的字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-223">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="8a65f-224">可以将任意数量的批注特性（自定义 XML 特性）应用于**CollectionType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-224">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="8a65f-225">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-226">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-227">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-227">Example</span></span>

<span data-ttu-id="8a65f-228">下面的示例演示一个模型定义函数，该函数使用**CollectionType**元素来指定该函数返回**Person**实体类型的集合（使用**ElementType**特性指定）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-228">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="8a65f-229">下面的示例演示一个模型定义函数，该函数使用**CollectionType**元素来指定该函数返回行的集合（在**RowType**元素中指定）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-229">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="8a65f-230">下面的示例演示一个模型定义的函数，该函数使用**CollectionType**元素来指定该函数接受作为参数的**部门**实体类型的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-230">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="8a65f-231">ComplexType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-231">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="8a65f-232">**ComplexType**元素定义由**EdmSimpleType**属性或其他复杂类型组成的数据结构。</span><span class="sxs-lookup"><span data-stu-id="8a65f-232">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="8a65f-233">  复杂类型可以是实体类型或其他复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-233">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="8a65f-234">复杂类型类似于复杂类型定义数据中的实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-234">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="8a65f-235">但是，在复杂类型与实体类型之间仍存在着一些重要区别：</span><span class="sxs-lookup"><span data-stu-id="8a65f-235">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="8a65f-236">复杂类型没有标识（或键），因此不能独立存在。</span><span class="sxs-lookup"><span data-stu-id="8a65f-236">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="8a65f-237">复杂类型只能作为实体类型或其他复杂类型的属性而存在。</span><span class="sxs-lookup"><span data-stu-id="8a65f-237">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="8a65f-238">复杂类型不能参与关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-238">Complex types cannot participate in associations.</span></span> <span data-ttu-id="8a65f-239">关联的任一端都不能是复杂类型，因此不能为复杂类型定义导航属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-239">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="8a65f-240">复杂类型属性不能具有 null 值，但可以将复杂类型的每个标量属性设置为 null。</span><span class="sxs-lookup"><span data-stu-id="8a65f-240">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="8a65f-241">**ComplexType**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-241">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-242">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-242">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-243">Property（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-243">Property (zero or more elements)</span></span>
-   <span data-ttu-id="8a65f-244">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-244">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="8a65f-245">下表介绍可应用于**ComplexType**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-245">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="8a65f-246">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-246">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="8a65f-247">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-247">Is Required</span></span> | <span data-ttu-id="8a65f-248">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-248">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-249">名称</span><span class="sxs-lookup"><span data-stu-id="8a65f-249">Name</span></span>                                                                                                           | <span data-ttu-id="8a65f-250">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-250">Yes</span></span>         | <span data-ttu-id="8a65f-251">复杂类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-251">The name of the complex type.</span></span> <span data-ttu-id="8a65f-252">复杂类型的名称不能与模型作用域中的其他复杂类型、实体类型或关联的名称相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-252">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="8a65f-253">BaseType</span><span class="sxs-lookup"><span data-stu-id="8a65f-253">BaseType</span></span>                                                                                                       | <span data-ttu-id="8a65f-254">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-254">No</span></span>          | <span data-ttu-id="8a65f-255">作为所定义的复杂类型的基类型的另一个复杂类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-255">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="8a65f-256">> 此属性在 CSDL v1 中不适用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-256">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="8a65f-257">在该版本中不支持复杂类型的继承。</span><span class="sxs-lookup"><span data-stu-id="8a65f-257">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="8a65f-258">抽象</span><span class="sxs-lookup"><span data-stu-id="8a65f-258">Abstract</span></span>                                                                                                       | <span data-ttu-id="8a65f-259">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-259">No</span></span>          | <span data-ttu-id="8a65f-260">**True**或**False** （默认值），取决于复杂类型是否为抽象类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-260">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="8a65f-261">> 此属性在 CSDL v1 中不适用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-261">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="8a65f-262">该版本中的复杂类型不能是抽象类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-262">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="8a65f-263">可以将任意数量的批注特性（自定义 XML 特性）应用于**ComplexType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-263">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="8a65f-264">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-264">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-265">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-265">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-266">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-266">Example</span></span>

<span data-ttu-id="8a65f-267">下面的示例显示了一个复杂类型**Address**，其中包含**EdmSimpleType**属性**StreetAddress**、 **City**、 **StateOrProvince**、**国家/地区**和**邮政编码**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-267">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="8a65f-268">若要将复杂类型**地址**（如上所示）定义为某个实体类型的属性，必须在实体类型定义中声明该属性类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-268">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="8a65f-269">下面的示例显示了在实体类型（**发布者**）上作为复杂类型的**地址**属性：</span><span class="sxs-lookup"><span data-stu-id="8a65f-269">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="8a65f-270">DefiningExpression 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-270">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="8a65f-271">概念架构定义语言（CSDL）中的**DefiningExpression**元素包含一个定义概念模型中的函数的实体 SQL 表达式。</span><span class="sxs-lookup"><span data-stu-id="8a65f-271">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="8a65f-272">出于验证目的， **DefiningExpression**元素可以包含任意内容。</span><span class="sxs-lookup"><span data-stu-id="8a65f-272">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="8a65f-273">但是，如果**DefiningExpression**元素不包含有效的实体 SQL，则实体框架在运行时将引发异常。</span><span class="sxs-lookup"><span data-stu-id="8a65f-273">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-274">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-274">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-275">可以将任意数量的批注特性（自定义 XML 特性）应用于**DefiningExpression**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-275">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="8a65f-276">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-276">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-277">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-277">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-278">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-278">Example</span></span>

<span data-ttu-id="8a65f-279">下面的示例使用**DefiningExpression**元素来定义一个函数，该函数返回发布书籍之后的年数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-279">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="8a65f-280">**DefiningExpression**元素的内容是以实体 SQL 编写的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-280">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="8a65f-281">Dependent 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-281">Dependent Element (CSDL)</span></span>

<span data-ttu-id="8a65f-282">概念架构定义语言（CSDL）中的**依赖**元素是 ReferentialConstraint 元素的子元素，用于定义引用约束的依赖端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-282">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="8a65f-283">**ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。</span><span class="sxs-lookup"><span data-stu-id="8a65f-283">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="8a65f-284">与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-284">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="8a65f-285">引用的实体类型称为约束的*主体端*。</span><span class="sxs-lookup"><span data-stu-id="8a65f-285">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="8a65f-286">引用主体端的实体类型称为约束的*依赖端*。</span><span class="sxs-lookup"><span data-stu-id="8a65f-286">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="8a65f-287">**PropertyRef**元素用于指定哪些键引用主体端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-287">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="8a65f-288">**依赖**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-288">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-289">PropertyRef （一个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-289">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="8a65f-290">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-290">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-291">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-291">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-292">下表描述了可应用于**依赖**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-292">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="8a65f-293">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-293">Attribute Name</span></span> | <span data-ttu-id="8a65f-294">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-294">Is Required</span></span> | <span data-ttu-id="8a65f-295">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-295">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="8a65f-296">**角色**</span><span class="sxs-lookup"><span data-stu-id="8a65f-296">**Role**</span></span>       | <span data-ttu-id="8a65f-297">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-297">Yes</span></span>         | <span data-ttu-id="8a65f-298">关联的依赖端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-298">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-299">可以将任意数量的批注特性（自定义 XML 特性）应用于**依赖**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-299">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="8a65f-300">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-300">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-301">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-301">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-302">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-302">Example</span></span>

<span data-ttu-id="8a65f-303">下面的示例演示了在**PublishedBy**关联的定义中使用的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-303">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="8a65f-304">**Book**实体类型的**PublisherId**属性构成了引用约束的依赖端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-304">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="8a65f-305">Documentation 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-305">Documentation Element (CSDL)</span></span>

<span data-ttu-id="8a65f-306">概念架构定义语言（CSDL）中的**文档**元素可用于提供有关在父元素中定义的对象的信息。</span><span class="sxs-lookup"><span data-stu-id="8a65f-306">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="8a65f-307">在 .edmx 文件中，当**文档**元素是元素的子元素，而该元素显示为 EF 设计器设计图面上的对象（如实体、关联或属性）时，**文档**元素的内容将显示在对象的 Visual Studio **属性**窗口中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-307">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="8a65f-308">**文档**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-308">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-309">**摘要**：父元素的简要说明。</span><span class="sxs-lookup"><span data-stu-id="8a65f-309">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="8a65f-310">（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-310">(zero or one element)</span></span>
-   <span data-ttu-id="8a65f-311">**LongDescription**：父元素的详细说明。</span><span class="sxs-lookup"><span data-stu-id="8a65f-311">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="8a65f-312">（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-312">(zero or one element)</span></span>
-   <span data-ttu-id="8a65f-313">批注元素.</span><span class="sxs-lookup"><span data-stu-id="8a65f-313">Annotation elements.</span></span> <span data-ttu-id="8a65f-314">（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-314">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-315">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-315">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-316">可以将任意数量的批注特性（自定义 XML 特性）应用于**文档**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-316">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="8a65f-317">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-317">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-318">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-318">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-319">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-319">Example</span></span>

<span data-ttu-id="8a65f-320">下面的示例将**文档**元素显示为 EntityType 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-320">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="8a65f-321">如果下面的代码片段在 .edmx 文件的 CSDL 内容中，则当你单击 `Customer` 实体类型时， **Summary**和**LongDescription**元素的内容将显示在 Visual Studio 的 " **属性**" 窗口中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-321">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="8a65f-322">End 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-322">End Element (CSDL)</span></span>

<span data-ttu-id="8a65f-323">概念架构定义语言（CSDL）中的**End**元素可以是 Association 元素或 AssociationSet 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-323">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="8a65f-324">在每种情况下， **End**元素的角色不同，适用的属性也不同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-324">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="8a65f-325">End 元素作为 Association 元素的子元素</span><span class="sxs-lookup"><span data-stu-id="8a65f-325">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="8a65f-326">**结束**元素（作为**association**元素的子元素）标识了关联一端的实体类型以及该关联端可以存在的实体类型实例的数量。</span><span class="sxs-lookup"><span data-stu-id="8a65f-326">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="8a65f-327">关联端定义为关联的一部分；关联必须正好有两个关联端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-327">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="8a65f-328">通过导航属性或外键（如果实体类型上有）可以访问关联某一端的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-328">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="8a65f-329">**结束**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-329">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-330">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-330">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-331">OnDelete （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-331">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-332">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-332">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-333">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-333">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-334">下表描述了当**结束**元素是**Association**元素的子元素时，可应用于该元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-334">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="8a65f-335">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-335">Attribute Name</span></span>   | <span data-ttu-id="8a65f-336">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-336">Is Required</span></span> | <span data-ttu-id="8a65f-337">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-337">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-338">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-338">**Type**</span></span>         | <span data-ttu-id="8a65f-339">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-339">Yes</span></span>         | <span data-ttu-id="8a65f-340">关联一端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-340">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="8a65f-341">**角色**</span><span class="sxs-lookup"><span data-stu-id="8a65f-341">**Role**</span></span>         | <span data-ttu-id="8a65f-342">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-342">No</span></span>          | <span data-ttu-id="8a65f-343">关联端的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-343">A name for the association end.</span></span> <span data-ttu-id="8a65f-344">如果不提供名称，将使用关联端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-344">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="8a65f-345">**多样性**</span><span class="sxs-lookup"><span data-stu-id="8a65f-345">**Multiplicity**</span></span> | <span data-ttu-id="8a65f-346">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-346">Yes</span></span>         | <span data-ttu-id="8a65f-347">**1**、 **0、1**或 **\*** ，具体取决于可在关联末尾的实体类型实例的数量。</span><span class="sxs-lookup"><span data-stu-id="8a65f-347">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="8a65f-348">**1**指示关联端刚好存在一个实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-348">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="8a65f-349">**0 .0**表示在关联端存在零个或一个实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-349">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="8a65f-350">**\*** 指示关联端存在零个、一个或多个实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-350">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-351">可以将任意数量的批注特性（自定义 XML 特性）应用于**结束**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-351">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="8a65f-352">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-352">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-353">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-353">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="8a65f-354">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-354">Example</span></span>

<span data-ttu-id="8a65f-355">下面的示例演示了一个定义**CustomerOrders**关联的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-355">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="8a65f-356">关联的每个**端点**的**多重性**值指示可以将多个**订单**与一个**客户**关联，但只能有一个**客户**与一个**订单**相关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-356">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="8a65f-357">此外， **OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-357">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="8a65f-358">End 元素作为 AssociationSet 元素的子元素</span><span class="sxs-lookup"><span data-stu-id="8a65f-358">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="8a65f-359">**End**元素指定关联集的一端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-359">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="8a65f-360">**AssociationSet**元素必须包含两个**结束**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-360">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="8a65f-361">**End**元素中包含的信息用于将关联集映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="8a65f-361">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="8a65f-362">**结束**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-362">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-363">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-363">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-364">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-364">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-365">Annotation 元素必须出现在所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="8a65f-365">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="8a65f-366">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-366">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-367">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-367">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-368">下表描述了当**结束**元素为**AssociationSet**元素的子元素时，可应用于该元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-368">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="8a65f-369">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-369">Attribute Name</span></span> | <span data-ttu-id="8a65f-370">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-370">Is Required</span></span> | <span data-ttu-id="8a65f-371">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-371">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-372">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="8a65f-372">**EntitySet**</span></span>  | <span data-ttu-id="8a65f-373">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-373">Yes</span></span>         | <span data-ttu-id="8a65f-374">定义父**AssociationSet**元素一端的**EntitySet**元素的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-374">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="8a65f-375">必须在与父**AssociationSet**元素相同的实体容器中定义**EntitySet**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-375">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="8a65f-376">**角色**</span><span class="sxs-lookup"><span data-stu-id="8a65f-376">**Role**</span></span>       | <span data-ttu-id="8a65f-377">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-377">No</span></span>          | <span data-ttu-id="8a65f-378">关联集端的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-378">The name of the association set end.</span></span> <span data-ttu-id="8a65f-379">如果未使用**Role**属性，则关联集端的名称将为实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-379">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="8a65f-380">可以将任意数量的批注特性（自定义 XML 特性）应用于**结束**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-380">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="8a65f-381">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-381">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-382">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-382">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="8a65f-383">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-383">Example</span></span>

<span data-ttu-id="8a65f-384">下面的示例演示具有两个**AssociationSet**元素的**EntityContainer**元素，每个元素都有两个**End**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-384">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="8a65f-385">EntityContainer 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-385">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="8a65f-386">概念架构定义语言（CSDL）中的**EntityContainer**元素是实体集、关联集和函数导入的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="8a65f-386">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="8a65f-387">概念模型实体容器通过 EntityContainerMapping 元素映射到存储模型实体容器。</span><span class="sxs-lookup"><span data-stu-id="8a65f-387">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="8a65f-388">存储模型实体容器描述数据库的结构：实体集描述表、关联集描述外键约束、函数导入描述数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="8a65f-388">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="8a65f-389">**EntityContainer**元素可以有零个或一个文档元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-389">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="8a65f-390">如果存在**文档**元素，则它必须位于所有**EntitySet**、 **AssociationSet**和**FunctionImport**元素之前。</span><span class="sxs-lookup"><span data-stu-id="8a65f-390">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="8a65f-391">**EntityContainer**元素可包含零个或多个下列子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-391">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-392">EntitySet</span><span class="sxs-lookup"><span data-stu-id="8a65f-392">EntitySet</span></span>
-   <span data-ttu-id="8a65f-393">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="8a65f-393">AssociationSet</span></span>
-   <span data-ttu-id="8a65f-394">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="8a65f-394">FunctionImport</span></span>
-   <span data-ttu-id="8a65f-395">批注元素</span><span class="sxs-lookup"><span data-stu-id="8a65f-395">Annotation elements</span></span>

<span data-ttu-id="8a65f-396">可以扩展**EntityContainer**元素，使其包含同一命名空间中的另一个**entitycontainer**的内容。</span><span class="sxs-lookup"><span data-stu-id="8a65f-396">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="8a65f-397">若要包含另一个**entitycontainer**的内容，请在引用**entitycontainer**元素中，将 "**扩展**" 属性的值设置为要包括的**EntityContainer**元素的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-397">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="8a65f-398">包含的**entitycontainer**元素的所有子元素将被视为引用**entitycontainer**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-398">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-399">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-399">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-400">下表描述了可应用于**Using**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-400">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="8a65f-401">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-401">Attribute Name</span></span> | <span data-ttu-id="8a65f-402">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-402">Is Required</span></span> | <span data-ttu-id="8a65f-403">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-403">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="8a65f-404">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-404">**Name**</span></span>       | <span data-ttu-id="8a65f-405">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-405">Yes</span></span>         | <span data-ttu-id="8a65f-406">实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-406">The name of the entity container.</span></span>                               |
| <span data-ttu-id="8a65f-407">**延续**</span><span class="sxs-lookup"><span data-stu-id="8a65f-407">**Extends**</span></span>    | <span data-ttu-id="8a65f-408">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-408">No</span></span>          | <span data-ttu-id="8a65f-409">同一命名空间中另一实体容器的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-409">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-410">可以将任意数量的批注特性（自定义 XML 特性）应用于**EntityContainer**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-410">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="8a65f-411">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-411">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-412">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-412">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-413">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-413">Example</span></span>

<span data-ttu-id="8a65f-414">下面的示例演示一个**EntityContainer**元素，该元素定义三个实体集和两个关联集。</span><span class="sxs-lookup"><span data-stu-id="8a65f-414">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="8a65f-415">EntitySet 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-415">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="8a65f-416">概念架构定义语言中的**EntitySet**元素是某个实体类型的实例和从该实体类型派生的任何类型的实例的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="8a65f-416">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="8a65f-417">实体类型与实体集之间的关系类似于关系数据库中行与表之间的关系。</span><span class="sxs-lookup"><span data-stu-id="8a65f-417">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="8a65f-418">实体类型与行类似，它定义一组相关数据；而实体集与表类似，它包含该定义的实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-418">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="8a65f-419">实体集为对实体类型实例分组提供了一个构造，以便能够将它们映射到数据源中的相关数据结构。</span><span class="sxs-lookup"><span data-stu-id="8a65f-419">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="8a65f-420">可以为特定实体类型定义多个实体集。</span><span class="sxs-lookup"><span data-stu-id="8a65f-420">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-421">EF 设计器不支持每个类型包含多个实体集的概念模型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-421">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="8a65f-422">**EntitySet**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-422">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-423">文档元素（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-423">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="8a65f-424">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-424">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-425">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-425">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-426">下表描述了可应用于**EntitySet**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-426">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="8a65f-427">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-427">Attribute Name</span></span> | <span data-ttu-id="8a65f-428">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-428">Is Required</span></span> | <span data-ttu-id="8a65f-429">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-429">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-430">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-430">**Name**</span></span>       | <span data-ttu-id="8a65f-431">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-431">Yes</span></span>         | <span data-ttu-id="8a65f-432">实体集的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-432">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="8a65f-433">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-433">**EntityType**</span></span> | <span data-ttu-id="8a65f-434">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-434">Yes</span></span>         | <span data-ttu-id="8a65f-435">实体集包含其实例的实体类型的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-435">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-436">可以向**EntitySet**元素应用任意数量的批注特性（自定义 XML 特性）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-436">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="8a65f-437">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-437">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-438">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-438">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-439">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-439">Example</span></span>

<span data-ttu-id="8a65f-440">下面的示例演示具有三个**EntitySet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-440">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="8a65f-441">每个类型可以定义多个实体集 (MEST)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-441">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="8a65f-442">下面的示例定义了一个实体容器，其中包含**书籍**实体类型的两个实体集：</span><span class="sxs-lookup"><span data-stu-id="8a65f-442">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="8a65f-443">EntityType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-443">EntityType Element (CSDL)</span></span>

<span data-ttu-id="8a65f-444">**EntityType**元素表示概念模型中的顶级概念（例如客户或订单）的结构。</span><span class="sxs-lookup"><span data-stu-id="8a65f-444">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="8a65f-445">实体类型是应用程序中实体类型实例的模板。</span><span class="sxs-lookup"><span data-stu-id="8a65f-445">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="8a65f-446">每个模板都包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="8a65f-446">Each template contains the following information:</span></span>

-   <span data-ttu-id="8a65f-447">唯一名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-447">A unique name.</span></span> <span data-ttu-id="8a65f-448">（必选。）</span><span class="sxs-lookup"><span data-stu-id="8a65f-448">(Required.)</span></span>
-   <span data-ttu-id="8a65f-449">由一个或多个属性定义的实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-449">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="8a65f-450">（必选。）</span><span class="sxs-lookup"><span data-stu-id="8a65f-450">(Required.)</span></span>
-   <span data-ttu-id="8a65f-451">用于包含数据的属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-451">Properties for containing data.</span></span> <span data-ttu-id="8a65f-452">（可选）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-452">(Optional.)</span></span>
-   <span data-ttu-id="8a65f-453">导航属性，用于从关联的一端导航至另一端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-453">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="8a65f-454">（可选）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-454">(Optional.)</span></span>

<span data-ttu-id="8a65f-455">在应用程序中，实体类型的实例表示一个特定对象（例如特定客户或订单）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-455">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="8a65f-456">实体类型的每个实例在实体集中都必须具有唯一的实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-456">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="8a65f-457">只有两个实体类型实例的类型相同且其实体键的值也相同时，才认为它们是相等的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-457">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="8a65f-458">**EntityType**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-458">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-459">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-459">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-460">Key（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-460">Key (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-461">Property（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-461">Property (zero or more elements)</span></span>
-   <span data-ttu-id="8a65f-462">NavigationProperty（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-462">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="8a65f-463">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-463">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-464">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-464">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-465">下表介绍可应用于**EntityType**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-465">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="8a65f-466">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-466">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="8a65f-467">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-467">Is Required</span></span> | <span data-ttu-id="8a65f-468">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-468">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-469">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-469">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="8a65f-470">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-470">Yes</span></span>         | <span data-ttu-id="8a65f-471">实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-471">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="8a65f-472">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-472">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="8a65f-473">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-473">No</span></span>          | <span data-ttu-id="8a65f-474">实体类型的名称，该实体类型是所定义的另一个实体类型的基类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-474">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="8a65f-475">**抽象**</span><span class="sxs-lookup"><span data-stu-id="8a65f-475">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="8a65f-476">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-476">No</span></span>          | <span data-ttu-id="8a65f-477">**True**或**False**，具体取决于实体类型是否为抽象类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-477">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="8a65f-478">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-478">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="8a65f-479">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-479">No</span></span>          | <span data-ttu-id="8a65f-480">**True**或**False** ，具体取决于实体类型是否为开放式实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-480">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="8a65f-481">> **OpenType**特性仅适用于在与 ADO.NET Data Services 一起使用的概念模型中定义的实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-481">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="8a65f-482">可以向**EntityType**元素应用任意数量的批注特性（自定义 XML 特性）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-482">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="8a65f-483">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-483">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-484">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-484">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-485">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-485">Example</span></span>

<span data-ttu-id="8a65f-486">下面的示例演示一个具有三个**属性**元素和两个**NavigationProperty**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-486">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="8a65f-487">EnumType 元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="8a65f-487">EnumType Element (CSDL)</span></span>

<span data-ttu-id="8a65f-488">**EnumType**元素表示一个枚举类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-488">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="8a65f-489">**EnumType**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-489">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-490">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-490">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-491">成员（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-491">Member (zero or more elements)</span></span>
-   <span data-ttu-id="8a65f-492">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-492">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-493">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-493">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-494">下表介绍可应用于**EnumType**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-494">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="8a65f-495">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-495">Attribute Name</span></span>     | <span data-ttu-id="8a65f-496">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-496">Is Required</span></span> | <span data-ttu-id="8a65f-497">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-497">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-498">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-498">**Name**</span></span>           | <span data-ttu-id="8a65f-499">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-499">Yes</span></span>         | <span data-ttu-id="8a65f-500">实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-500">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="8a65f-501">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="8a65f-501">**IsFlags**</span></span>        | <span data-ttu-id="8a65f-502">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-502">No</span></span>          | <span data-ttu-id="8a65f-503">**True**或**False**，具体取决于枚举类型是否可用作一组标志。</span><span class="sxs-lookup"><span data-stu-id="8a65f-503">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="8a65f-504">默认值为**False。**</span><span class="sxs-lookup"><span data-stu-id="8a65f-504">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="8a65f-505">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-505">**UnderlyingType**</span></span> | <span data-ttu-id="8a65f-506">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-506">No</span></span>          | <span data-ttu-id="8a65f-507">**Edm**、 **edm**、 **edm**、 **edm**或**edm** ，定义类型的值的范围的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-507">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="8a65f-508">  枚举元素的默认基础类型为**Edm**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-508">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-509">可以将任意数量的批注特性（自定义 XML 特性）应用于**EnumType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-509">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="8a65f-510">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-510">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-511">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-511">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-512">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-512">Example</span></span>

<span data-ttu-id="8a65f-513">下面的示例演示具有三个**成员**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-513">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="8a65f-514">Function 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-514">Function Element (CSDL)</span></span>

<span data-ttu-id="8a65f-515">概念架构定义语言（CSDL）中的**函数**元素用于在概念模型中定义或声明函数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-515">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="8a65f-516">函数通过使用 DefiningExpression 元素来定义。</span><span class="sxs-lookup"><span data-stu-id="8a65f-516">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="8a65f-517">**函数**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-517">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-518">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-518">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-519">Parameter（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-519">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="8a65f-520">DefiningExpression（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-520">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-521">ReturnType （函数）（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-521">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-522">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-522">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="8a65f-523">函数的返回类型必须与**returntype** （function）元素或**returntype**属性（见下文）一起指定，但不能同时指定两者。</span><span class="sxs-lookup"><span data-stu-id="8a65f-523">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="8a65f-524">可能的返回类型为任何 EdmSimpleType、实体类型、复杂类型、行类型或引用类型（或这些类型之一的集合）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-524">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-525">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-525">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-526">下表描述了可应用于**Function**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-526">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="8a65f-527">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-527">Attribute Name</span></span> | <span data-ttu-id="8a65f-528">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-528">Is Required</span></span> | <span data-ttu-id="8a65f-529">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-529">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="8a65f-530">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-530">**Name**</span></span>       | <span data-ttu-id="8a65f-531">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-531">Yes</span></span>         | <span data-ttu-id="8a65f-532">函数名。</span><span class="sxs-lookup"><span data-stu-id="8a65f-532">The name of the function.</span></span>          |
| <span data-ttu-id="8a65f-533">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-533">**ReturnType**</span></span> | <span data-ttu-id="8a65f-534">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-534">No</span></span>          | <span data-ttu-id="8a65f-535">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-535">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-536">可以将任意数量的批注特性（自定义 XML 特性）应用于**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-536">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="8a65f-537">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-537">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-538">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-538">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-539">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-539">Example</span></span>

<span data-ttu-id="8a65f-540">下面的示例使用**function**元素来定义一个函数，该函数返回一个指导员后的年数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-540">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="8a65f-541">FunctionImport 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-541">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="8a65f-542">概念性架构定义语言（CSDL）中的**FunctionImport**元素表示在数据源中定义但可通过概念模型用于对象的函数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-542">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="8a65f-543">例如，存储模型中的 Function 元素可用于表示数据库中的存储过程。</span><span class="sxs-lookup"><span data-stu-id="8a65f-543">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="8a65f-544">概念模型中的**FunctionImport**元素表示实体框架应用程序中的相应函数并通过使用 FunctionImportMapping 元素映射到存储模型函数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-544">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="8a65f-545">在应用程序中调用函数时，会在数据库中执行相应的存储过程。</span><span class="sxs-lookup"><span data-stu-id="8a65f-545">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="8a65f-546">**FunctionImport**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-546">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-547">文档（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-547">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="8a65f-548">Parameter（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-548">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="8a65f-549">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-549">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="8a65f-550">ReturnType （FunctionImport）（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-550">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="8a65f-551">应为函数接受的每个参数定义一个**参数**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-551">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="8a65f-552">函数的返回类型必须与**returntype** （FunctionImport）元素或**returntype**属性（见下文）一起指定，但不能同时指定两者。</span><span class="sxs-lookup"><span data-stu-id="8a65f-552">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="8a65f-553">返回类型值必须是 EdmSimpleType、EntityType 或 ComplexType 的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-553">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-554">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-554">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-555">下表介绍可应用于**FunctionImport**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-555">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="8a65f-556">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-556">Attribute Name</span></span>   | <span data-ttu-id="8a65f-557">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-557">Is Required</span></span> | <span data-ttu-id="8a65f-558">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-558">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-559">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-559">**Name**</span></span>         | <span data-ttu-id="8a65f-560">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-560">Yes</span></span>         | <span data-ttu-id="8a65f-561">导入的函数的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-561">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="8a65f-562">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-562">**ReturnType**</span></span>   | <span data-ttu-id="8a65f-563">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-563">No</span></span>          | <span data-ttu-id="8a65f-564">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-564">The type that the function returns.</span></span> <span data-ttu-id="8a65f-565">如果函数不返回值，不要使用此特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-565">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="8a65f-566">否则，该值必须是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-566">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="8a65f-567">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="8a65f-567">**EntitySet**</span></span>    | <span data-ttu-id="8a65f-568">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-568">No</span></span>          | <span data-ttu-id="8a65f-569">如果函数返回实体类型的集合，则**EntitySet**的值必须是该集合所属的实体集。</span><span class="sxs-lookup"><span data-stu-id="8a65f-569">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="8a65f-570">否则，不能使用**EntitySet**特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-570">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="8a65f-571">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="8a65f-571">**IsComposable**</span></span> | <span data-ttu-id="8a65f-572">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-572">No</span></span>          | <span data-ttu-id="8a65f-573">如果将该值设置为 true，则该函数是可组合的（表值函数），可以在 LINQ 查询中使用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-573">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="8a65f-574">  默认值为**false**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-574">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="8a65f-575">可以将任意数量的批注特性（自定义 XML 特性）应用于**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-575">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="8a65f-576">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-576">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-577">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-577">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-578">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-578">Example</span></span>

<span data-ttu-id="8a65f-579">下面的示例演示一个**FunctionImport**元素，该元素接受一个参数并返回实体类型的集合：</span><span class="sxs-lookup"><span data-stu-id="8a65f-579">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="8a65f-580">Key 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-580">Key Element (CSDL)</span></span>

<span data-ttu-id="8a65f-581">**Key**元素是 EntityType 元素的子元素，用于定义*实体键*（用于确定标识的实体类型的一个或一组属性）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-581">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="8a65f-582">构成实体键的属性是在设计时选择的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-582">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="8a65f-583">实体键属性的值必须在运行时唯一标识实体集中的实体类型实例。</span><span class="sxs-lookup"><span data-stu-id="8a65f-583">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="8a65f-584">在选择构成实体键的属性时应确保实例在实体集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-584">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="8a65f-585">**Key**元素通过引用实体类型的一个或多个属性来定义实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-585">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="8a65f-586">**Key**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-586">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="8a65f-587">PropertyRef （一个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-587">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="8a65f-588">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-588">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-589">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-589">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-590">可以将任意数量的批注特性（自定义 XML 特性）应用于**该键**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-590">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="8a65f-591">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-592">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-593">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-593">Example</span></span>

<span data-ttu-id="8a65f-594">下面的示例定义了一个名为**Book**的实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-594">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="8a65f-595">实体键通过引用实体类型的**ISBN**属性来定义。</span><span class="sxs-lookup"><span data-stu-id="8a65f-595">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="8a65f-596">由于国际标准图书编号（ISBN）唯一标识书籍，因此**ISBN**属性是实体键的一个不错选择。</span><span class="sxs-lookup"><span data-stu-id="8a65f-596">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="8a65f-597">下面的示例演示一个实体类型（**作者**），其中包含一个由两个属性、**名称**和**地址**组成的实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-597">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="8a65f-598">使用实体键的**名称**和**地址**是合理的选择，因为同一名称的两个作者不会居住在同一地址。</span><span class="sxs-lookup"><span data-stu-id="8a65f-598">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="8a65f-599">但是，针对实体键的这种选择并不能绝对确保实体键在实体集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-599">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="8a65f-600">在这种情况下，建议添加可用于唯一标识作者的属性，例如**AuthorId**：。</span><span class="sxs-lookup"><span data-stu-id="8a65f-600">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="8a65f-601">Member 元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="8a65f-601">Member Element (CSDL)</span></span>

<span data-ttu-id="8a65f-602">**Member**元素是 EnumType 元素的子元素，用于定义枚举类型的成员。</span><span class="sxs-lookup"><span data-stu-id="8a65f-602">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-603">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-603">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-604">下表介绍可应用于**FunctionImport**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-604">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="8a65f-605">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-605">Attribute Name</span></span> | <span data-ttu-id="8a65f-606">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-606">Is Required</span></span> | <span data-ttu-id="8a65f-607">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-607">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-608">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-608">**Name**</span></span>       | <span data-ttu-id="8a65f-609">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-609">Yes</span></span>         | <span data-ttu-id="8a65f-610">成员名。</span><span class="sxs-lookup"><span data-stu-id="8a65f-610">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="8a65f-611">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="8a65f-611">**Value**</span></span>      | <span data-ttu-id="8a65f-612">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-612">No</span></span>          | <span data-ttu-id="8a65f-613">成员的值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-613">The value of the member.</span></span> <span data-ttu-id="8a65f-614">默认情况下，第一个成员的值为0，并且每个连续枚举数的值将增加1。</span><span class="sxs-lookup"><span data-stu-id="8a65f-614">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="8a65f-615">可能存在多个具有相同值的成员。</span><span class="sxs-lookup"><span data-stu-id="8a65f-615">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-616">可以将任意数量的批注特性（自定义 XML 特性）应用于**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-616">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="8a65f-617">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-617">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-618">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-618">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-619">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-619">Example</span></span>

<span data-ttu-id="8a65f-620">下面的示例演示具有三个**成员**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-620">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="8a65f-621">NavigationProperty 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-621">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="8a65f-622">**NavigationProperty**元素定义导航属性，该属性提供对关联的另一端的引用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-622">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="8a65f-623">与使用 Property 元素定义的属性不同，导航属性不定义数据的形状和特征。</span><span class="sxs-lookup"><span data-stu-id="8a65f-623">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="8a65f-624">它们提供了一种在两个实体类型之间导航关联的方法。</span><span class="sxs-lookup"><span data-stu-id="8a65f-624">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="8a65f-625">注意，对于关联两端的两种实体类型，导航属性都是可选的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-625">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="8a65f-626">如果您对于位于关联一端的实体类型定义一个导航属性，则不需要对于位于关联另一端的实体类型定义导航属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-626">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="8a65f-627">导航属性返回的数据类型是由其远程关联端的重数确定的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-627">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="8a65f-628">例如，假设有一个导航属性**OrdersNavProp**，该属性存在于**客户**实体类型上，并在**customer**和**Order**之间导航一对多关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-628">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="8a65f-629">因为导航属性的远程关联端的重数为 many （\*），所以其数据类型为集合（**顺序**）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-629">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="8a65f-630">同样，如果 "**订单**" 实体类型上存在导航属性**CustomerNavProp**，则其数据类型为**Customer** ，因为远程端的重数为一（1）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-630">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="8a65f-631">**NavigationProperty**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-631">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-632">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-632">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-633">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-633">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-634">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-634">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-635">下表介绍可应用于**NavigationProperty**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-635">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="8a65f-636">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-636">Attribute Name</span></span>   | <span data-ttu-id="8a65f-637">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-637">Is Required</span></span> | <span data-ttu-id="8a65f-638">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-638">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-639">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-639">**Name**</span></span>         | <span data-ttu-id="8a65f-640">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-640">Yes</span></span>         | <span data-ttu-id="8a65f-641">导航属性的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-641">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="8a65f-642">**关系**</span><span class="sxs-lookup"><span data-stu-id="8a65f-642">**Relationship**</span></span> | <span data-ttu-id="8a65f-643">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-643">Yes</span></span>         | <span data-ttu-id="8a65f-644">处于模型的作用域中的关联的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-644">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="8a65f-645">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="8a65f-645">**ToRole**</span></span>       | <span data-ttu-id="8a65f-646">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-646">Yes</span></span>         | <span data-ttu-id="8a65f-647">导航在此结束的关联端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-647">The end of the association at which navigation ends.</span></span> <span data-ttu-id="8a65f-648">**ToRole**属性的值必须与某个关联端上定义的某个**角色**属性（在 AssociationEnd 元素中定义）的值相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-648">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="8a65f-649">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="8a65f-649">**FromRole**</span></span>     | <span data-ttu-id="8a65f-650">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-650">Yes</span></span>         | <span data-ttu-id="8a65f-651">导航从其开始的关联端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-651">The end of the association from which navigation begins.</span></span> <span data-ttu-id="8a65f-652">**FromRole**属性的值必须与某个关联端上定义的某个**角色**属性（在 AssociationEnd 元素中定义）的值相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-652">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-653">可以将任意数量的批注特性（自定义 XML 特性）应用于**NavigationProperty**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-653">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="8a65f-654">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-654">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-655">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-655">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-656">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-656">Example</span></span>

<span data-ttu-id="8a65f-657">下面的示例定义了一个具有两个导航属性（**PublishedBy**和**WrittenBy**）的实体类型（**Book**）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-657">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="8a65f-658">OnDelete 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-658">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="8a65f-659">概念架构定义语言（CSDL）中的**OnDelete**元素定义与关联关联的行为。</span><span class="sxs-lookup"><span data-stu-id="8a65f-659">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="8a65f-660">如果在关联的一端将**Action**特性设置为**Cascade** ，则在删除第一条上的实体类型时，将删除关联的另一端上的相关实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-660">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="8a65f-661">如果两个实体类型之间的关联是主键到主键关系，则当删除关联的另一端上的主体对象时，将删除加载的依赖对象，而不考虑**OnDelete**规范。</span><span class="sxs-lookup"><span data-stu-id="8a65f-661">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="8a65f-662">**OnDelete**元素只影响应用程序的运行时行为;它不会影响数据源中的行为。</span><span class="sxs-lookup"><span data-stu-id="8a65f-662">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="8a65f-663">在数据源中定义的行为应与在应用程序中定义的行为相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-663">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="8a65f-664">**OnDelete**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-664">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-665">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-665">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-666">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-666">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-667">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-667">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-668">下表介绍可应用于**OnDelete**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-668">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="8a65f-669">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-669">Attribute Name</span></span> | <span data-ttu-id="8a65f-670">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-670">Is Required</span></span> | <span data-ttu-id="8a65f-671">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-671">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-672">**Action**</span><span class="sxs-lookup"><span data-stu-id="8a65f-672">**Action**</span></span>     | <span data-ttu-id="8a65f-673">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-673">Yes</span></span>         | <span data-ttu-id="8a65f-674">**Cascade**或**None**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-674">**Cascade** or **None**.</span></span> <span data-ttu-id="8a65f-675">如果在删除主体实体类型时将删除**级联**的依赖实体类型，则为。</span><span class="sxs-lookup"><span data-stu-id="8a65f-675">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="8a65f-676">如果为 "**无**"，则在删除主体实体类型时不会删除从属实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-676">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-677">可以将任意数量的批注特性（自定义 XML 特性）应用于**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-677">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="8a65f-678">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-678">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-679">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-679">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-680">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-680">Example</span></span>

<span data-ttu-id="8a65f-681">下面的示例演示了一个定义**CustomerOrders**关联的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-681">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="8a65f-682">**OnDelete**元素指示在删除**客户**时，将删除与特定**客户**相关且已加载到 ObjectContext 的所有**订单**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-682">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="8a65f-683">Parameter 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-683">Parameter Element (CSDL)</span></span>

<span data-ttu-id="8a65f-684">概念架构定义语言（CSDL）中的**Parameter**元素可以是 FunctionImport 元素或 Function 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-684">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="8a65f-685">FunctionImport 元素应用程序</span><span class="sxs-lookup"><span data-stu-id="8a65f-685">FunctionImport Element Application</span></span>

<span data-ttu-id="8a65f-686">**Parameter**元素（作为**FunctionImport**元素的子元素）用于定义在 CSDL 中声明的函数导入的输入和输出参数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-686">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="8a65f-687">**Parameter**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-687">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-688">文档（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-688">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="8a65f-689">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-689">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-690">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-690">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-691">下表介绍可应用于**参数**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-691">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="8a65f-692">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-692">Attribute Name</span></span> | <span data-ttu-id="8a65f-693">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-693">Is Required</span></span> | <span data-ttu-id="8a65f-694">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-694">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-695">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-695">**Name**</span></span>       | <span data-ttu-id="8a65f-696">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-696">Yes</span></span>         | <span data-ttu-id="8a65f-697">参数的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-697">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="8a65f-698">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-698">**Type**</span></span>       | <span data-ttu-id="8a65f-699">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-699">Yes</span></span>         | <span data-ttu-id="8a65f-700">参数类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-700">The parameter type.</span></span> <span data-ttu-id="8a65f-701">该值必须是**EDMSimpleType** ，或者是模型作用域内的复杂类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-701">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="8a65f-702">**众**</span><span class="sxs-lookup"><span data-stu-id="8a65f-702">**Mode**</span></span>       | <span data-ttu-id="8a65f-703">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-703">No</span></span>          | <span data-ttu-id="8a65f-704">**In**、 **Out**或**InOut** ，具体取决于参数是输入、输出还是输入/输出参数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-704">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="8a65f-705">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-705">**MaxLength**</span></span>  | <span data-ttu-id="8a65f-706">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-706">No</span></span>          | <span data-ttu-id="8a65f-707">参数的最大允许长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-707">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="8a65f-708">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-708">**Precision**</span></span>  | <span data-ttu-id="8a65f-709">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-709">No</span></span>          | <span data-ttu-id="8a65f-710">参数的精度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-710">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="8a65f-711">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-711">**Scale**</span></span>      | <span data-ttu-id="8a65f-712">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-712">No</span></span>          | <span data-ttu-id="8a65f-713">参数的小数位数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-713">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="8a65f-714">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-714">**SRID**</span></span>       | <span data-ttu-id="8a65f-715">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-715">No</span></span>          | <span data-ttu-id="8a65f-716">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-716">Spatial System Reference Identifier.</span></span> <span data-ttu-id="8a65f-717">仅对空间类型的参数有效。</span><span class="sxs-lookup"><span data-stu-id="8a65f-717">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="8a65f-718">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-718">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-719">可以将任意数量的批注特性（自定义 XML 特性）应用于**参数**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-719">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="8a65f-720">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-720">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-721">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-721">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="8a65f-722">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-722">Example</span></span>

<span data-ttu-id="8a65f-723">下面的示例演示一个具有一个**参数**子元素的**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-723">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="8a65f-724">函数接受一个输入参数并返回实体类型的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-724">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="8a65f-725">Function 元素应用程序</span><span class="sxs-lookup"><span data-stu-id="8a65f-725">Function Element Application</span></span>

<span data-ttu-id="8a65f-726">**Parameter**元素（作为**Function**元素的子元素）为在概念模型中定义或声明的函数定义参数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-726">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="8a65f-727">**Parameter**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-727">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-728">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-728">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="8a65f-729">CollectionType （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-729">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="8a65f-730">ReferenceType （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-730">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="8a65f-731">RowType （零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-731">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-732">只有一个**CollectionType**、 **ReferenceType**或**RowType**元素可以是**属性**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-732">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="8a65f-733">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-733">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-734">Annotation 元素必须出现在所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="8a65f-734">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="8a65f-735">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-735">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-736">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-736">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-737">下表介绍可应用于**参数**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-737">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="8a65f-738">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-738">Attribute Name</span></span>   | <span data-ttu-id="8a65f-739">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-739">Is Required</span></span> | <span data-ttu-id="8a65f-740">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-740">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-741">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-741">**Name**</span></span>         | <span data-ttu-id="8a65f-742">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-742">Yes</span></span>         | <span data-ttu-id="8a65f-743">参数的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-743">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="8a65f-744">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-744">**Type**</span></span>         | <span data-ttu-id="8a65f-745">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-745">No</span></span>          | <span data-ttu-id="8a65f-746">参数类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-746">The parameter type.</span></span> <span data-ttu-id="8a65f-747">参数可以是以下任意类型（或这些类型的集合）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-747">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="8a65f-748">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-748">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="8a65f-749">Entity Type — 实体类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-749">entity type</span></span> <br/> <span data-ttu-id="8a65f-750">Complex Type — 复杂类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-750">complex type</span></span> <br/> <span data-ttu-id="8a65f-751">行类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-751">row type</span></span> <br/> <span data-ttu-id="8a65f-752">Reference Type — 引用类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-752">reference type</span></span>                             |
| <span data-ttu-id="8a65f-753">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="8a65f-753">**Nullable**</span></span>     | <span data-ttu-id="8a65f-754">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-754">No</span></span>          | <span data-ttu-id="8a65f-755">**True** （默认值）或**False** ，具体取决于属性是否可以具有**null**值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-755">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="8a65f-756">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="8a65f-756">**DefaultValue**</span></span> | <span data-ttu-id="8a65f-757">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-757">No</span></span>          | <span data-ttu-id="8a65f-758">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-758">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="8a65f-759">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-759">**MaxLength**</span></span>    | <span data-ttu-id="8a65f-760">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-760">No</span></span>          | <span data-ttu-id="8a65f-761">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-761">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="8a65f-762">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-762">**FixedLength**</span></span>  | <span data-ttu-id="8a65f-763">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-763">No</span></span>          | <span data-ttu-id="8a65f-764">**True**或**False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="8a65f-764">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="8a65f-765">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-765">**Precision**</span></span>    | <span data-ttu-id="8a65f-766">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-766">No</span></span>          | <span data-ttu-id="8a65f-767">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-767">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="8a65f-768">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-768">**Scale**</span></span>        | <span data-ttu-id="8a65f-769">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-769">No</span></span>          | <span data-ttu-id="8a65f-770">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-770">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="8a65f-771">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-771">**SRID**</span></span>         | <span data-ttu-id="8a65f-772">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-772">No</span></span>          | <span data-ttu-id="8a65f-773">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-773">Spatial System Reference Identifier.</span></span> <span data-ttu-id="8a65f-774">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="8a65f-774">Valid only for properties of spatial types.</span></span> <span data-ttu-id="8a65f-775">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-775">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="8a65f-776">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-776">**Unicode**</span></span>      | <span data-ttu-id="8a65f-777">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-777">No</span></span>          | <span data-ttu-id="8a65f-778">**True**或**False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-778">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="8a65f-779">**归类**</span><span class="sxs-lookup"><span data-stu-id="8a65f-779">**Collation**</span></span>    | <span data-ttu-id="8a65f-780">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-780">No</span></span>          | <span data-ttu-id="8a65f-781">指定要在数据源中使用的排序序列的字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-781">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="8a65f-782">可以将任意数量的批注特性（自定义 XML 特性）应用于**参数**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-782">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="8a65f-783">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-783">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-784">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-784">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="8a65f-785">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-785">Example</span></span>

<span data-ttu-id="8a65f-786">下面的示例演示一个使用一个**参数**子元素定义函数参数的**函数**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-786">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="8a65f-787">Principal 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-787">Principal Element (CSDL)</span></span>

<span data-ttu-id="8a65f-788">概念架构定义语言（CSDL）中的**主体**元素是 ReferentialConstraint 元素的子元素，用于定义引用约束的主体端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-788">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="8a65f-789">**ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。</span><span class="sxs-lookup"><span data-stu-id="8a65f-789">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="8a65f-790">与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-790">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="8a65f-791">引用的实体类型称为约束的*主体端*。</span><span class="sxs-lookup"><span data-stu-id="8a65f-791">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="8a65f-792">引用主体端的实体类型称为约束的*依赖端*。</span><span class="sxs-lookup"><span data-stu-id="8a65f-792">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="8a65f-793">**PropertyRef**元素用于指定依赖端引用的键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-793">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="8a65f-794">**主体**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-794">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-795">PropertyRef （一个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-795">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="8a65f-796">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-796">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-797">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-797">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-798">下表描述了可应用于**主体**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-798">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="8a65f-799">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-799">Attribute Name</span></span> | <span data-ttu-id="8a65f-800">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-800">Is Required</span></span> | <span data-ttu-id="8a65f-801">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-801">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="8a65f-802">**角色**</span><span class="sxs-lookup"><span data-stu-id="8a65f-802">**Role**</span></span>       | <span data-ttu-id="8a65f-803">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-803">Yes</span></span>         | <span data-ttu-id="8a65f-804">关联的主体端的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-804">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-805">可以将任意数量的批注特性（自定义 XML 特性）应用于**主体**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-805">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="8a65f-806">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-806">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-807">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-807">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-808">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-808">Example</span></span>

<span data-ttu-id="8a65f-809">下面的示例显示了**ReferentialConstraint**元素，它是**PublishedBy**关联定义的一部分。</span><span class="sxs-lookup"><span data-stu-id="8a65f-809">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="8a65f-810">**发行者**实体类型的**Id**属性构成了引用约束的主体端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-810">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="8a65f-811">Property 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-811">Property Element (CSDL)</span></span>

<span data-ttu-id="8a65f-812">概念架构定义语言（CSDL）中的**属性**元素可以是 EntityType 元素、ComplexType 元素或 RowType 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-812">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="8a65f-813">EntityType 和 ComplexType 元素应用程序</span><span class="sxs-lookup"><span data-stu-id="8a65f-813">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="8a65f-814">**属性**元素（作为**EntityType**或**ComplexType**元素的子元素）定义实体类型实例或复杂类型实例将包含的数据的形状和特征。</span><span class="sxs-lookup"><span data-stu-id="8a65f-814">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="8a65f-815">概念模型中的属性类似于为类定义的属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-815">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="8a65f-816">正如类的属性定义类的形状和携带有关对象的信息一样，概念模型中的属性也定义实体类型的形状和携带有关实体类型实例的信息。</span><span class="sxs-lookup"><span data-stu-id="8a65f-816">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="8a65f-817">**Property**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-817">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-818">文档元素（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-818">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="8a65f-819">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-819">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="8a65f-820">以下方面可应用于**属性**元素：可以**为 null**、**默认**值、 **MaxLength**、 **FixedLength**、**精度**、**小数位数**、 **Unicode**、**排序规则**、 **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-820">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="8a65f-821">方面是一些 XML 特性，它们提供有关如何在数据存储区中存储属性值的信息。</span><span class="sxs-lookup"><span data-stu-id="8a65f-821">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-822">Facet 只能应用于**EDMSimpleType**类型的属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-822">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-823">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-823">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-824">下表介绍可应用于**属性**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-824">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="8a65f-825">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-825">Attribute Name</span></span>                                                         | <span data-ttu-id="8a65f-826">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-826">Is Required</span></span> | <span data-ttu-id="8a65f-827">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-827">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-828">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-828">**Name**</span></span>                                                               | <span data-ttu-id="8a65f-829">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-829">Yes</span></span>         | <span data-ttu-id="8a65f-830">属性的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-830">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="8a65f-831">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-831">**Type**</span></span>                                                               | <span data-ttu-id="8a65f-832">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-832">Yes</span></span>         | <span data-ttu-id="8a65f-833">属性值的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-833">The type of the property value.</span></span> <span data-ttu-id="8a65f-834">属性值类型必须为模型范围内的**EDMSimpleType**或复杂类型（由完全限定的名称指示）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-834">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="8a65f-835">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="8a65f-835">**Nullable**</span></span>                                                           | <span data-ttu-id="8a65f-836">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-836">No</span></span>          | <span data-ttu-id="8a65f-837">**True** （默认值）或<strong>False</strong> ，具体取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-837">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="8a65f-838">CSDL v1 中的 >，复杂类型属性必须具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="8a65f-838">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="8a65f-839">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="8a65f-839">**DefaultValue**</span></span>                                                       | <span data-ttu-id="8a65f-840">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-840">No</span></span>          | <span data-ttu-id="8a65f-841">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-841">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="8a65f-842">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-842">**MaxLength**</span></span>                                                          | <span data-ttu-id="8a65f-843">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-843">No</span></span>          | <span data-ttu-id="8a65f-844">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-844">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="8a65f-845">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-845">**FixedLength**</span></span>                                                        | <span data-ttu-id="8a65f-846">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-846">No</span></span>          | <span data-ttu-id="8a65f-847">**True**或**False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="8a65f-847">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="8a65f-848">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-848">**Precision**</span></span>                                                          | <span data-ttu-id="8a65f-849">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-849">No</span></span>          | <span data-ttu-id="8a65f-850">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-850">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="8a65f-851">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-851">**Scale**</span></span>                                                              | <span data-ttu-id="8a65f-852">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-852">No</span></span>          | <span data-ttu-id="8a65f-853">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-853">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="8a65f-854">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-854">**SRID**</span></span>                                                               | <span data-ttu-id="8a65f-855">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-855">No</span></span>          | <span data-ttu-id="8a65f-856">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-856">Spatial System Reference Identifier.</span></span> <span data-ttu-id="8a65f-857">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="8a65f-857">Valid only for properties of spatial types.</span></span> <span data-ttu-id="8a65f-858">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-858">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="8a65f-859">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-859">**Unicode**</span></span>                                                            | <span data-ttu-id="8a65f-860">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-860">No</span></span>          | <span data-ttu-id="8a65f-861">**True**或**False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-861">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="8a65f-862">**归类**</span><span class="sxs-lookup"><span data-stu-id="8a65f-862">**Collation**</span></span>                                                          | <span data-ttu-id="8a65f-863">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-863">No</span></span>          | <span data-ttu-id="8a65f-864">指定要在数据源中使用的排序序列的字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-864">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="8a65f-865">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-865">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="8a65f-866">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-866">No</span></span>          | <span data-ttu-id="8a65f-867">**None** （默认值）或**Fixed**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-867">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="8a65f-868">如果将该值设置为**Fixed**，则属性值将用于开放式并发检查。</span><span class="sxs-lookup"><span data-stu-id="8a65f-868">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="8a65f-869">可以将任意数量的批注特性（自定义 XML 特性）应用于**Property**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-869">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="8a65f-870">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-870">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-871">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-871">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="8a65f-872">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-872">Example</span></span>

<span data-ttu-id="8a65f-873">下面的示例演示一个具有三个**属性**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-873">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="8a65f-874">下面的示例演示一个包含五个**属性**元素的**ComplexType**元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-874">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="8a65f-875">RowType 元素应用</span><span class="sxs-lookup"><span data-stu-id="8a65f-875">RowType Element Application</span></span>

<span data-ttu-id="8a65f-876">**Property**元素（作为**RowType**元素的子元素）定义可传递到模型定义函数或从模型定义函数返回的数据的形状和特征。</span><span class="sxs-lookup"><span data-stu-id="8a65f-876">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="8a65f-877">**Property**元素可以具有以下子元素之一：</span><span class="sxs-lookup"><span data-stu-id="8a65f-877">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="8a65f-878">CollectionType</span><span class="sxs-lookup"><span data-stu-id="8a65f-878">CollectionType</span></span>
-   <span data-ttu-id="8a65f-879">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="8a65f-879">ReferenceType</span></span>
-   <span data-ttu-id="8a65f-880">RowType</span><span class="sxs-lookup"><span data-stu-id="8a65f-880">RowType</span></span>

<span data-ttu-id="8a65f-881">**Property**元素可以有任意数量的子批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-881">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-882">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-882">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-883">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-883">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-884">下表介绍可应用于**属性**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-884">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="8a65f-885">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-885">Attribute Name</span></span>                                                     | <span data-ttu-id="8a65f-886">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-886">Is Required</span></span> | <span data-ttu-id="8a65f-887">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-887">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-888">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-888">**Name**</span></span>                                                           | <span data-ttu-id="8a65f-889">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-889">Yes</span></span>         | <span data-ttu-id="8a65f-890">属性的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-890">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="8a65f-891">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-891">**Type**</span></span>                                                           | <span data-ttu-id="8a65f-892">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-892">Yes</span></span>         | <span data-ttu-id="8a65f-893">属性值的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-893">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="8a65f-894">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="8a65f-894">**Nullable**</span></span>                                                       | <span data-ttu-id="8a65f-895">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-895">No</span></span>          | <span data-ttu-id="8a65f-896">**True** （默认值）或**False** ，具体取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-896">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="8a65f-897">CSDL v1 中的 > 复杂类型属性必须具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="8a65f-897">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="8a65f-898">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="8a65f-898">**DefaultValue**</span></span>                                                   | <span data-ttu-id="8a65f-899">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-899">No</span></span>          | <span data-ttu-id="8a65f-900">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-900">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="8a65f-901">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-901">**MaxLength**</span></span>                                                      | <span data-ttu-id="8a65f-902">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-902">No</span></span>          | <span data-ttu-id="8a65f-903">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-903">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="8a65f-904">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-904">**FixedLength**</span></span>                                                    | <span data-ttu-id="8a65f-905">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-905">No</span></span>          | <span data-ttu-id="8a65f-906">**True**或**False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="8a65f-906">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="8a65f-907">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-907">**Precision**</span></span>                                                      | <span data-ttu-id="8a65f-908">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-908">No</span></span>          | <span data-ttu-id="8a65f-909">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-909">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="8a65f-910">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-910">**Scale**</span></span>                                                          | <span data-ttu-id="8a65f-911">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-911">No</span></span>          | <span data-ttu-id="8a65f-912">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-912">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="8a65f-913">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-913">**SRID**</span></span>                                                           | <span data-ttu-id="8a65f-914">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-914">No</span></span>          | <span data-ttu-id="8a65f-915">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-915">Spatial System Reference Identifier.</span></span> <span data-ttu-id="8a65f-916">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="8a65f-916">Valid only for properties of spatial types.</span></span> <span data-ttu-id="8a65f-917">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-917">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="8a65f-918">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-918">**Unicode**</span></span>                                                        | <span data-ttu-id="8a65f-919">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-919">No</span></span>          | <span data-ttu-id="8a65f-920">**True**或**False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-920">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="8a65f-921">**归类**</span><span class="sxs-lookup"><span data-stu-id="8a65f-921">**Collation**</span></span>                                                      | <span data-ttu-id="8a65f-922">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-922">No</span></span>          | <span data-ttu-id="8a65f-923">指定要在数据源中使用的排序序列的字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-923">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="8a65f-924">可以将任意数量的批注特性（自定义 XML 特性）应用于**Property**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-924">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="8a65f-925">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-925">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-926">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-926">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="8a65f-927">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-927">Example</span></span>

<span data-ttu-id="8a65f-928">下面的示例演示了用于定义模型定义函数的返回类型的形状的**属性**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-928">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="8a65f-929">PropertyRef 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-929">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="8a65f-930">概念架构定义语言（CSDL）中的**PropertyRef**元素引用实体类型的属性，以指示该属性将执行以下角色之一：</span><span class="sxs-lookup"><span data-stu-id="8a65f-930">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="8a65f-931">实体键的一部分（实体类型的用于确定标识的一个或一组属性）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-931">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="8a65f-932">一个或多个**PropertyRef**元素可用于定义实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-932">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="8a65f-933">引用约束的依赖端或主体端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-933">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="8a65f-934">**PropertyRef**元素只能将批注元素（零个或多个）作为子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-934">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-935">仅在 CSDL v2 和更高版本中允许使用批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-935">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-936">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-936">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-937">下表介绍可应用于**PropertyRef**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-937">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="8a65f-938">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-938">Attribute Name</span></span> | <span data-ttu-id="8a65f-939">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-939">Is Required</span></span> | <span data-ttu-id="8a65f-940">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-940">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="8a65f-941">**Name**</span><span class="sxs-lookup"><span data-stu-id="8a65f-941">**Name**</span></span>       | <span data-ttu-id="8a65f-942">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-942">Yes</span></span>         | <span data-ttu-id="8a65f-943">所引用属性的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-943">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-944">可以将任意数量的批注特性（自定义 XML 特性）应用于**PropertyRef**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-944">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="8a65f-945">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-945">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-946">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-946">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-947">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-947">Example</span></span>

<span data-ttu-id="8a65f-948">下面的示例定义了一个实体类型（**Book**）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-948">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="8a65f-949">实体键通过引用实体类型的**ISBN**属性来定义。</span><span class="sxs-lookup"><span data-stu-id="8a65f-949">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="8a65f-950">在下一个示例中，两个**PropertyRef**元素用于指示两个属性（**Id**和**PublisherId**）是引用约束的主体端和依赖端。</span><span class="sxs-lookup"><span data-stu-id="8a65f-950">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="8a65f-951">ReferenceType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-951">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="8a65f-952">概念架构定义语言（CSDL）中的**ReferenceType**元素指定对实体类型的引用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-952">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="8a65f-953">**ReferenceType**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-953">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="8a65f-954">ReturnType （函数）</span><span class="sxs-lookup"><span data-stu-id="8a65f-954">ReturnType (Function)</span></span>
-   <span data-ttu-id="8a65f-955">参数</span><span class="sxs-lookup"><span data-stu-id="8a65f-955">Parameter</span></span>
-   <span data-ttu-id="8a65f-956">CollectionType</span><span class="sxs-lookup"><span data-stu-id="8a65f-956">CollectionType</span></span>

<span data-ttu-id="8a65f-957">在定义函数的参数或返回类型时，将使用**ReferenceType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-957">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="8a65f-958">**ReferenceType**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-958">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-959">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-959">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-960">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-960">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-961">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-961">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-962">下表介绍可应用于**ReferenceType**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-962">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="8a65f-963">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-963">Attribute Name</span></span> | <span data-ttu-id="8a65f-964">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-964">Is Required</span></span> | <span data-ttu-id="8a65f-965">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-965">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="8a65f-966">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-966">**Type**</span></span>       | <span data-ttu-id="8a65f-967">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-967">Yes</span></span>         | <span data-ttu-id="8a65f-968">所引用的实体类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-968">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-969">可以将任意数量的批注特性（自定义 XML 特性）应用于**ReferenceType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-969">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="8a65f-970">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-970">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-971">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-971">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-972">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-972">Example</span></span>

<span data-ttu-id="8a65f-973">下面的示例显示了**ReferenceType**元素，该元素在接受对**Person**实体类型的引用的模型定义函数中用作**Parameter**元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-973">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="8a65f-974">下面的示例显示了**ReferenceType**元素，该元素用作模型定义的函数中的**ReturnType** （函数）元素的子元素，该函数返回对**Person**实体类型的引用：</span><span class="sxs-lookup"><span data-stu-id="8a65f-974">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="8a65f-975">ReferentialConstraint 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-975">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="8a65f-976">概念架构定义语言（CSDL）中的**ReferentialConstraint**元素定义的功能与关系数据库中的引用完整性约束类似。</span><span class="sxs-lookup"><span data-stu-id="8a65f-976">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="8a65f-977">与数据库表中的一个（或多个）列可以引用另一个表的主键相同，实体类型的一个（或多个）属性可以引用另一个实体类型的实体键。</span><span class="sxs-lookup"><span data-stu-id="8a65f-977">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="8a65f-978">引用的实体类型称为约束的*主体端*。</span><span class="sxs-lookup"><span data-stu-id="8a65f-978">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="8a65f-979">引用主体端的实体类型称为约束的*依赖端*。</span><span class="sxs-lookup"><span data-stu-id="8a65f-979">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="8a65f-980">如果一个实体类型上公开的外键引用另一个实体类型上的属性，则**ReferentialConstraint**元素将定义这两个实体类型之间的关联。</span><span class="sxs-lookup"><span data-stu-id="8a65f-980">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="8a65f-981">由于**ReferentialConstraint**元素提供有关两个实体类型之间的关系的信息，因此，在映射规范语言（MSL）中不需要对应的 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-981">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="8a65f-982">没有公开外键的两个实体类型之间的关联必须具有相应的**AssociationSetMapping**元素，才能将关联信息映射到数据源。</span><span class="sxs-lookup"><span data-stu-id="8a65f-982">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="8a65f-983">如果在实体类型上没有公开外键，则**ReferentialConstraint**元素只能定义实体类型和其他实体类型之间的主键到主键约束。</span><span class="sxs-lookup"><span data-stu-id="8a65f-983">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="8a65f-984">**ReferentialConstraint**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-984">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-985">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-985">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-986">Principal （恰好一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-986">Principal (exactly one element)</span></span>
-   <span data-ttu-id="8a65f-987">Dependent（恰好一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-987">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="8a65f-988">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-988">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-989">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-989">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-990">**ReferentialConstraint**元素可以具有任意数量的批注特性（自定义 XML 特性）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-990">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="8a65f-991">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-991">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-992">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-992">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-993">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-993">Example</span></span>

<span data-ttu-id="8a65f-994">下面的示例演示了在**PublishedBy**关联的定义中使用的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-994">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="8a65f-995">ReturnType （Function）元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="8a65f-995">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="8a65f-996">概念架构定义语言（CSDL）中的**ReturnType** （函数）元素指定在 function 元素中定义的函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-996">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="8a65f-997">还可以使用**ReturnType**特性指定函数返回类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-997">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="8a65f-998">返回类型可以是任何**EdmSimpleType**、实体类型、复杂类型、行类型、引用类型或这些类型之一的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-998">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="8a65f-999">函数的返回类型可以使用**ReturnType** （function）元素的**type**属性指定，也可以使用以下子元素之一指定：</span><span class="sxs-lookup"><span data-stu-id="8a65f-999">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="8a65f-1000">CollectionType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1000">CollectionType</span></span>
-   <span data-ttu-id="8a65f-1001">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1001">ReferenceType</span></span>
-   <span data-ttu-id="8a65f-1002">RowType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1002">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-1003">如果指定的函数返回类型同时具有**ReturnType** （函数）元素的**type**属性和一个子元素，模型将不会验证。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1003">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-1004">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1004">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-1005">下表介绍可应用于**ReturnType** （函数）元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1005">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="8a65f-1006">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-1006">Attribute Name</span></span> | <span data-ttu-id="8a65f-1007">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-1007">Is Required</span></span> | <span data-ttu-id="8a65f-1008">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-1008">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="8a65f-1009">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1009">**ReturnType**</span></span> | <span data-ttu-id="8a65f-1010">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1010">No</span></span>          | <span data-ttu-id="8a65f-1011">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1011">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-1012">可以将任意数量的批注特性（自定义 XML 特性）应用于**ReturnType** （Function）元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1012">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="8a65f-1013">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1013">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1014">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1014">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-1015">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1015">Example</span></span>

<span data-ttu-id="8a65f-1016">下面的示例使用**function**元素来定义一个函数，该函数返回书籍已打印的年数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1016">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="8a65f-1017">请注意，返回类型由**ReturnType** （函数）元素的**type**属性指定。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1017">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="8a65f-1018">ReturnType （FunctionImport）元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1018">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="8a65f-1019">概念架构定义语言（CSDL）中的**ReturnType** （FunctionImport）元素指定在 FunctionImport 元素中定义的函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1019">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="8a65f-1020">还可以使用**ReturnType**特性指定函数返回类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1020">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="8a65f-1021">返回类型可以是实体类型、复杂类型或**EdmSimpleType**的任何集合，</span><span class="sxs-lookup"><span data-stu-id="8a65f-1021">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="8a65f-1022">函数的返回类型是使用**ReturnType** （FunctionImport）元素的**type**属性指定的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1022">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-1023">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1023">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-1024">下表介绍可应用于**ReturnType** （FunctionImport）元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1024">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="8a65f-1025">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-1025">Attribute Name</span></span> | <span data-ttu-id="8a65f-1026">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-1026">Is Required</span></span> | <span data-ttu-id="8a65f-1027">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-1027">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-1028">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-1028">**Type**</span></span>       | <span data-ttu-id="8a65f-1029">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1029">No</span></span>          | <span data-ttu-id="8a65f-1030">函数返回的类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1030">The type that the function returns.</span></span> <span data-ttu-id="8a65f-1031">该值必须是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1031">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="8a65f-1032">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1032">**EntitySet**</span></span>  | <span data-ttu-id="8a65f-1033">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1033">No</span></span>          | <span data-ttu-id="8a65f-1034">如果函数返回实体类型的集合，则**EntitySet**的值必须是该集合所属的实体集。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1034">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="8a65f-1035">否则，不能使用**EntitySet**特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1035">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-1036">可以将任意数量的批注特性（自定义 XML 特性）应用于**ReturnType** （FunctionImport）元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1036">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="8a65f-1037">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1037">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1038">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1038">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-1039">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1039">Example</span></span>

<span data-ttu-id="8a65f-1040">下面的示例使用返回书籍和出版商的**FunctionImport** 。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1040">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="8a65f-1041">请注意，函数将返回两个结果集，因而指定了两个**ReturnType** （FunctionImport）元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1041">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="8a65f-1042">RowType 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1042">RowType Element (CSDL)</span></span>

<span data-ttu-id="8a65f-1043">概念架构定义语言（CSDL）中的**RowType**元素将未命名的结构定义为概念模型中定义的函数的参数或返回类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1043">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="8a65f-1044">**RowType**元素可以是以下元素的子元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1044">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="8a65f-1045">CollectionType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1045">CollectionType</span></span>
-   <span data-ttu-id="8a65f-1046">参数</span><span class="sxs-lookup"><span data-stu-id="8a65f-1046">Parameter</span></span>
-   <span data-ttu-id="8a65f-1047">ReturnType （函数）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1047">ReturnType (Function)</span></span>

<span data-ttu-id="8a65f-1048">**RowType**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1048">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-1049">Property（一个或多个）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1049">Property (one or more)</span></span>
-   <span data-ttu-id="8a65f-1050">批注元素（零个或多个）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1050">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-1051">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1051">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-1052">可以将任意数量的批注特性（自定义 XML 特性）应用于**RowType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1052">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="8a65f-1053">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1053">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1054">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1054">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-1055">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1055">Example</span></span>

<span data-ttu-id="8a65f-1056">下面的示例演示一个模型定义函数，该函数使用**CollectionType**元素来指定该函数返回行的集合（在**RowType**元素中指定）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1056">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="8a65f-1057">Schema 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1057">Schema Element (CSDL)</span></span>

<span data-ttu-id="8a65f-1058">**Schema**元素是概念模型定义的根元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1058">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="8a65f-1059">它包括构成概念模型的对象、函数和容器的定义。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1059">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="8a65f-1060">**Schema**元素可包含零个或多个下列子元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1060">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="8a65f-1061">Using</span><span class="sxs-lookup"><span data-stu-id="8a65f-1061">Using</span></span>
-   <span data-ttu-id="8a65f-1062">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="8a65f-1062">EntityContainer</span></span>
-   <span data-ttu-id="8a65f-1063">EntityType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1063">EntityType</span></span>
-   <span data-ttu-id="8a65f-1064">EnumType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1064">EnumType</span></span>
-   <span data-ttu-id="8a65f-1065">关联</span><span class="sxs-lookup"><span data-stu-id="8a65f-1065">Association</span></span>
-   <span data-ttu-id="8a65f-1066">ComplexType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1066">ComplexType</span></span>
-   <span data-ttu-id="8a65f-1067">函数</span><span class="sxs-lookup"><span data-stu-id="8a65f-1067">Function</span></span>

<span data-ttu-id="8a65f-1068">一个**Schema**元素可包含零个或一个批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1068">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-1069">仅在 CSDL v2 和更高版本中允许**Function**元素和 annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1069">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="8a65f-1070">**Schema**元素使用**namespace**特性为概念模型中的实体类型、复杂类型和关联对象定义命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1070">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="8a65f-1071">在命名空间内，任何两个对象都不能同名。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1071">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="8a65f-1072">命名空间可以跨多个**架构**元素和多个 csdl 文件。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1072">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="8a65f-1073">概念模型命名空间与**Schema**元素的 XML 命名空间不同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1073">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="8a65f-1074">概念模型命名空间（由**命名空间**特性定义）是实体类型、复杂类型和关联类型的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1074">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="8a65f-1075">**Schema**元素的 XML 命名空间（由**xmlns**特性指示）是**schema**元素的子元素和属性的默认命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1075">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="8a65f-1076">窗体的 XML 命名空间 https://schemas.microsoft.com/ado/YYYY/MM/edm （其中 YYYY 和 MM 表示年和月分别） 是为 CSDL 保留。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1076">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1077">自定义元素和特性不能位于具有此格式的命名空间中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1077">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-1078">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1078">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-1079">下表描述了可对**Schema**元素应用的属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1079">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="8a65f-1080">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-1080">Attribute Name</span></span> | <span data-ttu-id="8a65f-1081">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-1081">Is Required</span></span> | <span data-ttu-id="8a65f-1082">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-1082">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-1083">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1083">**Namespace**</span></span>  | <span data-ttu-id="8a65f-1084">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1084">Yes</span></span>         | <span data-ttu-id="8a65f-1085">概念模型的命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1085">The namespace of the conceptual model.</span></span> <span data-ttu-id="8a65f-1086">**命名空间**属性的值用于构成类型的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1086">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="8a65f-1087">例如，如果名为*Customer*的**entitytype**位于 Simple 命名空间中，则**entitytype**的完全限定名称为 simpleexamplemodel.customer。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1087">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="8a65f-1088">以下字符串不能用作**Namespace**特性的值： **System**、**暂时性**或**Edm**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1088">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="8a65f-1089">**命名空间**属性的值不能与 SSDL 架构元素中**namespace**属性的值相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1089">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="8a65f-1090">**Alias**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1090">**Alias**</span></span>      | <span data-ttu-id="8a65f-1091">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1091">No</span></span>          | <span data-ttu-id="8a65f-1092">用于取代命名空间名称的标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1092">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="8a65f-1093">例如，如果名为*Customer*的**EntityType**位于 Simple 命名空间，并且**Alias**属性的值为*Model*，则可以使用 model 作为 EntityType 的完全限定名称 **。**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1093">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="8a65f-1094">可以将任意数量的批注特性（自定义 XML 特性）应用于**Schema**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1094">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="8a65f-1095">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1095">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1096">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1096">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-1097">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1097">Example</span></span>

<span data-ttu-id="8a65f-1098">下面的示例演示一个**架构**元素，该元素包含一个**EntityContainer**元素、两个**EntityType**元素和一个**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1098">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="8a65f-1099">TypeRef 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1099">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="8a65f-1100">概念架构定义语言（CSDL）中的**TypeRef**元素提供对现有命名类型的引用。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1100">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="8a65f-1101">**TypeRef**元素可以是 CollectionType 元素的子元素，用于指定函数具有集合作为参数或返回类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1101">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="8a65f-1102">**TypeRef**元素可以具有以下子元素（按所列顺序）：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1102">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8a65f-1103">文档（零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1103">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="8a65f-1104">批注元素（零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1104">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-1105">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1105">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-1106">下表介绍可应用于**TypeRef**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1106">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="8a65f-1107">请注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**小数位数**、 **Unicode**和**排序规则**特性仅适用于**edmsimpletype**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1107">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="8a65f-1108">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-1108">Attribute Name</span></span>                                                     | <span data-ttu-id="8a65f-1109">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-1109">Is Required</span></span> | <span data-ttu-id="8a65f-1110">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-1110">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-1111">类型</span><span class="sxs-lookup"><span data-stu-id="8a65f-1111">**Type**</span></span>                                                           | <span data-ttu-id="8a65f-1112">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1112">No</span></span>          | <span data-ttu-id="8a65f-1113">所引用的类型的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1113">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="8a65f-1114">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1114">**Nullable**</span></span>                                                       | <span data-ttu-id="8a65f-1115">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1115">No</span></span>          | <span data-ttu-id="8a65f-1116">**True** （默认值）或**False** ，具体取决于属性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1116">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="8a65f-1117">CSDL v1 中的 > 复杂类型属性必须具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1117">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="8a65f-1118">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1118">**DefaultValue**</span></span>                                                   | <span data-ttu-id="8a65f-1119">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1119">No</span></span>          | <span data-ttu-id="8a65f-1120">属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1120">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="8a65f-1121">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1121">**MaxLength**</span></span>                                                      | <span data-ttu-id="8a65f-1122">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1122">No</span></span>          | <span data-ttu-id="8a65f-1123">属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1123">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="8a65f-1124">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1124">**FixedLength**</span></span>                                                    | <span data-ttu-id="8a65f-1125">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1125">No</span></span>          | <span data-ttu-id="8a65f-1126">**True**或**False** ，具体取决于属性值是否将作为固定长度字符串存储。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1126">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="8a65f-1127">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1127">**Precision**</span></span>                                                      | <span data-ttu-id="8a65f-1128">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1128">No</span></span>          | <span data-ttu-id="8a65f-1129">属性值的精度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1129">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="8a65f-1130">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-1130">**Scale**</span></span>                                                          | <span data-ttu-id="8a65f-1131">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1131">No</span></span>          | <span data-ttu-id="8a65f-1132">属性值的刻度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1132">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="8a65f-1133">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1133">**SRID**</span></span>                                                           | <span data-ttu-id="8a65f-1134">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1134">No</span></span>          | <span data-ttu-id="8a65f-1135">空间系统引用标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1135">Spatial System Reference Identifier.</span></span> <span data-ttu-id="8a65f-1136">仅对空间类型的属性有效。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1136">Valid only for properties of spatial types.</span></span> <span data-ttu-id="8a65f-1137">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1137">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="8a65f-1138">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1138">**Unicode**</span></span>                                                        | <span data-ttu-id="8a65f-1139">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1139">No</span></span>          | <span data-ttu-id="8a65f-1140">**True**或**False** ，具体取决于属性值是否将存储为 Unicode 字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1140">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="8a65f-1141">**归类**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1141">**Collation**</span></span>                                                      | <span data-ttu-id="8a65f-1142">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1142">No</span></span>          | <span data-ttu-id="8a65f-1143">指定要在数据源中使用的排序序列的字符串。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1143">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="8a65f-1144">可以将任意数量的批注特性（自定义 XML 特性）应用于**CollectionType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1144">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="8a65f-1145">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1145">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1146">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1146">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-1147">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1147">Example</span></span>

<span data-ttu-id="8a65f-1148">下面的示例演示一个模型定义函数，该函数使用**TypeRef**元素（作为**CollectionType**元素的子元素）来指定该函数接受**部门**实体类型的集合。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1148">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="8a65f-1149">Using 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1149">Using Element (CSDL)</span></span>

<span data-ttu-id="8a65f-1150">概念性架构定义语言（CSDL）中的**Using**元素导入存在于其他命名空间中的概念模型的内容。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1150">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="8a65f-1151">通过设置**Namespace**属性的值，您可以引用在其他概念模型中定义的实体类型、复杂类型和关联类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1151">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="8a65f-1152">多个**Using**元素可以是**Schema**元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1152">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-1153">CSDL 中的**using**元素的作用与编程语言中的**using**语句完全相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1153">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="8a65f-1154">通过使用编程语言中的**using**语句导入命名空间，不会影响原始命名空间中的对象。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1154">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="8a65f-1155">在 CSDL 中，导入的命名空间可以包含从原始命名空间中的实体类型导出的实体类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1155">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="8a65f-1156">这可能影响在原始命名空间中声明的实体集。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1156">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="8a65f-1157">**Using**元素可以具有以下子元素：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1157">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="8a65f-1158">文档（允许零个或一个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1158">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="8a65f-1159">Annotation 元素（允许零个或多个元素）</span><span class="sxs-lookup"><span data-stu-id="8a65f-1159">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8a65f-1160">适用的特性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1160">Applicable Attributes</span></span>

<span data-ttu-id="8a65f-1161">下表描述了可应用于**Using**元素的特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1161">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="8a65f-1162">特性名</span><span class="sxs-lookup"><span data-stu-id="8a65f-1162">Attribute Name</span></span> | <span data-ttu-id="8a65f-1163">是否必需</span><span class="sxs-lookup"><span data-stu-id="8a65f-1163">Is Required</span></span> | <span data-ttu-id="8a65f-1164">值</span><span class="sxs-lookup"><span data-stu-id="8a65f-1164">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-1165">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1165">**Namespace**</span></span>  | <span data-ttu-id="8a65f-1166">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1166">Yes</span></span>         | <span data-ttu-id="8a65f-1167">导入的命名空间的名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1167">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="8a65f-1168">**Alias**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1168">**Alias**</span></span>      | <span data-ttu-id="8a65f-1169">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1169">Yes</span></span>         | <span data-ttu-id="8a65f-1170">用于取代命名空间名称的标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1170">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="8a65f-1171">尽管此特性是必需的，但并不要求使用它来取代命名空间以限定对象名称。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1171">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="8a65f-1172">可以将任意数量的批注特性（自定义 XML 特性）应用于**Using**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1172">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="8a65f-1173">然而，自定义特性可能不属于为 CSDL 保留的任何 XML 命名空间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="8a65f-1174">任何两个自定义特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="8a65f-1175">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1175">Example</span></span>

<span data-ttu-id="8a65f-1176">下面的示例演示用于导入在其他位置定义的命名空间的**Using**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1176">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="8a65f-1177">请注意，显示的**架构**元素的命名空间是 `BooksModel`的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1177">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="8a65f-1178">`Publisher`**EntityType**上的 `Address` 属性是在 `ExtendedBooksModel` 命名空间中定义的复杂类型（使用**Using**元素导入）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1178">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="8a65f-1179">Annotation 特性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1179">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="8a65f-1180">以概念架构定义语言 (CSDL) 表示的批注特性是概念模型中的自定义 XML 特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1180">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="8a65f-1181">除了具有有效的 XML 结构之外，还必须满足批注特性的以下各项条件：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1181">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="8a65f-1182">批注特性不能位于为 CSDL 保留的任何 XML 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1182">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="8a65f-1183">可以将多个批注特性应用于给定的 CSDL 元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1183">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="8a65f-1184">任何两个批注特性的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1184">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="8a65f-1185">可以使用批注特性提供有关概念模型中元素的额外元数据。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1185">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="8a65f-1186">可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1186">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-1187">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1187">Example</span></span>

<span data-ttu-id="8a65f-1188">下面的示例演示具有批注特性（**CustomAttribute**）的**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1188">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="8a65f-1189">本示例还演示了一个应用于实体类型元素的批注元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1189">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="8a65f-1190">下面的代码检索批注特性中的元数据并将其写入控制台：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1190">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="8a65f-1191">上面的代码假定 `School.csdl` 文件位于项目的输出目录中，且您已将下面的 `Imports` 和 `Using` 语句添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1191">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="8a65f-1192">Annotation 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1192">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="8a65f-1193">以概念架构定义语言 (CSDL) 表示的批注元素是概念模型中的自定义 XML 元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1193">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="8a65f-1194">除了具有有效的 XML 结构之外，还必须满足批注元素的以下各项条件：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1194">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="8a65f-1195">批注元素不能位于为 CSDL 保留的任何 XML 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1195">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="8a65f-1196">多个批注元素可能是某个给定 CSDL 元素的子元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1196">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="8a65f-1197">任何两个批注元素的完全限定名称都不能相同。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1197">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="8a65f-1198">批注元素必须出现在给定 CSDL 元素的所有其他子元素之后。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1198">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="8a65f-1199">可以使用批注元素提供有关概念模型中元素的额外元数据。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1199">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="8a65f-1200">从 .NET Framework 版本4开始，可以在运行时通过使用 System.web 命名空间中的类访问批注元素中包含的元数据。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1200">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-1201">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1201">Example</span></span>

<span data-ttu-id="8a65f-1202">下面的示例显示具有 annotation 元素（**CustomElement**）的**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1202">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="8a65f-1203">本示例还演示了一个应用于实体类型元素的批注特性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1203">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="8a65f-1204">下面的代码检索批注元素中的元数据并将其写入控制台：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1204">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="8a65f-1205">上面的代码假定 School.csdl 文件位于项目的输出目录中并且您已将下面的 `Imports` 和 `Using` 语句添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1205">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="8a65f-1206">概念模型类型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1206">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="8a65f-1207">概念性架构定义语言（CSDL）支持一组抽象基元数据类型（称为**edmsimpletype**），用于定义概念模型中的属性。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1207">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="8a65f-1208">**Edmsimpletype**是存储或承载环境中支持的基元数据类型的代理。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1208">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="8a65f-1209">下表列出了 CSDL 支持的基元数据类型。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1209">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="8a65f-1210">该表还列出了可应用于每个**EDMSimpleType**的分面。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1210">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="8a65f-1211">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="8a65f-1211">EDMSimpleType</span></span>                    | <span data-ttu-id="8a65f-1212">说明</span><span class="sxs-lookup"><span data-stu-id="8a65f-1212">Description</span></span>                                                | <span data-ttu-id="8a65f-1213">适用的方面</span><span class="sxs-lookup"><span data-stu-id="8a65f-1213">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="8a65f-1214">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1214">**Edm.Binary**</span></span>                   | <span data-ttu-id="8a65f-1215">包含二进制数据。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1215">Contains binary data.</span></span>                                      | <span data-ttu-id="8a65f-1216">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1216">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="8a65f-1217">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1217">**Edm.Boolean**</span></span>                  | <span data-ttu-id="8a65f-1218">包含值**true**或**false**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1218">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="8a65f-1219">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1219">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="8a65f-1220">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1220">**Edm.Byte**</span></span>                     | <span data-ttu-id="8a65f-1221">包含一个无符号的 8 位整数值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1221">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="8a65f-1222">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1222">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1223">**Edm. DateTime**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1223">**Edm.DateTime**</span></span>                 | <span data-ttu-id="8a65f-1224">表示日期和时间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1224">Represents a date and time.</span></span>                                | <span data-ttu-id="8a65f-1225">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1225">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1226">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1226">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="8a65f-1227">包含日期和时间，采用相对于 GMT 的偏移量（以分钟为单位）的形式。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1227">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="8a65f-1228">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1228">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1229">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1229">**Edm.Decimal**</span></span>                  | <span data-ttu-id="8a65f-1230">包含一个具有固定精度和小数位数的数值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1230">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="8a65f-1231">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1231">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1232">**Edm。 Double**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1232">**Edm.Double**</span></span>                   | <span data-ttu-id="8a65f-1233">包含具有15位精度的浮点数</span><span class="sxs-lookup"><span data-stu-id="8a65f-1233">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="8a65f-1234">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1234">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1235">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1235">**Edm.Float**</span></span>                    | <span data-ttu-id="8a65f-1236">包含一个具有 7 位精度的浮点数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1236">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="8a65f-1237">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1237">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1238">**Edm Guid**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1238">**Edm.Guid**</span></span>                     | <span data-ttu-id="8a65f-1239">包含一个 16 字节的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1239">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="8a65f-1240">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1240">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1241">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1241">**Edm.Int16**</span></span>                    | <span data-ttu-id="8a65f-1242">包含一个带符号的 16 位整数值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1242">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="8a65f-1243">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1243">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1244">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1244">**Edm.Int32**</span></span>                    | <span data-ttu-id="8a65f-1245">包含一个带符号的 32 位整数值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1245">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="8a65f-1246">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1246">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1247">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1247">**Edm.Int64**</span></span>                    | <span data-ttu-id="8a65f-1248">包含一个带符号的 64 位整数值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1248">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="8a65f-1249">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1249">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1250">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1250">**Edm.SByte**</span></span>                    | <span data-ttu-id="8a65f-1251">包含一个带符号的 8 位整数值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1251">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="8a65f-1252">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1252">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1253">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1253">**Edm.String**</span></span>                   | <span data-ttu-id="8a65f-1254">包含字符数据。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1254">Contains character data.</span></span>                                   | <span data-ttu-id="8a65f-1255">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1255">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="8a65f-1256">**Edm。时间**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1256">**Edm.Time**</span></span>                     | <span data-ttu-id="8a65f-1257">包含当天的时间。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1257">Contains a time of day.</span></span>                                    | <span data-ttu-id="8a65f-1258">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="8a65f-1258">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="8a65f-1259">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1259">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="8a65f-1260">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1260">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1261">**Edm. GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1261">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="8a65f-1262">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1262">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1263">**Edm. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1263">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="8a65f-1264">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1264">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1265">**Edm. GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1265">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="8a65f-1266">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1266">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1267">**Edm. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1267">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="8a65f-1268">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1268">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1269">**Edm. GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1269">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="8a65f-1270">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1270">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1271">**Edm. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1271">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="8a65f-1272">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1272">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1273">**Edm. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1273">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="8a65f-1274">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1274">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1275">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1275">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="8a65f-1276">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1276">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1277">**Edm. GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1277">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="8a65f-1278">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1278">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1279">**Edm. GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1279">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="8a65f-1280">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1280">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1281">**Edm. GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1281">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="8a65f-1282">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1282">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1283">**Edm. GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1283">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="8a65f-1284">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1284">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1285">**Edm. GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1285">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="8a65f-1286">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1286">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1287">**Edm. GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1287">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="8a65f-1288">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1288">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="8a65f-1289">**Edm. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1289">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="8a65f-1290">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="8a65f-1290">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="8a65f-1291">方面 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="8a65f-1291">Facets (CSDL)</span></span>

<span data-ttu-id="8a65f-1292">以概念架构定义语言 (CSDL) 表示的方面表示对于实体类型和复杂类型的属性的约束。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1292">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="8a65f-1293">方面作为 XML 特性出现在以下 CSDL 元素上：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1293">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="8a65f-1294">属性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1294">Property</span></span>
-   <span data-ttu-id="8a65f-1295">TypeRef</span><span class="sxs-lookup"><span data-stu-id="8a65f-1295">TypeRef</span></span>
-   <span data-ttu-id="8a65f-1296">参数</span><span class="sxs-lookup"><span data-stu-id="8a65f-1296">Parameter</span></span>

<span data-ttu-id="8a65f-1297">下表描述了 CSDL 中支持的方面。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1297">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="8a65f-1298">所有方面都是可选的。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1298">All facets are optional.</span></span> <span data-ttu-id="8a65f-1299">从概念模型生成数据库时，实体框架将使用下面列出的一些方面。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1299">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="8a65f-1300">有关概念模型中的数据类型的信息，请参阅概念模型类型（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1300">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="8a65f-1301">方面</span><span class="sxs-lookup"><span data-stu-id="8a65f-1301">Facet</span></span>               | <span data-ttu-id="8a65f-1302">说明</span><span class="sxs-lookup"><span data-stu-id="8a65f-1302">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="8a65f-1303">适用于</span><span class="sxs-lookup"><span data-stu-id="8a65f-1303">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="8a65f-1304">用于生成数据库</span><span class="sxs-lookup"><span data-stu-id="8a65f-1304">Used for the database generation</span></span> | <span data-ttu-id="8a65f-1305">由运行时使用</span><span class="sxs-lookup"><span data-stu-id="8a65f-1305">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="8a65f-1306">**归类**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1306">**Collation**</span></span>       | <span data-ttu-id="8a65f-1307">指定在对属性值执行比较和排序操作时要使用的排序序列。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1307">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="8a65f-1308">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1308">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="8a65f-1309">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1309">Yes</span></span>                              | <span data-ttu-id="8a65f-1310">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1310">No</span></span>                  |
| <span data-ttu-id="8a65f-1311">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1311">**ConcurrencyMode**</span></span> | <span data-ttu-id="8a65f-1312">表示应使用属性的值来进行开放式并发检查。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1312">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="8a65f-1313">所有**EDMSimpleType**属性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1313">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="8a65f-1314">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1314">No</span></span>                               | <span data-ttu-id="8a65f-1315">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1315">Yes</span></span>                 |
| <span data-ttu-id="8a65f-1316">**Default**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1316">**Default**</span></span>         | <span data-ttu-id="8a65f-1317">如果在安装时未提供值，则指定属性的默认值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1317">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="8a65f-1318">所有**EDMSimpleType**属性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1318">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="8a65f-1319">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1319">Yes</span></span>                              | <span data-ttu-id="8a65f-1320">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1320">Yes</span></span>                 |
| <span data-ttu-id="8a65f-1321">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1321">**FixedLength**</span></span>     | <span data-ttu-id="8a65f-1322">指定属性值的长度是否可变。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1322">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="8a65f-1323">**Edm**、 **edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1323">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="8a65f-1324">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1324">Yes</span></span>                              | <span data-ttu-id="8a65f-1325">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1325">No</span></span>                  |
| <span data-ttu-id="8a65f-1326">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1326">**MaxLength**</span></span>       | <span data-ttu-id="8a65f-1327">指定属性值的最大长度。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1327">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="8a65f-1328">**Edm**、 **edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1328">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="8a65f-1329">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1329">Yes</span></span>                              | <span data-ttu-id="8a65f-1330">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1330">No</span></span>                  |
| <span data-ttu-id="8a65f-1331">**可以为 null**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1331">**Nullable**</span></span>        | <span data-ttu-id="8a65f-1332">指定属性是否可以具有**null**值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1332">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="8a65f-1333">所有**EDMSimpleType**属性</span><span class="sxs-lookup"><span data-stu-id="8a65f-1333">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="8a65f-1334">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1334">Yes</span></span>                              | <span data-ttu-id="8a65f-1335">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1335">Yes</span></span>                 |
| <span data-ttu-id="8a65f-1336">**精度**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1336">**Precision**</span></span>       | <span data-ttu-id="8a65f-1337">对于**Decimal**类型的属性，指定属性值可以具有的位数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1337">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="8a65f-1338">对于类型为**Time**、 **DateTime**和**DateTimeOffset**的属性，指定属性值的秒的小数部分的位数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1338">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="8a65f-1339">**Edm. DateTime**、 **edm**、 **edm**、 **edm 和 Time**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1339">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="8a65f-1340">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1340">Yes</span></span>                              | <span data-ttu-id="8a65f-1341">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1341">No</span></span>                  |
| <span data-ttu-id="8a65f-1342">缩放</span><span class="sxs-lookup"><span data-stu-id="8a65f-1342">**Scale**</span></span>           | <span data-ttu-id="8a65f-1343">指定属性值小数点右侧的位数。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1343">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="8a65f-1344">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1344">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="8a65f-1345">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1345">Yes</span></span>                              | <span data-ttu-id="8a65f-1346">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1346">No</span></span>                  |
| <span data-ttu-id="8a65f-1347">**SRID**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1347">**SRID**</span></span>            | <span data-ttu-id="8a65f-1348">指定空间系统引用系统 ID。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1348">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="8a65f-1349">有关详细信息，请参阅[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1349">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="8a65f-1350">**Edm、GeographyPoint、GeographyLineString、GeographyPolygon、、GeographyMultiPoint、、GeographyMultiLineString、GeographyMultiPolygon、edm、GeographyCollection、GeometryPoint、、、GeometryLineString、GeometryPolygon、GeometryMultiPoint、、GeometryMultiLineString、、GeometryMultiPolygon、GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1350">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="8a65f-1351">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1351">No</span></span>                               | <span data-ttu-id="8a65f-1352">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1352">Yes</span></span>                 |
| <span data-ttu-id="8a65f-1353">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1353">**Unicode**</span></span>         | <span data-ttu-id="8a65f-1354">指示是否将属性值存储为 Unicode。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1354">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="8a65f-1355">**Edm**</span><span class="sxs-lookup"><span data-stu-id="8a65f-1355">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="8a65f-1356">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1356">Yes</span></span>                              | <span data-ttu-id="8a65f-1357">是</span><span class="sxs-lookup"><span data-stu-id="8a65f-1357">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="8a65f-1358">当从概念模型生成数据库，生成数据库向导将识别的值**StoreGeneratedPattern**特性，可以在**属性**元素是否在下面的示例命名空间： https://schemas.microsoft.com/ado/2009/02/edm/annotation 。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1358">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="8a65f-1359">特性支持的值为**Identity**和**计算**。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1359">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="8a65f-1360">**标识**值将生成一个数据库列，其中包含在数据库中生成的标识值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1360">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="8a65f-1361">**计算得出**的值将生成一个列，该列具有在数据库中计算的值。</span><span class="sxs-lookup"><span data-stu-id="8a65f-1361">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="8a65f-1362">示例</span><span class="sxs-lookup"><span data-stu-id="8a65f-1362">Example</span></span>

<span data-ttu-id="8a65f-1363">下面的示例演示了应用于实体类型的属性的方面：</span><span class="sxs-lookup"><span data-stu-id="8a65f-1363">The following example shows facets applied to the properties of an entity type:</span></span>

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
